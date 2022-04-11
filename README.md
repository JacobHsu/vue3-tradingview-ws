# vue3-tradingview-ws

[tradingview-web-socket](https://github.com/472647301/tradingview-web-socket) vue3-javascript-demo  

tradingview-web-socket/[charting_library](https://github.com/472647301/tradingview-web-socket/tree/master/charting_library)  

`cp -R ../charting_library ./public`  

npm [tradingview-api](https://github.com/472647301/tradingview-web-socket/tree/master/tradingview-api)

## tradingview

initTradingView

```js
const initTradingView = () => {
    widget.value = new TvWidget({
        ...
        disabled_features: [
        'volume_force_overlay', // 成交量與k線分離
        ],
        enabled_features: [
        'hide_left_toolbar_by_default'
        ],
        overrides:{
        'volumePaneSize': 'medium', //成交量高度設置，可選值 large, medium, small, tiny
        }
    })
}
```

[featuresets](https://tradingview.gitee.io/featuresets/)
TradingView 中文开发文档 [功能集](https://www.lhsz.xyz/read/tradingViewWikiCn/book-Featuresets.md) [volume_force_overlay](https://zlq4863947.gitbook.io/tradingview/4-tu-biao-ding-zhi/customization-overview)  
[自定义UI控件](https://www.kancloud.cn/isung/tradingview/972226) widget.headerReady() createButton

### 技術指標

[布林帶(BB)](https://tw.tradingview.com/scripts/bollingerbands/) [studys](https://github.com/472647301/tradingview-web-socket/tree/master/react-typescript-demo/src/studys)

## element-plus

Element Plus [On-demand Import](https://element-plus.org/en-US/guide/quickstart.html#on-demand-import)  
element-plus [select](https://element-plus.org/zh-CN/component/select.html)  


## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
