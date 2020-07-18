# React 编码规范




[1 版本限制](#1)

　　[1.1 法律要求](#1-1)

[2 文件组织](#2)

　　[2.1 命名](#2-1)

　　[2.2 模块化](#2-2)

[3 命名规则](#3)

　　[3.1 组件](#3-1)

　　[3.2 属性](#3-2)

　　[3.3 高阶组件](#3-3)

[4 组件声明](#4)

　　[4.1 组件](#4-1)

　　[4.2 属性](#4-2)

　　[4.3 生命周期](#4-3)

　　[4.4 高阶组件](#4-4)

[5 组件实现](#5)

　　[5.1 更新机制](#5-1)

　　[5.2 纯函数应用](#5-2)

[6 JSX](#6-jsx)

　　[6.1 标签](#6-1)

　　[6.2 属性](#6-2)

　　　　[6.2.1 层级](#6-2-1)



## 1 版本限制


### 1.1 法律要求


#### [强制] 使用React `>=15.6.2`版本

解释

在`15.6.2`以前的版本协议带有Facebook Patents，依据法务风险，严禁使用。



## 2 文件组织


### 2.1 命名


#### [强制] 同一目录下不得拥有同名的`.js`和`.jsx`文件。

解释：

在使用模块导入时，倾向于不添加后缀，如果存在同名但不同后缀的文件，构建工具将无法决定哪一个是需要引入的模块。

#### [强制] 组件文件使用一致的`.js`或 `.jsx`后缀。

解释：

所有组件文件的后缀名从`.js`或`.jsx`中任选其一。

不应在项目中出现部分组件为`.js`文件，部分为`.jsx`的情况。

### 2.2 模块化


#### [建议] 实现单个组件的文件以`export default`的形式暴露一个组件。

解释：

允许一个文件中存在多个不同的组件，但仅允许通过`export default`暴露一个组件，其它组件均定义为内部组件。

#### [建议] 组织多个组件的目录使用一个`index.js`以命名导出的形式暴露所有组件。

解释：

同目录内的组件相互引用使用`import Foo from './Foo';`进行。

引用其它目录的组件使用`import {Foo} from '../component';`进行。

建议使用[VSCode的export-index插件](https://marketplace.visualstudio.com/items?itemName=BrunoLM.export-index)等插件自动生成`index.js`的内容。



## 3 命名规则


### 3.1 组件


#### [强制] 组件名为PascalCase。

包括函数组件，名称均为PascalCase。

#### [强制] 组件名称与文件名称保持相同。

同时组件名称应当能体现出组件的功能，以便通过观察文件名即确定使用哪一个组件。

### 3.2 属性


#### [强制] 使用`onXxx`形式作为`props`中用于回调的属性名称。

解释：

使用统一的命名规则用以区分`props`中回调和非回调部分的属性，在JSX上可以清晰地看到一个组件向上和向下的逻辑交互。

对于不用于回调的函数类型的属性，使用动词作为属性名称。

示例：

```javascript
// onClick作为回调以on开头，renderText非回调函数则使用动词
let Label = ({onClick, renderText}) => <span onClick={onClick}>{renderText()}</span>;
```

#### [建议] 作为组件方法的事件处理函数以具备业务含义的词作为名称，不使用`onXxx`形式命名。

示例：

```javascript
// Good
class Form {
    @bind()
    collectAndSubmitData() {
        let data = {
            name: this.state.name,
            age: this.state.age
        };
        this.props.onSubmit(data);
    }

    @bind()
    syncName() {
        // ...
    }

    @bind()
    syncAge() {
        // ...
    }

    render() {
        return (
            <div>
                <label>姓名：<input type="text" onChange={this.syncName} /></label>
                <label>年龄：<input type="number" onChange={this.syncAge} /></label>
                <button type="button" onClick={this.collectAndSubmit}>提交</button>
            </div>
        );
    }
}
```

### 3.3 高阶组件


#### [强制] 高阶组件使用camelCase命名。

解释：

高阶组件事实上并非一个组件，而是一个“生成组件类型”的函数，因此遵守JavaScript函数命名的规范，使用camelCase命名。

#### [建议] 使用`withXxx`或`xxxable`形式的词作为高阶组件的名称。

解释：

高阶组件是为组件添加行为和功能的函数，因此使用如上形式的词有助于对其功能进行理解。




## 4 组件声明


### 4.1 组件


#### [强制] 使用ES Class声明组件，禁止使用`React.createClass`。

解释：

[React v15.5.0](https://facebook.github.io/react/blog/2017/04/07/react-v15.5.0.html)已经弃用了`React.createClass`函数。

示例：

```javascript
// Bad
let Message = React.createClass({
    render() {
        return <span>{this.state.message}</span>;
    }
});

// Good
class Message extends PureComponent {
    render() {
        return <span>{this.state.message}</span>;
    }
}
```

#### [强制] 尽量使用无状态函数组件。

解释：

函数组件在React中有着特殊的地位，在将来也有可能得到更多的内部优化。

示例：

```javascript
// Bad
class NextNumber {
    render() {
        return <span>{this.props.value + 1}</span>
    }
}

// Good
let NextNumber = ({value}) => <span>{value + 1}</span>;
```

#### [建议] 无需显式引入React对象。

解释：

使用JSX隐式地依赖当前环境下有`React`这一对象，但在源码上并没有显式使用，这种情况下添加`import React from 'react';`会造成一个没有使用的变量存在。

使用[babel-plugin-react-require](https://www.npmjs.com/package/babel-plugin-react-require)插件可以很好地解决这一问题，因此无需显式地编写`import React from 'react';`这一语句。

#### [建议] 使用箭头函数声明函数组件。

解释：

箭头函数具备更简洁的语法（无需`function`关键字），且可以在仅有一个语句时省去`return`造成的额外缩进。

### 4.2 属性


#### [强制] 对于所有非`isRequired`的属性，在`defaultProps`中声明对应的值。

解释：

声明初始值有助于对组件初始状态的理解，也可以减少`propTypes`对类型进行校验产生的开销。

对于初始没有值的属性，应当声明初始值为`null`而非`undefined`。

#### [强制] 如无必要，使用属性语法声明`propsTypes`、`contextTypes`、`defaultProps`和`state`。

解释：

仅当初始`state`需要从`props`计算得到的时候，才将`state`的声明放在构造函数中，其它情况下均使用属性声明进行。

### 4.3 生命周期


#### [强制] 禁止使用`componentWillMount`。

解释：

使用`constructor`代替。

#### [强制] 依照规定顺序编排组件中的方法和属性。

按照以下顺序编排组件中的方法和属性：

1. `static displayName`
2. `static propTypes`
3. `state defaultProps`
4. 其它静态的属性
5. `state`
6. 其它实例属性
7. 用于事件处理并且以属性的方式（`onClick = e => {...}`）声明的方法
8. `constructor`
9. `componentDidMount`
10. `shouldComponentUpdate`
11. `static getDerivedStateFromProps`
12. `componentDidUpdate`
13. `componentWillUnmount`
14. 事件处理方法
15. 其它方法
16. `render`

### 4.4 高阶组件


#### [建议] 高阶组件返回新的组件类型时，添加`displayName`属性。

同时在`displayName`上声明高阶组件的存在。

```javascript
// Good
let asPureComponent = Component => {
    let componentName = Component.displayName || Component.name || 'UnknownComponent';
    return class extends PureComponent {
        static displayName = `asPure(${componentName})`

        render() {
            return <Component {..this.props} />;
        }
    };
};
```



## 5 组件实现


### 5.1 更新机制


#### [强制] 禁止为继承自`PureComponent`的组件编写`shouldComponentUpdate`实现。

参考[React的相关Issue](https://github.com/facebook/react/issues/9239)，在React的实现中，`PureComponent`并不直接实现`shouldComponentUpdate`，而是添加一个`isReactPureComponent`的标记，由`CompositeComponent`通过识别这个标记实现相关的逻辑。因此在`PureComponent`上自定义`shouldComponentUpdate`并无法享受`super.shouldComponentUpdate`的逻辑复用，也会使得这个继承关系失去意义。

### 5.2 纯函数应用


#### [强制] 禁止在`componentWillReceiveProps`中包含除`setState`外的副作用。

解释：

`componentWillReceiveProps`应当且仅应当用于`props`和`state`的数据同步，不得用于获取远程数据、更新外部状态、执行回调函数等逻辑。

新版React使用`getDerivedStateFromProps`代替`componentWillReceiveProps`。

#### [建议] 除顶层或路由级组件以外，所有组件均在概念上实现为纯组件（Pure Component）。

本条规则并非要求组件继承自`PureComponent`，“概念上的纯组件”的意思为一个组件在`props`和`state`没有变化（shallowEqual）的情况下，渲染的结果应保持一致。

一个典型的非纯组件是使用了随机数或日期等函数：

```javascript
let RandomNumber = () => <span>{Math.random()}</span>;
let Clock = () => <span>{Date.time()}</span>;
```

非纯组件具备向上的“传染性”，即一个包含非纯组件的组件也必须是非纯组件，依次沿组件树结构向上。由于非纯组件无法通过`shouldComponentUpdate`优化渲染性能且具备传染性，因此要避免在非顶层或路由组件中使用。

如果需要在组件树的某个节点使用随机数、日期等非纯的数据，应当由顶层组件生成这个值并通过`props`传递下来。对于使用Redux等应用状态管理的系统，可以在应用状态中存放相关值（如Redux使用Action Creator生成这些值并通过Action和reducer更新到store中）。



## 6 JSX


### 6.1 标签


#### [强制] 没有子节点的组件使用自闭合语法。

解释：

JSX与HTML不同，所有元素均可以自闭合。

示例：

```javascript
// Bad
<Foo></Foo>
<div></div>

// Good
<Foo />
<div />
```

#### [强制] 保持起始和结束标签在同一层缩进。

解释：

对于标签前面有其它语句（如`return`的情况，使用括号进行换行和缩进）。

对于直接`return`的函数组件，可以直接使用括号而省去大括号和`return`关键字：

```javascript
let Message = () => (
    <div>
        <span>Hello World</span>
    </div>
);
```

示例：

```javascript
// Bad
class Message {
    render() {
        return <div>
            <span>Hello World</span>
        </div>;
    }
}

// Good
class Message {
    render() {
        return (
            <div>
                <span>Hello World</span>
            </div>;
        );
    }
}
```

#### [强制] 自闭合标签的`/>`前添加一个空格。

示例：

```javascript
// Bad
<Foo bar="bar"/>
<Foo bar="bar"  />

// Good
<Foo bar="bar" />
```

### 6.2 属性


#### [强制] 对于多属性需要换行，从第一个属性开始，每个属性一行。

示例：

```javascript
// 没有子节点
<SomeComponent
    longProp={longProp}
    anotherLongProp={anotherLongProp}
/>

// 有子节点
<SomeComponent
    longProp={longProp}
    anotherLongProp={anotherLongProp}
>
    <SomeChild />
    <SomeChild />
</SomeComponent>
```

#### [强制] 以字符串字面量作为值的属性使用双引号（`"`），在其它类型表达式中的字符串使用单引号（`'`）。

示例：

```javascript
// Bad
<Foo bar='bar' />
<Foo style={{width: "20px"}} />

// Good
<Foo bar="bar" />
<Foo style={{width: '20px'}} />
```

#### [强制] 对于值为`true`的属性，省去值部分。

示例：

```javascript
// Bad
<Foo visible={true} />

// Good
<Foo visible />
```

#### [强制] 值为`true`的属性放在其它属性前面。

解释：

将`true`类的值放在前面使得组件的声明更接近自然语言，提高可读性。

示例：

```javascript
// Bad
<Foo data={data} visible requireValidation />

// Good
<Foo visible requireValidation data={data} />
```

#### [强制] 对于需要使用`key`的场合，提供一个唯一标识作为`key`属性的值，禁止使用可能会变化的属性（如索引）。

解释：

`key`属性是React在进行列表更新时的重要属性，如该属性会发生变化，渲染的性能和**正确性**都无法得到保证。

示例：

```javascript
// Bad
{list.map((item, index) => <Foo key={index} {...item} />)}

// Good
{list.map(item => <Foo key={item.id} {...item} />)}
```

#### [建议] 避免在JSX的属性值中直接使用对象和函数表达式。

解释：

`PureComponent`使用[`shallowEqual`](https://github.com/facebook/fbjs/blob/master/packages/fbjs/src/core/shallowEqual.js)对`props`和`state`进行比较来决定是否需要渲染，而在JSX的属性值中使用对象、函数表达式会造成每一次的对象引用不同，从而`shallowEqual`会返回`false`，导致不必要的渲染。

示例：


```javascript
// Bad
class WarnButton {
    alertMessage(message) {
        alert(message);
    }

    render() {
        return <button type="button" onClick={() => this.alertMessage(this.props.message)}>提示</button>
    }
}

// Good
class WarnButton {
    @bind()
    alertMessage() {
        alert(this.props.message);
    }

    render() {
        return <button type="button" onClick={this.alertMessage}>提示</button>
    }
}
```

#### 6.2.1 层级


#### [建议] 将JSX的层级控制在5层以内。

解释：

JSX提供了基于组件的便携的复用形式，因此可以通过将结构中的一部分封装为一个函数组件来很好地拆分大型复杂的结构。层次过深的结构会带来过多缩进、可读性下降等缺点。如同控制函数内代码行数和分支层级一样，对JSX的层级进行控制可以有效提升代码的可维护性。

示例：

```javascript
// Bad
let List = ({items}) => (
    <ul>
        {
            items.map(item => (
                <li>
                    <header>
                        <h3>{item.title}</h3>
                        <span>{item.subtitle}</span>
                    </header>
                    <section>{item.content}</section>
                    <footer>
                        <span>{item.author}</span>@<time>{item.postTime}</time>
                    </footer>
                </li>
            ))
        }
    </ul>
);

// Good
let Header = ({title, subtitle}) => (
    <header>
        <h3>{title}</h3>
        <span>{subtitle}</span>
    </header>
);

let Content = ({content}) => <section>{content}</section>;

let Footer = ({author, postTime}) => (
    <footer>
        <span>{author}</span>@<time>{postTime}</time>
    </footer>
);

let Item = item => (
    <div>
        <Header {...item} />
        <Content {...item} />
        <Footer {...item} />
    </div>
);

let List = ({items}) => (
    <ul>
        {items.map(Item)}
    </ul>
);
```



