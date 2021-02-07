---
title: StoryBook6.0
date: 2021-02-07 21:16:15
tags: 组件化工具
---



## 组件化开发常见的问题

无论你使用`React`还是`Vue`进行项目开发，都一定都会封装出各种各样的组件，这些组件将一个完整的页面分成若干份，让整体的代码看起来更加清晰易读

随着项目越来越复杂，你可能会写出很多好用的公共组件，但这些组件往往都被引入在复杂的页面结构中，使其没有办法清晰的展示给其他的协同开发人员，所以我们总会看到一个组件在项目中被实现了一遍又一遍。而这种情况在B端系统中尤为常见

在平时常见的脚手架（React，Vue）搭建的项目中，并没有可以单独展示组件的入口，所以如何把项目中的公共组件进行优雅的展示，成为一个值得讨论的问题。

## StoryBook

`StoryBook`是目前最受欢迎的 组件可视化展示平台 ,自上线以来就一直被广大开发者青睐。可以为目前大部分框架提供结构化的UI开发和文档。

2020年8月，`StoryBook`又推出了6.0版本（几乎完全向后兼容5.X.），本次的改动大大的加强了它的使用体验。有以下几个方面：

1. 零配置设置
2. 新一代的故事格式
3. 实时编辑，无需代码

## 安装初始化storyBook

如果你想在已有的项目中引入`StoryBook`，那直接去情况二就好，如果你想创建一个新的项目，那么请选择情况一

#### 情况一：新项目中生成StoryBook

```javascript
# 创建组件库目录
mkdir storybook-project

# 进入项目
cd storybook-project

# 初始化storybook
npx sb init

==这时会进行提示==

# 你是否要手动选择要安装的storybook项目类型？
Do you want to manually choose a Storybook project type to install? y/n

选择y

# 下一步选择项目类型：react（以react为例）
 Please choose a project type from the following list:

选择react
--------------------------------------------------
# 当然，
你也可以在初始化的时候直接指定好项目类型：

npx sb init --type react
```

##### == 注意 ==

以`React`为例，假如你的新项目还没有安装过除`StoryBook`之外任何依赖，你需要安装你选择的框架类型所对应的相关依赖包。否则`StoryBook`会因为找不到这些依赖而导致报错

```javascript
npm install react react-dom --save
```

#### 情况二：已有的项目中引入StoryBook

```javascript
#先进入到已有项目的目录,执行

npx sb init

# 例如当前的项目是Vue、React、框架搭建的， storybook 会自动检测项目类型并进行初始化！！
```

自StoryBook6.0起，`.storybook`的文件夹里面只有两个文件，并且已经默认配置好`add-docs (配置文档)`、`addon-info(自动化)`等常用的功能（6.0之前的版本都需要安装对应的依赖，并进行配置，很繁琐！！！）

## 运行storyBook

```javascript
npm run storybook
//或
yarn storybook
```

到这里你应该已经成功的运行起来`StoryBook`了，接下来介绍一些 `StoryBook`中常用的操作

## 编写StoryBook

== 以默认初始化好的Button组件为例~ ==

1. 首先你需要创建一个以`.stories.js`或者`.stories.tsx`结尾的文件（运行StoryBook的时候，会自动匹配后缀名）
2. 然后在这个文件中引入你写好的组件
3. 接下来就是编写组件的`StoryBook`

基本代码如下：

```javascript
// Button.stories.js
import React from 'react';
//导入你写好的组件
import Button from 'xxxx'
//创建模板
const Template = (args) => <Button {...args} />;
//导出被展示的组件
export const Primary = Template.bind({});
//为该组件设置参数
Primary.args = {
  //组件需要接受的属性
  primary: true,
  label: 'Primary',
};
```

这时组件就会被成功的展示：

![image-20210203100835369](/Users/koolearn/Library/Application Support/typora-user-images/image-20210203100835369.png)

### 故事页中组件的基本配置以及常见操作：

下列代码主要涉及几个属性的配置

+ title：设置左边的菜单栏，展示组件的名称
+ component:需要展示的组件
+ argTypes：对一些特定的属性进行特殊的配置
+ decorators：为组件添加样式

如何生成一个新的展示页？

每导出一个新的组件，StoryBook就会为你重新创建一个展示页面（如下图）

```javascript
// Button.stories.js
# 我们可以在这个对象中对本故事页进行配置，在该对象中配置过的属性会在该组件的整个故事页中生效
export default {
  # Example是该组件的一级标题名称，Button是该组件的二级标题名称
  title: 'Example/Button',
  # Button需要展示的组件
  component: Button,
  ,
  # 为背景色添加颜色控制器（这是storybook自带的颜色选择器）
 argTypes: {
    backgroundColor: { control: 'color' },
  },
  # 装饰器，修饰组件的样式
  decorators: [(Story) => <div style={{ margin: '3em' }}><Story/></div>]
};
const Template = (args) => <Button {...args} />;
# 每次导出都会重新创建一个展示页
export const Primary = Template.bind({});
# 每次导出都会重新创建一个展示页，
export const second=Template.bind({});

# 可以通过修改storyName的方式为组件改名（中文也可以）
second.storyName='Secondary'

```

效果如下：

![image-20210203110400190](https://gitee.com/lxx7473/images/raw/master/img/config-story.png)

### Args

每个组件都需要传入一些属性用来切换组件的不同状态，

### Decorators

`Decorators`可以对你要展示的组件进行装饰，修改每个故事中的组件的样式，比如让组件居中，左对齐等等。

基本用法如下：

```javascript
// Button.stories.tsx
export default {
  component: Button,
  decorators: [(Story) => <div style={{ margin: '3em' }}><Story/></div>]
}
```

展示：

![image-20210203102246168](https://gitee.com/lxx7473/images/raw/master/img/storybook.png)

##### == 注意 ==

这种配置方式会作用于`Button.stories.tsx`下所有的`Button`

当然我们也可以对某个组件进行单独配置

```javascript
export default {
  title: 'Example/Button',
  component: Button,
  argTypes: {
    backgroundColor: { control: 'color' },
  },
};
# 模板1
const Template1 = (args) => <div style={{margin:'3em'}}><Button {...args} /></div>;
# 模板2
const Template2 = (args) => <div ><Button {...args} label={'button'}/></div>;

export const Primary = Template1.bind({});
Primary.args = {
  primary: true,
  label: 'Button',
};
export const second = Template2.bind({});

```

Primary组件样式已经被改变

![image-20210203112425437](/Users/koolearn/Library/Application Support/typora-user-images/image-20210203112425437.png)

Second组件样式未被改变

![image-20210203112804191](https://gitee.com/lxx7473/images/raw/master/img/moban.png)



### Parameters

通常用于控制Storybook功能和插件的行为。例如，我们通过参数自定义背景插件。

在单个故事页中设置parameters

```javascript
// Button.story.js
export const Primary = Template.bind({});
Primary.args = {
  primary: true,
  label: 'Button',
};
Primary.parameters = {
  backgrounds: {
    values: [
      { name: 'red', value: '#f00' },
      { name: 'green', value: '#0f0' },
    ],
  },
};
```

也在全局中设置parameters

```javascript
// .storybook/preview.js
export const parameters = {
  backgrounds: {
    values: [
      { name: 'red', value: '#f00' },
      { name: 'green', value: '#0f0' },
    ],
  },
};
```

展示：

![image-20210203143450841](https://gitee.com/lxx7473/images/raw/master/img/bg-storybook.png)



## StoryBook中的Doc(文档)配置

`DocsPage`是零配置的默认文档，它将您的组件，文本描述，args表，代码示例，统一展示到`Doc`页面中，自动生成如图：

![image-20210203154951177](https://gitee.com/lxx7473/images/raw/master/img/20210203155030.png)

当然如果默认的`doc`文档不能满足你所预期的时候，你也可以对该`doc`结构进行配置，不过官方的建议是：希望每个组件都有自己的文档和故事集。

## Doc中的常见配置

### page

我们可以通过设置`docs.page`的值来改变`doc`文档的结构。当`page`值为`null`的时候。

**组件层**

作用范围：`Primary`组件

```javascript
//文件路径
// Button.stories.js | Button.stories.ts
export const Primary = Template.bind({});
Primary.parameters = { docs: { page: null } }
```

**故事层**

作用范围：该文件下的组件

```javascript
//文件路径
// Button.stories.js | Button.stories.ts
import { Button } from './Button';
export default {
  title: 'Button',
  component: Button,
  parameters: { 
    docs: { 
      page: null 
    } 
  },
};
```

**全局层**

作用范围：所有以`stories.js||stories.jsx||stories.tsx`结尾的文件

```javascript
//文件路径
// .storybook/preview.js
export const parameters = { docs: { page: null } };
```

效果如图：

![image-20210203155502431](https://gitee.com/lxx7473/images/raw/master/img/20210203155502.png)

通过修改`page`属性我们也可以`doc`的结构进行配置

```javascript
//.Button.stories.jsx
import {
     Title,
     Subtitle,
     Description,
     Primary,
     ArgsTable,
     Stories,
     PRIMARY_STORY,
} from '@storybook/addon-docs/blocks' 
//.....
parameters: {
        docs: {
            page: () => (
                <>
              //大标题
                 <Title />
              //小标题
                 <Subtitle /> 
              //描述
                  <Description /> 
              //被展示的组件
                   <Stories includePrimary={true} title='' />
              //组件的属性列表
                    <ArgsTable story={PRIMARY_STORY} />
                </>
            ),
        },
    }
```
