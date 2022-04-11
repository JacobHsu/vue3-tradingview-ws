<template>
  <div class="kline">
    <div id="tv_chart_container" />
  </div>
</template>
<script>
  import { DataFeed, widget as TvWidget } from 'tradingview-api'
  import { onMounted, ref, toRefs } from 'vue'
  import { getKlineHistory } from '@/api'
  import { intervalMap, supported_resolutions } from '@/utils/hardCode'
  import { ws } from '@/utils/socket'
  import BB from "../studys/BB";
  import EMA from "../studys/EMA";

  // 切换到tv的iframe可通过 window.JSServer.studyLibrary 查看tv所有内置指标详情
  /**
   * 默认展示指标 Array<[指标名称, [指标入参]]>
   */
  const defaultStudy = [
    ["myBB", [20, 2]],
    ["myEMA", [9, 26]],
    ["MACD", [12, 26, "close", 9]],
  ];

  export default {
    name: 'KLineWidget',
    props: {
      symbolInfo: {
        type: Object,
        required: true,
      },
      symbol: {
        type: String,
      },
    },
    setup(props) {
      const widget = ref(null)
      const interval = ref('5min')
      const { symbol, symbolInfo } = toRefs(props)
      /** 訂閱websocket */
      const subscribeKLine = () => {
        ws.subscribe(
          `market.${symbol.value.toLocaleLowerCase()}.kline.${interval.value}`,
          {
            id: 'react-tv',
            sub: `market.${symbol.value.toLocaleLowerCase()}.kline.${
              interval.value
            }`,
          },
          (data) => {
            const tick = data.tick
            datafeed.value.updateKLine({
              time: tick.id * 1000,
              open: tick.open,
              high: tick.high,
              low: tick.low,
              close: tick.close,
              volume: tick.vol,
            })
          }
        )
      }
      /** websocket取消訂閱 */
      const unsubscribeKLine = () => {
        ws.unsubscribe(`market.${symbol.value}.kline.${interval.value}`)
      }
      /** 取得數據 訂閱數據 */
      const getBars = async (params) => {
        try {
          console.log('params: ', params)
          const size = window.innerWidth
          // 是否第一次請求歷史數據
          if (!params.firstDataRequest) {
            return {
              bars: [],
              meta: {
                noData: true,
              },
            }
          }
          if (params.resolution !== intervalMap[interval.value]) {
            unsubscribeKLine()
            for (let key in intervalMap) {
              if (intervalMap[key] === params.resolution) {
                interval.value = key
              }
            }
          }

          // do api get history
          const [list] = await getKlineHistory({
            symbol: symbol.value.toLocaleLowerCase(),
            period: interval.value,
            size: size > 2000 ? 2000 : size,
          })

          console.log('list: ', list)

          if (
            params.resolution === intervalMap[interval.value] &&
            params.firstDataRequest &&
            list
          ) {
            subscribeKLine()
          }

          if (!list) {
            return {
              bars: [],
              meta: { noData: true },
            }
          }
          return {
            bars: list,
            meta: {
              noData: !list.length,
            },
          }
        } catch (error) {
          console.log('getBars error:', error)
        }
      }
      /** 配置trading-view */
      const resolveSymbol = () => {
        return new Promise((resolve) => {
          const display_name = symbol.value.replace('USDT', '/USDT')
          resolve({
            name: display_name,
            full_name: display_name,
            description: symbol.value,
            type: symbol.value,
            session: '24x7',
            exchange: 'HuoBi',
            listed_exchange: symbol.value,
            timezone: 'Asia/Shanghai',
            format: 'price',
            pricescale: Math.pow(10, symbolInfo.value['price-precision']),
            minmov: 1,
            volume_precision: symbolInfo.value['value-precision'],
            has_intraday: true,
            supported_resolutions: supported_resolutions,
          })
        })
      }
      const datafeed = ref(
        new DataFeed({
          getBars: (params) => getBars(params),
          fetchResolveSymbol: () => resolveSymbol(),
          fetchConfiguration: () => {
            return new Promise((resolve) => {
              resolve({
                supported_resolutions,
              })
            })
          },
        })
      )
      /** 初始化trading-view */
      const display_name = symbol.value.replace('USDT', '/USDT')
      const initTradingView = () => {
        widget.value = new TvWidget({
          fullscreen: true,
          symbol: display_name,
          interval: intervalMap[interval.value],
          container_id: 'tv_chart_container',
          datafeed: datafeed.value,
          library_path: '/charting_library/',
          locale: 'zh',
          theme: "Light", // 'Dark',
          timezone: 'Asia/Shanghai',
          disabled_features: [
            'volume_force_overlay', // 成交量與k線分離
            "header_resolutions",
            "header_compare",
            "header_undo_redo",
          ],
          enabled_features: [
            'hide_left_toolbar_by_default'
          ],
          overrides:{
            'volumePaneSize': 'medium', //成交量高度設置，可選值 large, medium, small, tiny
          },
          custom_indicators_getter: function (PineJS) {
            return Promise.resolve([BB(PineJS), EMA(PineJS)]);
          },          
        })

        widget.value.onChartReady(function () {
          const chart = widget.value.chart();
          // 先清除已创建指标
          // chart.removeAllStudies(); // 删除全部指标 包含成交量
          for (let item of defaultStudy) {
            chart.createStudy(item[0], false, true, item[1]);
          }
        });
      }
      const setSymbol = (newSymbol) => {
        unsubscribeKLine()
        symbol.value = newSymbol
        widget.value?.setSymbol(
          newSymbol.toLocaleUpperCase(),
          intervalMap[interval.value],
          () => {
            console.log('------setSymbol---------', symbol.value)
          }
        )
      }
      onMounted(() => {
        initTradingView()
      })

      return {
        widget,
        setSymbol,
      }
    },
  }
</script>
