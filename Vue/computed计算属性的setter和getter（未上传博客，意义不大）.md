### 计算属性的setter和getter

* 大部分情况下用不到set方法。  
* 只用到get方法，所以这个为只读属性

```html
  <div id="app">
    <h2>{{fullName}}</h2>
  </div>

  <script>
    const app = new Vue({
      el:'#app',
      data:{
        firstName: 'liu',
        lastName: 'lily',
      },
      computed:{
        fullName:{
		  //当调用app.fullName = newValue时会调用set方法
          //set方法会传入新设置的value，所以我们可以在里面传入Value
          set:function (newValue) {
            const newName = newValue.split(' ');
            this.firstName = newName[0];
            this.lastName = newName[1];
          },
          get:function () {
            return this.firstName+ ' '+this.lastName;
          }
        }
      }
    })
  </script>
```
<br>

* 由于我们大部分情况用不到set方法，可以删除掉set方法，但写get方法太麻烦，所以vue设置了只需写函数就能调用get方法  

* 因为computed实质上是一个属性，所以调用computed的时候不用写括号()来调用  

	  //只需写函数即可
	  computed:{
		fullName:function () {
	      return this.firstName+ ' '+this.lastName;
	    }
	  }
	
	