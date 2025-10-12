<template>
  <VChart class="chart" :option="option" />
</template>

<script setup lang="ts">
import * as echarts from 'echarts/core';
import {
  DatasetComponent,
  DatasetComponentOption,
  TitleComponent,
  TitleComponentOption,
  TooltipComponent,
  TooltipComponentOption,
  GridComponent,
  GridComponentOption,
  TransformComponent
} from 'echarts/components';
import {
  ScatterChart,
  ScatterSeriesOption,
  LineChart,
  LineSeriesOption
} from 'echarts/charts';
import { UniversalTransition, LabelLayout } from 'echarts/features';
import { CanvasRenderer } from 'echarts/renderers';
import VChart, { THEME_KEY } from "vue-echarts";

echarts.use([
  DatasetComponent,
  TitleComponent,
  TooltipComponent,
  GridComponent,
  TransformComponent,
  ScatterChart,
  LineChart,
  CanvasRenderer,
  UniversalTransition,
  LabelLayout
]);

type EChartsOption = echarts.ComposeOption<
  | DatasetComponentOption
  | TitleComponentOption
  | TooltipComponentOption
  | GridComponentOption
  | ScatterSeriesOption
  | LineSeriesOption
>;

import ecStat from 'echarts-stat';
echarts.registerTransform(ecStat.transform.regression);
import { ref, provide } from "vue";

const option = ref({
  dataset: [
    {
      source: [
        [1, 4862.4],
        [2, 5294.7],
        [3, 5934.5],
        [4, 7171.0],
        [5, 8964.4],
        [6, 10202.2],
        [7, 11962.5],
        [8, 14928.3],
        [9, 16909.2],
        [10, 18547.9],
        [11, 21617.8],
        [12, 26638.1],
        [13, 34634.4],
        [14, 46759.4],
        [15, 58478.1],
        [16, 67884.6],
        [17, 74462.6],
        [18, 79395.7]
      ]
    },
    {
      transform: {
        type: 'ecStat:regression',
        config: {
          // method: 'exponential'
          // method: 'linear'
          method: 'polynomial'
          // 'end' by default
          // formulaOn: 'start'
        }
      }
    }
  ],
  title: {
    text: '1981 - 1998 gross domestic product GDP (trillion yuan)',
    subtext: 'By ecStat.regression',
    sublink: 'https://github.com/ecomfe/echarts-stat',
    left: 'center'
  },
  tooltip: {
    trigger: 'axis',
    axisPointer: {
      type: 'cross'
    }
  },
  xAxis: {
    splitLine: {
      lineStyle: {
        type: 'dashed'
      }
    }
  },
  yAxis: {
    splitLine: {
      lineStyle: {
        type: 'dashed'
      }
    }
  },
  series: [
    {
      name: 'scatter',
      type: 'scatter',
      datasetIndex: 0
    },
    {
      name: 'line',
      type: 'line',
      smooth: true,
      datasetIndex: 1,
      symbolSize: 0.1,
      symbol: 'circle',
      label: { show: true, fontSize: 16 },
      labelLayout: { dx: -20 },
      encode: { label: 2, tooltip: 1 }
    }
  ]
});

</script>

<style scoped>
.chart {
  height: 400px;
  width: 600px;  /* 必须指定width, 不然有时候因为组件载入速度导致宽度不对 */
}
</style>
