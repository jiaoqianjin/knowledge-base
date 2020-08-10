# Vue 组件代码规范

[1 前言](#1)

[2 代码风格](#2)

  [2.1 缩进](#2-1)

  [2.2 单行最长限制](#2-2)

  [2.3 模块书写顺序](#2-3)

[3 template 部分](#3)

  [3.1 根节点](#3-1)

  [3.2 标签](#3-2)

  [3.3 属性](#3-3)

  [3.4 指令](#3-4)

  [3.5 插值（Mustache）](#3-5)

  [3.6 空格](#3-6)

  [3.7 变量](#3-7)

[4 javascript 部分](#4)

  [4.1 props](#4-1)

  [4.2 data](#4-2)

  [4.3 变量](#4-3)

  [4.4 其他](#4-4)

[5 style 部分](#5)

[6 参考](#6)

## 1 前言

任何问题或建议，欢迎跟我们讨论: [fe-styleguide@baidu.com](mailto:fe-styleguide@baidu.com)

## 2 代码风格

### 2.1 缩进

#### [强制] 使用 `4` 个空格做为一个缩进层级，不允许使用 `2` 个空格或 `tab` 字符

### 2.2 单行最长限制

#### [强制] 每行不得超过 `120` 个字符

### 2.3 模块书写顺序

#### [建议] `template` -> `script` -> `style`

## 3 template 部分

### 3.1 根节点

#### [强制] `template` 根节点只允许包含一个直接子节点，以下情况都是不允许的：

* 根结点为空；
* 根结点是文字；
* 根结点有多个元素；
* 在根结点使用循环；
* 在根结点使用 template 和 slot；
* 在根结点使用 v-if，但是没有 v-else；

```html
// bad
<template></template>
<template>hello</template>
<template><div>one</div><div>two</div></template>
<template><div v-for="x in list"></div></template>
<template><template>hello</template></template>
<template><div v-if="title"></div></template>

// good
<template><div>one</div></template>
```

### 3.2 标签

#### [强制] 自定义组件的标签名不得使用 HTML 中默认已定义的标签（reserved HTML elements），要求至少由两个单词组成，并且符合 `kebab-case`

解释：

避免和 HTML 保留字段冲突导致错误。

```html
// bad
<template>
    <sub/>
</template>

<script>
import OtherComponent from './OtherComponent.vue';

export default {
    components: {
        sub: OtherComponent
    }
}
</script>

// good
<template>
    <other-component/>
</template>

<script>
import OtherComponent from './OtherComponent.vue';

export default {
    components: {
        OtherComponent
    }
}
</script>

// bad
<component/>
<mycomponent/>
<myComponent/>
<MyComponent/>

// good
<my-component/>
```

> 预留的 html 标签包括：

> `html`,`body`,`base`,`head`,`link`,`meta`,`style`,`title`,`address`,`article`,`aside`,`footer`,`header`,`h1`,`h2`,`h3`,`h4`,`h5`,`h6`,`hgroup`,`nav`,`section`,`div`,`dd`,`dl`,`dt`,`figcaption`,`figure`,`picture`,`hr`,`img`,`li`,`main`,`ol`,`p`,`pre`,`ul`,`a`,`b`,`abbr`,`bdi`,`bdo`,`br`,`cite`,`code`,`data`,`dfn`,`em`,`i`,`kbd`,`mark`,`q`,`rp`,`rt`,`rtc`,`ruby`,`s`,`samp`,`small`,`span`,`strong`,`sub`,`sup`,`time`,`u`,`var`,`wbr`,`area`,`audio`,`map`,`track`,`video`,`embed`,`object`,`param`,`source`,`canvas`,`script`,`noscript`,`del`,`ins`,`caption`,`col`,`colgroup`,`table`,`thead`,`tbody`,`td`,`th`,`tr`,`button`,`datalist`,`fieldset`,`form`,`input`,`label`,`legend`,`meter`,`optgroup`,`option`,`output`,`progress`,`select`,`textarea`,`details`,`dialog`,`menu`,`menuitem`,`summary`,`content`,`element`,`shadow`,`template`,`blockquote`,`iframe`,`tfoot`；

> 预留的 SVG 标签包括：

> `svg`,`animate`,`circle`,`clippath`,`cursor`,`defs`,`desc`,`ellipse`,`filter`,`font-face`,`foreignObject`,`g`,`glyph`,`image`,`line`,`marker`,`mask`,`missing-glyph`,`path`,`pattern`,`polygon`,`polyline`,`rect`,`switch`,`symbol`,`text`,`textpath`,`tspan`,`use`,`view`

#### [强制] [HTML Void Element](https://www.w3.org/TR/html51/syntax.html#void-elements) 不需要闭合，其它类型标签都需要闭合

```html
// bad
<input></input>
<br></br>

// good
<input>
<br>
```

#### [强制] 非根结点的 `template` 标签里必须有一个以上的子结点

```html
// bad
<ul>
    <template>
        <li></li>
    </template>
</ul>

// good
<ul>
    <li></li>
</ul>

// good
<ul>
    <template>
        <li></li>
        <li></li>
    </template>
</ul>
```

#### [强制] `template` 标签上不能带有 `key` 属性

```html
// bad
<template>
    <div>
        <template key="x"></template>
        <template v-bind:key="y"></template>
        <template :key="z"></template>
    </div>
</template>

// good
<template>
    <div>
        <div key="x"></div>
        <template></template>
    </div>
</template>
```

#### [强制] 如果自定义标签中没有内容，需要以自闭合标签形式出现

```html
// bad
<c-title :url="url" :label-type="type"></c-title>

// good
<c-title :url="url" :label-type="type"/>
```

#### [强制] 标签右括号 `>` 的位置：

* 元素只有一行时，右括号与元素保持在同一行。
* 多行元素（元素最后一个属性与左括号 `<` 不在同一行）时，右括号 `>` 需要另起一行，缩进与左括号 `<` 保持对齐。

```html
// bad
<div id="foo" class="bar"
></div>

// good
<div id="foo" class="bar"></div>

// bad
<div
    id="foo"
    class="bar">
</div>

// good
<div
    id="foo"
    class="bar"
>
    some message
</div>

// bad
<c-title
    :text="text"
    :url="url"
    :label-type="type"/>

// good
<c-title
    :text="text"
    :url="url"
    :label-type="type"
/>
```


#### [建议] 自闭合标签的 `/>` 前不用添加空格

```html
// bad
<c-title :url="url" :label-type="type" />

// good
<div></div>
<c-title :url="url" :label-type="type"/>
```


### 3.3 属性

#### [强制] 属性值必须用双引号包围

```html
// bad
<div class='c-color'></div>

// good
<div class="c-color"></div>
```


#### [强制] 模板中的属性命名需要符合 `kebab-case`

```html
// bad
<my-component greetingText="hi"/>

// good
<my-component greeting-text="hi"/>
```


#### [强制] `class` / `style` 属性值不能设置空字符串

```html
// bad
<div class=""></div>
<div style=""></div>

// good
<div></div>
```


#### [建议] 布尔类型的属性值为 `true` 时，建议不添加属性值

```html
// bad
<c-title text="带箭头标题" :arrow="true"/>

// good
<input type="text" disabled>
<c-title text="带箭头标题" arrow/>
<c-title text="带箭头标题" :arrow="false"/>
```


#### [强制] 当组件的属性多于 `2` 个时，必须分成多行，每行写一个属性；只有属性个数小于或等于 `2` 个时，可以写在一行内

```html
// bad
<c-title :text="text" :url="url" :label-type="type"/>

// good
<c-title :text="text" :url="url"/>
<c-title
    :text="text"
    :url="url"
    :label-type="type"
/>
```


#### [建议] 当元素有多个属性时，应该按照统一的顺序书写

优先级顺序：

1. 定义（提供组件的选项）
    - is
2. 列表渲染（创建多个变化的相同元素）
    - v-for
3. 条件渲染（元素是否渲染/显示）
    - v-if
    - v-else-if
    - v-else
    - v-show
    - v-cloak
4. 渲染方式（改变元素的渲染方式）
    - v-pre/v-once
5. 全局感知（需要超越组件的知识）
    - id
6. 唯一的特性（需要唯一值的特性）
    - ref
    - key
    - slot
7. 双向绑定（把绑定和事件结合起来）
    - v-model
8. 未绑定的属性
9. 其它绑定（所有普通的绑定）
    - v-bind
9. 事件（组件事件监听器）
    - v-on
10. 内容（覆写元素的内容）
    - v-html
    - v-text

#### [强制] 不能有重复的属性，`class` 和 `style` 除外

解释：

这里重复的属性包括以下两种情况：
1. 属性名完全一样：如有多个 `foo` 或者有多个 `:foo`
2. 有一个普通属性和一个 `v-bind` 动态绑定的属性，动态绑定的属性名与普通属性名重复

```html
// bad
<c-title foo="abc" :foo="def"/>
<c-title foo="def" foo="abc"/>
<c-title class="def" class="abc"/>
<c-title style="def" style="abc"/>

// good
<c-title :foo="def"/>
<c-title foo="abc"/>
<c-title
    class="c-color"
    :class="{'c-selected': selected}"
/>
<c-title
    style="color: #000;"
    :style="{width: '100px'}"
/>
```

#### [建议] `slot` 命名采用 `kebab-case`

```html
<slot name="header-left"></slot>

<div slot="header-left"></div>
```

#### [建议] `ref` 命名采用 `PascalCase`

```html
<div ref="userInfo"></div>
```

### 3.4 指令

#### [强制] 在使用 `v-for` 的元素上添加 `key`，以便维护内部组件及其子树的状态

```html
<ul>
    <li
        v-for="todo in todos"
        :key="todo.id"
    >
        {{ todo.text }}
    </li>
</ul>
```


#### [建议] 不要把 `v-if` 和 `v-for` 同时用在同一个元素上

解释：

当 Vue 处理指令时，`v-for` 比 `v-if` 具有更高的优先级。所以如果想要使用 `v-if` 判断 `v-for` 元素列表是否显示，将两个指令同时应用在同一个元素的方法就是错误的。引入这个规则是为了避免引起困惑。

```html
// bad
<ul>
    <li
        v-for="user in users"
        v-if="user.isActive"
        :key="user.id"
    >
        {{ user.name }}
    </li>
</ul>

// good
<template>
    <ul>
        <li
            v-for="user in activeUsers"
            :key="user.id"
        >
            {{ user.name }}
        </li>
    </ul>
</template>
<script>
export default {
    computed: {
        activeUsers: function () {
            return this.users.filter(function (user) {
                return user.isActive;
            });
        }
    }
}
</script>
```


#### [建议] 以下指令统一使用缩写

* 使用 `:` 表示 `v-bind:`
* 使用 `@` 表示 `v-on:`


### 3.5 插值（Mustache）

#### [建议] 插值左右添加一个空格

```html
// bad
<div>{{   text   }}</div>
<div>{{text}}</div>

// good
<div>{{ text }}</div>
```


### 3.6 空格

#### [强制] 不能有多余空格

```html
// bad
<div     class="foo"
      :style="bar"         >   </div>

// good
<div class="foo" :style="bar"></div>
```


### 3.7 变量

#### [强制] 不能有多余的变量

```html
// bad
<ol><!-- "i" is defined but never used. -->
    <li v-for="i in 5">item</li>
</ol>

// good
<ol>
    <li v-for="i in 5">{{ i }}</li>
</ol>
```


#### [强制] 禁止在插值中使用 `this`

```html
// bad
<a :href="this.url">
    {{ this.text }}
</a>

// good
<a :href="url">
    {{ text }}
</a>
```


## 4 javascript 部分

### 4.1 props

#### [强制] 指定 `props` 类型

```html
// bad
<script>
export default {
    props: ['status']
};
</script>

// good
<script>
export default {
    props: {
        status: String
    }
};
```


#### [强制] 如果 `props` 没有指定为 `required` 或者 `required` 为 `false`，则需要指定默认值

```html
// bad
<script>
export default {
    props: {
        a: Number,
        b: [Number, String],
        c: {
            type: Number
        },
        d: {
            type: Number,
            required: false
        }
    }
};
</script>

// good
<script>
export default {
    props: {
        a: {
            type: Number,
            required: true
        },
        b: {
            type: [Number, String],
            default: 0
        },
        c: {
            type: Number,
            default: 0,
            required: false
        }
    }
};
</script>
```


#### [强制] `props` 提供的默认值必须满足校验条件

```html
// bad
<script>
export default {
    props: {
        propA: {
            type: String,
            default: {}
        },
        propB: {
            type: String,
            default: []
        },
        propC: {
            type: Object,
            default: []
        },
        propD: {
            type: Array,
            default: []
        },
        propE: {
            type: Object,
            default: {
                message: 'hello'
            }
        }
    }
};
</script>

// good
<script>
export default {
    props: {
        propA: Number,
        propB: [String, Number],
        propD: {
            type: Number,
            default: 100
        },
        propE: {
            type: Object,
            default() {
                return {
                    message: 'hello'
                };
            }
        }
    }
};
```


#### [强制] 在 `props` 中声明的属性，其属性名应该始终符合 `camelCase`

```html
// bad
<script>
export default {
    props: {
        'greeting-text': String
    }
};
</script>

// good
<script>
export default {
    props: {
        greetingText: String
    }
};
</script>
```

### 4.2 data

#### [强制] `data` 必须是一个函数

```html
// bad
<script>
export default {
    data: {
        b: 1
    }
}
</script>

// good
<script>
export default {
    data() {
        return {
            b: 1
        };
    }
}
</script>
```


#### [强制] `data` 中禁止使用 `computed` 中的变量

```html
// bad
<script>
export default {
    props: {
        a: {
            type: String,
            default: 0
        }
    },
    data() {
        return {
            d: this.f
        };
    },
    computed: {
        f() {
            return this.a * 10;
        }
    }
};
</script>
```

#### [强制] `props`, `data`, `computed`, `methods` 中不能有重复的 `key`

```html
// bad
<script>
export default {
    props: {
        foo: String
    },
    data() {
        return {
            foo: null
        };
    },
    computed: {
        foo() {
            return 'foo';
        }
    }
}
</script>

// good
<script>
export default {
    props: {
        foo: String
    },
    data() {
        return {
            bar: null
        };
    },
    computed: {
        baz() {
            return foo + bar;
        }
    }
}
</script>
```

### 4.3 变量

#### [强制] 不能使用 Vue 中的保留字段命名变量

解释：

Vue 使用 `_` 前缀来定义其自身的私有属性，所以使用相同的前缀 (比如 `_update`) 有覆写实例属性的风险。即便你检查确认 Vue 当前版本没有用到这个属性名，也不能保证和将来的版本没有冲突。

对于 `$` 前缀来说，其在 Vue 生态系统中的目的是暴露给用户的一个特殊的实例属性，所以把它用于私有属性并不合适。

不过，我们推荐把这两个前缀结合为 `$_`，作为一个用户定义的私有属性的约定，以确保不会和 Vue 自身相冲突。

```html
// bad
<script>
export default {
    props: {
        $el: String
    },
    data() {
        return {
            _foo: null
        };
    },
    computed: {
        $on() {
            return 2;
        }
    },
    methods: {
        $nextTick() {
        }
    }
}
</script>
```

### 4.4 其它

#### [建议] 组件中使用 `$emit` 事件时携带的参数，个数不应该超过 `2` 个。建议将数据参数以 `Object` 形式传递，将事件参数 `event` 放在最后

```javascript
// bad
onClick(event) {
    this.$emit('click', this.value1, this.value2, event);
}

// good
onClick(event) {
   this.$emit(
       'click',
       {
           value1: this.value1,
           value2: this.value2
       },
       event
   );
}

// good
onClick(event) {
   this.$emit('click', event);
}
```

## 5 style 部分

#### [建议] 为组件样式设置作用域

```html
<style scoped>
.button {
    border: none;
    border-radius: 2px;
}
</style>
```

## 6 参考

* [vue 代码风格指南](https://cn.vuejs.org/v2/style-guide/)
* [ecomfe HTML 编码规范](https://github.com/ecomfe/spec/blob/master/html-style-guide.md)
* [ecomfe JavaScript 编码规范](https://github.com/ecomfe/spec/blob/master/javascript-style-guide.md)
* [ecomfe JavaScript 编码规范 - ESNext 补充篇（草案）](https://github.com/ecomfe/spec/blob/master/es-next-style-guide.md)
* [ecomfe CSS 编码规范](https://github.com/ecomfe/spec/blob/master/css-style-guide.md)
* [ecomfe less 编码规范](https://github.com/ecomfe/spec/blob/master/less-code-style.md)
