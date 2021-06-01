### ElementUI中使用cascader地址联级选择

> 在项目中，要求使用地址联级选择器，本来是一个不太难的需求，从github上找到中国区域数据，进行联级展示即可。但项目中要求地址可以多选，那么难点就出现了

首先看下成品图：

![](.\img\Element\el-cascader.png)

教程开始：

在ElementUI中，已经封装好联机选择器，也是就是`el-cascader`，我们直接拿过来用即可。

中国区域数据可以github走起，这里选择的是start数比较多又兼容ElementUI的`element-china-area-data`，根据自己的需求引入响应的数据即可。

这里项目中需要多选，所以数据中无需全部选项，所以直接引入`regionData`



**难点：如何设计向后端传输的数据结构？**

当我选择区级的时候，数据为`[{省:adcode,市:adcode,区:adcode}]`

当我选择市级下所有区的时候，数据为`[{省:adcode,市:adcode}]`

当我选择省级下所有市和区的时候，数据为`[{省:adcode}]`

也就是预期格式如下

```js
finishArr => [
    {"pr": "140000","ct": "140010","dt": "140011"},  //区
    {"pr": "150000","ct": "150100"},                 //市
    {"pr": "160000"},                                //省
]
```

但实际上当我们写好demo组件后，数据返给我们的是这个的。。。

```js
area => [['140000','140010','140011'],['140000','140010','140012'],...*100]
```

废话不多说，上代码！

```vue
<template>
  <div>
    <el-cascader
      v-model="selectVal"
      clearable
      collapse-tags
      :options="selectOptions"
      placeholder="请选择地区"
      :props="{ expandTrigger: 'hover', multiple: 'true' }"
      :show-all-levels="false"
      size="medium"
      @change="areaSelect"
    />
  </div>
</template>

<script>
  import { regionData } from 'element-china-area-data'

  export default {
    name: 'MainManage',
    data() {
      return {
        selectVal: [],
        selectOptions: regionData,
      }
    },
    mounted() {},
    methods: {
      // 选择了一个省级
      // 预期数据  areaArr = [{pr:140000}]
      // 选择了两个市级别
      // 预期数据  areaArr = [{pr:140000,ct:140010},{pr:140000,ct:140010}]
      // 选择了两个区级
      // 预期数据  areaArr = [{pr:140000,ct:140010,dt:140011},{pr:140000,ct:140010,dt:140012},]
      areaSelect(area) {
		/**
         area => [['140000','140010','140011'],['140000','140010','140012'],...*100]
         */
        // -------------------- 获取选择的区域根据相同市级进行分组区级别
        let areaArr = []
        for (let i = 0, l = area.length; i < l; i++) {
          // 如果不是第一条 && 与上一条相等，则把数据push到数组中
          if (i !== 0 && area[i][1] === area[i - 1][1]) {
            areaArr[areaArr.length - 1].push(area[i])
          } else {
            areaArr[areaArr.length] = [area[i]]
          }
        }
        /**
           * areaArr => [
               [[140000,140010,140011]],           //区
               [[150000,150010,150011],...*16],    //市
               [[160000,160010,160011],...*50],    //省
           ]
           *
           */
        // ------------------------ 对比并格式化市级下所有区的数据
        let cityArr = []
        for (let i = 0, l = areaArr.length; i < l; i++) {
          let item = areaArr[i]
          let length = item.length // 当前市中区得数量
          let province = item[0][0] // 每个数组得省的区域编码,因为编码相同，所以只取数组中第一个即可
          let city = item[0][1] // 每个数组得市的区域编码
          let cityItem = regionData
            .find((pItem) => {
              return pItem.value === province
            })
            .children.find((cItem) => {
              return cItem.value === city
            })
          // 有的字段只有市没有区，也就是没有children字段，我们可以进行以下判断
          let districtLength = cityItem.children && cityItem.children.length
          if (length === districtLength || !districtLength) {
            cityArr.push([province, city])
          } else {
            item.forEach((differItem) => {
              cityArr.push({
                pr: differItem[0],
                ct: differItem[1],
                dt: differItem[2],
              })
            })
          }
        }
        /**
       cityArr => [
       {"pr": "140000","ct": "140010","dt": "140011"},  //区
       ["150000","150010"],                             //市
       ["160000","160010"],                             //省
       ["160000","160011"],...*11
       ]
       */
        // ---------------------- 对市级区域进行再一次分组
        let provinceArr = []
        for (let i = 0, l = cityArr.length; i < l; i++) {
          if (i !== 0 && cityArr[i][0] === cityArr[i - 1][0]) {
            provinceArr[provinceArr.length - 1].push(cityArr[i])
          } else {
            provinceArr[provinceArr.length] = [cityArr[i]]
          }
        }
        /**
       provinceArr => [
       [{"pr": "140000","ct": "140010","dt": "140011"}], //区
       [["150000","150010"]],                            //市
       [["160000","160010"],["160000","160011"]...*11],  //省
       ]
       */
        // ----------------------- 对比并格式化省级所属的数据
        let finishArr = []
        for (let i = 0, l = provinceArr.length; i < l; i++) {
          let item = provinceArr[i]
          // 如果数据中有pr字段，代表已经格式化好数据，直接push进去即可
          if (item[0].pr) {
            finishArr.push(...item)
          } else {
            let length = item.length // 当前省中市得数量
            let province = item[0][0] // 每个数组得省的区域编码
            let cityLength = regionData.find((pItem) => {
              return pItem.value === province
            }).children.length
            if (length === cityLength) {
              finishArr.push({ pr: province })
            } else {
              item.forEach((differItem) => {
                finishArr.push({
                  pr: differItem[0],
                  ct: differItem[1],
                })
              })
            }
          }
        }
        /**
       	finishArr => [
           {"pr": "140000","ct": "140010","dt": "140011"},  //区
           {"pr": "150000","ct": "150100"},                 //市
           {"pr": "160000"},                                //省
       	]
       	*/
        // console.log(finishArr)
      },
    },
  }
</script>
```



啰嗦一句，如果想要把上述代码封装成组件，只需要把 v-model换成value即可，新增代码如下：

```js
// v-model="selectVal"		// 注释代码
:value="selectVal"			// 新增代码

props: {					// 新增代码
   selectVal: {
    type: Array,
    default: () => [],
  }
}

areaSelect(area){
  this.$emit('update:selectVal', area)   // 新增代码
}
```

