```js
//判断为空

//普通写法
if(a == undefined) a = [];

if(params.success) {
    params.success(res);
}

//优雅写法
a = a || [];
params.success&&params.success(res);

//多条件判断
//普通写法
var Statistics = function(){
    console.log('执行');
}
switch (currentTab){
    case 0:
        Statistics();
        break;
    case 1:
        Statistics();
        break;
    case 2:
        Statistics();
        break;
    case 3:
        Statistics();
        break;
}

//优雅写法
//将判断条件作为对象的属性名，将处理逻辑作为对象的属性值
var Statistics = function(){
    console.log('执行');
}
const comparativeTotles = new Map([
    [0,Statistics],
    [1,Statistics],
    [2,Statistics],
    [3,Statistics]
])
comparativeTotles.get(1)&&comparativeTotles.get(1)();

//匿名函数调用
//普通写法
(function(){})();

//优雅写法
!function(){}();
+function(){}();

//取整
//普通写法
parseInt(15.96);
//优雅写法
15.96 | 0;  //15
~~15.96;    //15

//异步回调改async/await
async function getData() {
    const result = await axios.get('https://dudu.qi/secret/ping');
    const data = result.data;
    console.log('data',data);
    return data;
}
getData();

//解构（Destructuring）& 默认值
//const {data} = await axios.get(...)
//当获取不到data的时候，可以通过设置一个默认值，来避免报错
const {id = 5} = {};
console.log(id);
```

