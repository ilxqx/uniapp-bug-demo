# Bug 复现步骤

## 1. Clone 项目到本地
## 2. 安装依赖

```shell
npm i
```

## 3. 运行项目

```shell
npm run dev:mp-weixin
```

**注意：** 此时你会发现编译巨慢，在我机器上要编译27秒左右：

```log
/Users/venus/.nvm/versions/node/v20.10.0/bin/npm run dev:mp-weixin

> uni-preset-vue@0.0.0 dev:mp-weixin
> uni -p mp-weixin

请注意运行模式下，因日志输出、sourcemap 以及未压缩源码等原因，性能和包体积，均不及发行模式。若要正式发布，请点击发行菜单或使用 cli 发布命令进行发布
正在编译中...
DONE  Build complete. Watching for changes...
运行方式：打开 微信开发者工具, 导入 dist/dev/mp-weixin 运行。
ready in 27491ms.
```

## 4. 修改代码

打开 `src/pages/index/index.vue`，修改代码如下：

```vue
<template>
  <view class="content">
    <!-- 注释掉此行 -->
    <!-- <tui-icon name="arrowdown" unit="rpx" :size="40" color="#666666" /> -->
    <image class="logo" src="/static/logo.png"></image>
    <view class="text-area">
      <text class="title">{{ title }}</text>
    </view>
  </view>
</template>

<script>
  // 注释掉此行
  // import TuiIcon from "../../components/thorui/tui-icon/tui-icon.vue";

  export default {
    // 注释掉此行
    // components: { TuiIcon },
    data() {
      return {
        title: 'Hello',
      }
    },
    onLoad() {},
    methods: {},
  }
</script>

<style>
  .content {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .logo {
    height: 200rpx;
    width: 200rpx;
    margin-top: 200rpx;
    margin-left: auto;
    margin-right: auto;
    margin-bottom: 50rpx;
  }

  .text-area {
    display: flex;
    justify-content: center;
  }

  .title {
    font-size: 36rpx;
    color: #8f8f94;
  }
</style>
```

注释掉 tui-icon 组件调用的那一行之后，再次编译，就会发现速度极快，重新回到正轨。

```log
/Users/venus/.nvm/versions/node/v20.10.0/bin/npm run dev:mp-weixin

> uni-preset-vue@0.0.0 dev:mp-weixin
> uni -p mp-weixin

请注意运行模式下，因日志输出、sourcemap 以及未压缩源码等原因，性能和包体积，均不及发行模式。若要正式发布，请点击发行菜单或使用 cli 发布命令进行发布
正在编译中...
DONE  Build complete. Watching for changes...
运行方式：打开 微信开发者工具, 导入 dist/dev/mp-weixin 运行。
ready in 1190ms.
```

> **总结：** 可以确定是因为引入了 `tui-icon` 组件导致编译速度变慢。
至于具体原因，我查看了 `tui-icon` 组件的源码（具体可查看 `src/components/thorui/tui-icon/tui-icon.vue` 文件），并未发现特殊之处，所以我猜测可能是 `uni-app` 的问题。
