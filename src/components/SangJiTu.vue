<template>
  <div class="SangJiTu">
    <div id="SangJiTU"></div>
  </div>
</template>

<script>
import * as echarts from "echarts/core";
import { SankeyChart } from "echarts/charts";
import { TitleComponent, TooltipComponent } from "echarts/components";
import { CanvasRenderer } from "echarts/renderers";

// 注册必要的组件
echarts.use([SankeyChart, TitleComponent, TooltipComponent, CanvasRenderer]);

export default {
  name: "SangJiTu",
  props: {
    sankeyData: Object, // 从父组件传递过来的桑基图数据
  },
  watch: {
    sankeyData(newData) {
      if (newData && newData.nodes.length > 0) {
        this.updateChart(newData); // 当数据变化时更新桑基图
      } else {
        this.showNoDataMessage(); // 没有数据时显示提示信息
      }
    },
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chartDom = document.getElementById("SangJiTU");
      this.myChart = echarts.init(chartDom);
    },

    // 更新桑基图
    updateChart(sankeyData) {
      const option = {
        tooltip: {
          trigger: "item",
          formatter: params => {
            if (params.dataType === "node") {
              return `地点: ${params.name}<br>类别: ${params.data.category}`;
            }
            return `从: ${params.data.source}<br>到: ${params.data.target}<br>值: ${params.data.value}`;
          },
        },
        series: [
          {
            type: "sankey",
            layout: "none", // 禁用自动布局
            data: sankeyData.nodes,
            links: sankeyData.links,
            emphasis: {
              focus: "adjacency",
            },
            layoutIterations: 64, // 增加布局优化次数
            nodeAlign: "justify", // 节点左对齐
            levels: [
              { depth: 0, itemStyle: { color: "#e1d5e7" } }, // 第一列
              { depth: 1, itemStyle: { color: "#d5e8d4" } }, // 第二列
              { depth: 2, itemStyle: { color: "#dae8fc" } }, // 第三列
              { depth: 3, itemStyle: { color: "#ffe6cc" } }, // 第四列
            ],
            top: "8%",
            lineStyle: {
              color: "source",
              opacity: 0.6,
            },
          },
        ],
        graphic: [
          // 添加色块和标题
          {
            type: "rect",
            left: "5%",
            shape: {
              width: 20,
              height: 20,
            },
            style: {
              fill: "#e1d5e7", // 第一列的颜色
            },
          },
          {
            type: "text",
            left: "7%",
            top: "0.35%",
            style: {
              text: "始祖原居地",
              fontSize: 16,
              fontWeight: "bold",
            },
          },
          {
            type: "rect",
            left: "29.5%",
            shape: {
              width: 20,
              height: 20,
            },
            style: {
              fill: "#d5e8d4", // 第二列的颜色
            },
          },
          {
            type: "text",
            left: "31.5%",
            top: "0.35%",
            style: {
              text: "始祖迁居地",
              fontSize: 16,
              fontWeight: "bold",
            },
          },
          {
            type: "rect",
            left: "54%",
            shape: {
              width: 20,
              height: 20,
            },
            style: {
              fill: "#dae8fc", // 第三列的颜色
            },
          },
          {
            type: "text",
            left: "56%",
            top: "0.35%",
            style: {
              text: "始迁祖原居地",
              fontSize: 16,
              fontWeight: "bold",
            },
          },
          {
            type: "rect",
            left: "78.5%",
            shape: {
              width: 20,
              height: 20,
            },
            style: {
              fill: "#ffe6cc", // 第四列的颜色
            },
          },
          {
            type: "text",
            left: "80.5%",
            top: "0.35%",
            style: {
              text: "始迁祖迁居地",
              fontSize: 16,
              fontWeight: "bold",
            },
          },
        ],
      };

      this.myChart.setOption(option);
    },

    // 没有数据时显示提示信息
    showNoDataMessage() {
      const option = {
        title: {
          text: "该省份下该姓氏暂无迁徙数据",
          left: "center",
          top: "center",
          textStyle: {
            fontSize: 18,
            fontWeight: "bold",
            color: "#666",
          },
        },
      };
      this.myChart.setOption(option);
    },
  },
};
</script>

<style scoped>
.SangJiTu {
  width: 100%;
  text-align: center;
}
#SangJiTU {
  width: 100%;
  height: 700px;
}
</style>
