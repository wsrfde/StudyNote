### 使用 Mocha 自动化测试

> 自动化测试是独立于代码的。它们以各种方式运行我们的函数，并将结果与预期结果进行比较。

当我们在写一个函数时，我们通常可以想象出它应该做什么：哪些参数会给出哪些结果。

但这样的手动“重新运行”是不完美的。**当通过手动重新运行来测试代码时，很容易漏掉一些东西。**



### 开发流程

开发流程通常看起来像这样：

1. 编写初始规范，测试最基本的功能。
2. 创建一个最初始的实现。
3. 检查它是否工作，我们运行测试框架 [Mocha](http://mochajs.org/)（很快会有更多细节）来运行测试。当功能未完成时，将显示错误。我们持续修正直到一切都能工作。
4. 现在我们有一个带有测试的能工作的初步实现。
5. 我们增加更多的用例到规范中，或许目前的程序实现还不支持。无法通过测试。
6. 回到第 3 步，更新程序直到测试不会抛出错误。
7. 重复第 3 步到第 6 步，直到功能完善。

如此来看，开发就是不断地 **迭代**。我们写规范，实现它，确保测试通过，然后写更多的测试，确保它们工作等等。最后，我们有了一个能工作的实现和针对它的测试。

这里我们来使用一种名为 **行为驱动开发**或简言为 BDD 的技术。

**BDD 包含了三部分内容：测试、文档和示例。**



### 开发规范

我们来看一个简单的测试代码

```js
describe("pow", function() {		
  
  it("raises to n-th power", function() {
    assert.equal(pow(2, 3), 8);
  });

});
```

让我们用伪代码来理解这段函数

```js
describe(描述,函数(){
  it("测试描述",函数(){
  	assert.equal(被测函数,期望值)		//equal用来测试两个值是否相等，其他断言语句可在下方查看
	})
})
```



### 正式测试

在本教程中，我们将使用以下 JavaScript 库进行测试：

- [Mocha](http://mochajs.org/) —— 核心框架：提供了包括通用型测试函数 `describe` 和 `it`，以及用于运行测试的主函数。
- [Chai](http://chaijs.com/) —— 提供很多断言（assertion）支持的库。它提供了很多不同的断言，现在我们只需要用 `assert.equal`。
- [Sinon](http://sinonjs.org/) —— 用于监视函数、模拟内置函数和其他函数的库，我们在后面才会用到它。

这些库都既适用于浏览器端，也适用于服务器端。

```html
<!DOCTYPE html>
<html>

<head>
  <!-- 添加mocha css，以显示结果 -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/mocha/3.2.0/mocha.css">
  <!-- 添加 mocha 框架代码 -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/mocha/3.2.0/mocha.js"></script>
  <!-- 添加 chai -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/chai/3.5.0/chai.js"></script>
</head>

<body>
  <!-- 项目代码 -->
  <script>
    //得到x的n次方
    function pow(x, n) {
      let result = 1;

      for (let i = 0; i < n; i++) {
        result *= x;
      }

      return result;
    }
  </script>

  <!-- 建议把以下测试代码都放在 test.js 中 -->
  <script>
  /**
   @describe使用方法

    describe(描述,函数(){
        it("测试描述",函数(){
          assert.equal(被测函数,期望值)
        })
    })
   */


    //我们来使用一种名为 行为驱动开发 或简言为 BDD 的技术。
    mocha.setup('bdd');

    // chai 有很多东西，让我们断言全局
    let assert = chai.assert;


    describe("pow", function () {

      describe("普通测试方式", function () {
        // 普通测试
        it("测试2的3次方", function () {
          assert.equal(pow(2, 3), 8);   //函数测试pow(2, 3)    预期值
        });
        // it.only("测试3的4次方", function () {    //it.only()   Mocha 将只运行这个代码块
        //   assert.equal(pow(3, 4), 81);
        // })
        it("测试两个value是否相等",function () {
          assert.equal(81, 81);
        })
      })

      
      //除了普通的测试方式，我们还可以使用for循环测试每个值
      describe("for循环测试", function () {
        //for循环测试
        function makeTest(x) {
          let result = x * x * x;
          it(`变量${x}的3次方=${result}`, function () {
            assert.equal(pow(x, 3), result);
          })
        }
        for (let x = 1; x < 5; x++) {
          makeTest(x);
        }
      })

    });


    //id ="mocha"的元素将包含测试结果
    document.write('<div id="mocha"></div>');
    //运行测试
    mocha.run();
  </script>

</body>

</html>
```



### Chai中其他断言语句

请注意断言语句 `assert.isNaN`：它用来检查 `NaN`。

在 [Chai](http://chaijs.com/) 中也有其他的断言，例如：

- `assert.equal(value1, value2)` —— 检查相等 `value1 == value2`。
- `assert.strictEqual(value1, value2)` —— 检查严格相等 `value1 === value2`。
- `assert.notEqual`，`assert.notStrictEqual` —— 执行和上面相反的检查。
- `assert.isTrue(value)` —— 检查 `value === true`。
- `assert.isFalse(value)` —— 检查 `value === false`。
- ……完整的列表请见 [docs](http://chaijs.com/api/assert/)



### 总结

在 BDD 中，规范先行，实现在后。最后我们同时拥有了规范和代码。

规范有三种使用方式：

1. 作为 **测试** —— 保证代码正确工作。
2. 作为 **文档** —— `describe` 和 `it` 的标题告诉我们函数做了什么。
3. 作为 **案例** —— 测试实际工作的例子展示了一个函数可以被怎样使用。

有了规范，我们可以安全地改进、修改甚至重写函数，并确保它仍然正确地工作。

这在一个函数会被用在多个地方的大型项目中尤其重要。当我们改变这样一个函数时，没有办法手动检查每个使用它们的地方是否仍旧正确。

如果没有测试，一般有两个办法：

1. 展示修改，无论修改了什么。然后我们的用户遇到了 bug，这应该是我们没有手动完成某些检查。
2. 如果对出错的惩罚比较严重，并且没有测试，那么大家会很害怕修改这样的函数，然后这些代码就会越来越陈旧，没有人会想接触它。这很不利于发展。

**自动化测试则有助于避免这样的问题！**

如果这个项目被测试代码覆盖了，就不会出现这种问题。在任何修改之后，我们都可以运行测试，并在几秒钟内看到大量的检查。