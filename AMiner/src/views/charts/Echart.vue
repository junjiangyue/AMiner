<template>
	<div class="chart-area">
		 <div class="crumbs">
            <el-breadcrumb separator=">>">
                <el-breadcrumb-item><i class="el-icon-star-on"></i> 图表</el-breadcrumb-item>
                <el-breadcrumb-item>vue-echart</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div id="myChart" style="width: 600px;height:400px;"></div>
	</div>
</template>

<script type="text/javascript">
	export default {
		data () {
			return {
				source:{}
			}
		},
		methods:{
			 drawChart() {
			let echarts = require("echarts");
     
			// 基于准备好的dom，初始化echarts实例
			let myChart = echarts.init(document.getElementById("myChart"));
			// 指定图表的配置项和数据
			let option = {
				title: {
				text: "ECharts 入门示例"
				},
				tooltip: {},
				legend: {
				data: ["销量"]
				},
				xAxis: {
				data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
				},
				yAxis: {},
				series: [
				{
					name: "销量",
					type: "bar",
					data: [5, 20, 36, 10, 10, 20]
				}
				]
			};
			// 使用刚指定的配置项和数据显示图表。
			myChart.setOption(option);
			}
		},
		mounted() {
			this.drawChart();
		}
	}
</script>

<style type="text/css" scoped>
	.chart-show{
		margin-top: 20px;
		border:2px solid #324157;
		border-radius: 5px;
	}
	.chart-item{
        width: 600px;
        display: inline-block;
        margin-left: 50px;
    }
    .chart-item-title{
        font-weight: 400;
        line-height: 50px;
        margin: 10px 0;
        font-size: 22px;
        color: #1f2f3d;
        text-align: center;
    }
</style>