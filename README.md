# vue-mpvue
vue及mpvue踩坑指南


# mpvue 快速上手
1、先按照官网的“快速上手”，新建一个项目。
地址： http://mpvue.com/mpvue/quickstart/





2、启动一下刚建好的项目。
        先在项目目录下执行“cnpm install”（没有淘宝镜像请用npm install）安装依赖，然后执行“npm run dev”启动项目

        成功运行后，这个项目代码就进入开发模式，一旦有源代码发生修改，就会触发自动编译。因为mpvue使用的是Vue + HTML Web的开发方式开发小程序，它最终还是需要被转换成小程序的代码才可以在小程序环境运行，所以这里的自动编译的目的就是要把Web代码编译成小程序代码。这里编译之后我们的代码就会被编译到dist目录下，我们后面运行的以及发布的代码都只是编译后的dist目录下的代码。



因为微信小程序要在 微信开发工具 上预览，所以我们打开一下开发工具预览刚启动的项目。项目目录选择文件夹中的 dist 目录（新版本的mpvue是dist/wx目录, 在project.config.json文件可以修改）





3、文件结构
以下的文件目录结构应该和你刚创建的项目目录结构有点差异，但是大致上的东西都是一致的，只不过是加了一些几个文件夹区分不用文件的作用，或者增加一些配置文件方便后面项目管理。

(1)package.json文件

package.json是项目的主配置文件，里面包含了mpvue项目的基本描述信息、项目所依赖的各种第三方库以及版本信息、以及可执行的脚本信息。

(2)static目录

static目录可以用于存放各种小程序本地静态资源，如图片、文本文件、公共样式、字体等。

(3)build目录

build目录下是一些用于项目编译打包的node.js脚本和webpack配置文件。一般情况下不需要修改这些文件。

(4)src目录

src目录是我们主要进行小程序功能编写的地方。src下有多个文件夹以及文件，这里分别说明一下；

component：存放组件的目录，小程序里面如果有些地方复用多次（如：列表）可以抽出来作为一个组件重复使用，组件放在此目录。

core（可选）：后面创建的文件夹，用于存放一些核心文件，如ajax，也可以不需要此文件夹，可以把文件放到util文件夹下。

util（可选）：用于存放一些公共的js，以便引用。

model（可选）：用于存放一些公共的模块，如：小程序登录模块，小程序播放音频模块（需要的话）等；

pages：存放小程序页面的目录。每一个页面对应里面的一个文件夹，每个文件夹包含3个文件index.vue、main.js、main.json；其中main.json是用于配置当前页面的头部（顶部标题、颜色等，以及需不需要下拉刷新上拉加载）



main.js + App.vue：这两个是入口文件，相当于原生小程序框架中的app.json和app.js的复合体，可以在main.js里面写一些（或者引入）通用的方法例如时间戳转换等，或者也可以在App.vue里引入公共的样式。



4、程序入口
首先我们先对我们的项目作一个清理。

（1）删掉src/components、src/pages、src/utils三个目录下的所有代码文件

（2）将src/App.vue文件中的内容重置成：



（3）将src/main.js文件中的内容重置成：





（4）将src/app.json文件中内容重置成



程序入口

一个mpvue框架的小程序， 入口应该包括以下几个部分

（1）App.vue + main.js

（2）app.json

（3）首页

（1）上面可以看到App.vue 和 main.js

（2）app.json，在pages中加入你的入口首页的路径



（3）首页，每一个mpvue的页面都有以下结构。



文件夹名字可以视为该页面的名字，index.vue用于实现功能，main.js用于将这个页面组件生成Vue实例并加载，main.json是页面头部的配置（不需要可以不加，注意：有些博客或者网站说可以把配置加在main.js中，但是实际上加在main.js并不生效，必须用这种方式去去加这个页面配置）。

main.js



index.vue，你的小程序的页面要实现什么就写在这里



main.json



到这里就写出了小程序的一个入口页面，后面需要什么页面的话就根据需要自行添加即可，组件方面的话跟vue挺相似的，需要用到的时候自己根据vue的方法去写即可。Vue 组件之间的通信。需要注意的是，每次新增页面都需要重新启动项目，而且有些标签元素在小程序中用不了，需要换成对应的标签，例如input的radio，select等都是用不了的。



5、框架的优化
其实完成以上的步骤这个框架也就算是可以用了吧。但是有些东西必须自己再封装一下什么的，这才比较符合我们的开发习惯。

（1）sass

一般来说，用了sass和less之后就会觉得他们这种写样式的方式会比较方便。这里mpvue也为我们提供了sass，但是需要我们自己添加对sass的支持才可以使用。

添加对sass的支持很简单，只需要手动安装node-sass 和 sass-loader 即可。

cnpm i sass-loader node-sass --save

使用的时候只要在style后加上属性 lang=”scss“ 即可



（2）ajax

小程序的请求使用的是小程序提供的 wx.request 方法，这里我们对这个方法做一个封装，方便我们作一些统一的处理，例如： 统一在请求的时候加上某参数，统一的错误处理方式，统一的域名端口（方便统一修改），统一的加载动画等等。

首先创建config.js文件，用于存放域名配置。



根据需要对ajax请求封装



使用的时候只要引入该ajax文件即可调用里面的方法，或者在整个小程序的入口main.js中引入该方法。



（3）其他

其他方面如，页面跳转，消息提示等都可以自己封装起来，如果觉得没必要也可以直接用微信小程序原生的方法， 如页面跳转用 wx.navigateTo 方法。这里用  this.$root.$mp.query 即可获取页面跳转携带的参数。

总之，框架是是一个协助我们开发的工具，觉得有利于开发的都可以整合到框架中去。加快开发速度。


# mpvue使用中需要注意的问题
1、事件处理
例如：

微信小程序登录时需要获取用户授权，获取授权时我们可能需要在回调里面做某些事情，于是我们的HTML代码可能会这样写：

<button open-type="getUserInfo" bindgetuserinfo="callback" lang="zh_CN">获取权限</button>

然后，你会发现并没有什么用。准确来说是没有bindgetuserinfo。在mpvue中，微信小程序里面的事件大部分都被“换了名字”，

如 tap 事件换成我们熟悉的click，一些微信小程序独有的事件只需要把 bind 改为 @ 即可。



2、表单元素
mpvue不支持表单元素radio，即： <input type="raido" />

也不支持select元素。

表单元素必须用 radio-group 组件代替，例如：
<radio-group class="radio-group" @change="radioChange">
        <label class="radio" >
                <radio :value="1" :checked="true"/> 1
        </label>

        <label class="radio" >
                <radio :value="2" :checked="false"/> 2
        </label>
</radio-group>



select 组件用 picker 组件进行代替，例如：
<template>

        <div>
                <picker @change="bindPickerChange" :value="index" :range="array">
                        <view class="picker">
                                当前选择：{{array[index]}}
                        </view>
                </picker>
        </div>
</template>

<script>
export default {
        data () {
                return {
                        index: 0,
                        array: ['A', 'B', 'C']
                }
        },
        methods: {
                bindPickerChange (e) {
                        console.log(e)
                }
        }
}

</script>
