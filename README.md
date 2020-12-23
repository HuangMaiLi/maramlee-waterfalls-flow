# maramlee-waterfalls-flow 使用

**注意：期待已久的 maramlee-waterfalls-flow-nav 插件更新啦，如果需要 nav 切换的样式，请移步 maramlee-waterfalls-flow-nav 获取插件。**

waterfalls-flow 是一个瀑布流插件，简单易用。

前段时间做项目中需要使用到瀑布流，但找了一圈，都没有找到合适的瀑布流插件，很多都是直接通过标签分列，治标不治本。

所以自己动手写了这个插件，可以配置多列（默认 2 列，最少 2 列，除非故意太多列影响渲染，按理讲最多可以无限多）。适用于 h5 端、app 端、微信小程序端，这几个端是经过我反复测试再发布，并使用在项目中，一般没啥 bug，但其他端没测试，可以自行测试，有什么问题留言给我。

此瀑布流可为纯图片瀑布流，也可在图片下方自定义其他内容，内容高度是自适应的，可以随意添加、布局内容。

利用 vue 的特性避免重复渲染，每次加载数据除非本身 http 请求时间长影响外，每次渲染只有新增的数据渲染，已经渲染的数据不会重复再次渲染影响性能。所以，不会因为数据加载越来越多而渲染越来越慢。

我自己的项目是用 ts 写的，不知道有没有需要 ts 版本的，我觉得可能用 ts 占少数，如果需要的话，也可以联系我。

## 使用方式

### 在 `script` 中引用组件

```
复制代码import waterfallsFlow from "@/components/maramlee-waterfalls-flow/maramlee-waterfalls-flow.vue";
export default {
  components: { waterfallsFlow },
};
```

### 在 `template` 中使用组件

#### 可以只是渲染图片，不需要其他

```
复制代码<template>
  <waterfallsFlow :single="true" :list="list" />
</template>
```

#### 有插槽（自定义内容）的情况要分情况使用

##### 若只适配 app、h5 端，利用作用域插槽

注意：`item` 包含 `list` 对应的数据项，可以随意搭配、自定义使用。

```
复制代码<template>
  <waterfallsFlow :list="list">
    <template v-slot:default="item">
      <!-- 此处添加插槽内容 -->
      <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view> -->
    </template>
  </waterfallsFlow>
</template>
```

##### 若只适配微信小程序，利用小程序插槽

注意：微信小程序没有动态模板，也和 vue 的插槽使用方式不一样

```
复制代码<template>
  <waterfallsFlow :list="list">
    <view v-for="(item, index) of list" :key="index" slot="slot{{index}}">
      <!-- 自定义内容 -->
      <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view> -->
    </view>
  </waterfallsFlow>
</template>
```

##### 若需要同时兼容 app、h5、微信小程序，则需条件渲染

```
复制代码<template>
  <view class="content">
    <waterfallsFlow :list="list">
      <!--  #ifdef  MP-WEIXIN -->
      <!-- 微信小程序自定义内容 -->
      <view v-for="(item, index) of list" :key="index" slot="slot{{index}}">
        <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view>-->
      </view>
      <!--  #endif -->

      <!-- #ifndef  MP-WEIXIN -->
      <!-- app、h5 自定义内容 -->
      <template v-slot:default="item">
        <!-- <view class="cnt">
          <view class="title">{{item.title}}</view>
          <view class="text">{{item.text}}</view>
        </view> -->
      </template>
    </waterfallsFlow>
    <!-- #endif -->
  </view>
</template>
```

## 属性说明

| 属性名      | 类型    | 默认值    | 是否必传 | 说明                                     | 平台支持         |
| ----------- | ------- | --------- | -------- | ---------------------------------------- | ---------------- |
| list        | Array   | -         | 是       | 渲染的列表                               | 全               |
| offset      | Number  | 10        | 否       | 单位是 px                                | 全               |
| idKey       | String  | id        | 否       | 列表渲染的 key 的键名，值必须唯一        | 全               |
| imageSrcKey | String  | image_url | 否       | 图片 src 的键名                          | 全               |
| cols        | Number  | 2         | 否       | 列数，值必须不小于 2                     | 全               |
| single      | Boolean | false     | 否       | 是否是单独的渲染图片，只控制图片圆角而已 | 全               |
| listStyle   | Object  | -         | 否       | 单个展示项的样式                         | 微信小程序不支持 |
| imageStyle  | Object  | -         | 否       | 图片的样式                               | 全               |

## 事件说明

| 事件名       | 说明             | 返回值       |
| ------------ | ---------------- | ------------ |
| @wapper-lick | 单项点击事件     | 单项对应数据 |
| @image-click | 图片点击事件     | 单项对应数据 |
| @image-load  | 图片渲染完成触发 | -            |

## 组件方法

| 方法名  | 说明                 | 参数 | 使用场景       |
| ------- | -------------------- | ---- | -------------- |
| refresh | 刷新数据，数据初始化 | -    | 页面下拉刷新等 |

### refresh 使用示例

使用部分代码样例

```
复制代码<template>
  <waterfallsFlow ref="waterfallsFlow" :list="list">
    <template v-slot:default="item"> <!-- ... --> </template>
  </waterfallsFlow>
</template>

import waterfallsFlow from "@/components/maramlee-waterfalls-flow/maramlee-waterfalls-flow.vue";
export default {
  components: { waterfallsFlow },
  onPullDownRefresh() {
    this.$refs.waterfallsFlow.refresh();
    // 重新获取渲染列表
  },
};
```# maramlee-waterfalls-flow
