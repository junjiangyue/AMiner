<template>
	<div class="page-area">
		<div class="bread-crumb">
			<el-breadcrumb separator=">>">
				<el-breadcrumb-item><span class="el-icon-menu"></span> 其它</el-breadcrumb-item>
				<el-breadcrumb-item>邻域查询</el-breadcrumb-item>
			</el-breadcrumb>
		</div>
		<div class="page-body">
      <div class="dataSearch">
        <el-card class="box-card">
          <el-row>
            <el-col :span="3">邻域查询</el-col>
            <el-col :span="8">
              请选择查询对象：<el-select v-model="value" placeholder="请选择查询对象">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="8">
              <el-col :span="9">
                请输入查询邻域：
              </el-col>
              <el-col :span="15">
                <el-input v-model="input" placeholder="请输入查询邻域"></el-input>
              </el-col>
            </el-col>
            <el-col :span="5">
              <el-button class="mar-left">查询</el-button>
            </el-col>
          </el-row>
        </el-card>
        <el-row>
          <el-col :span="5">
            <el-card class="information">
            </el-card>
          </el-col>
          <el-col :span="19">
            <el-card class="graph">
            </el-card>
          </el-col>
        </el-row>
      </div>
    </div>
	</div>
</template>
<script>
	export default {
		data () {
			return {
				input: '',
        authorName: 'Bob Jolls',
            // 防止出现多个echarts初始化的情况
        myChart: '',
        options: [{
          value: '选项1',
          label: '关键作者'
        }, {
          value: '选项2',
          label: '关键单位'
        }, {
          value: '选项3',
          label: '关键期刊'
        }, {
          value: '选项4',
          label: '关键会议'
        }],
        value: ''
			}
		},
        mounted:function(){
            this.getData();
        },
        methods: {
            getData() {
                this.$axios({
          method:"get",
          url: 'http://localhost:3000/getCooperationByName',
          params:{
            authorName:this.authorName
          }
        }).then(res=>{
          console.log(res);
        },err=>{
          console.log(err);
        })
            },

            // 绘制知识图谱
        getGraph(p1, p2) {
            // 若已经存在有初始化了的echarts实例，就直接进行绘制
            if(this.myChart) {
                this.configGraph(p1, p2);
            }else {
                // 没有初始化的echarts实例，就初始化一个
                this.myChart = this.$echarts.init(this.$refs.graph);
                this.configGraph(p1, p2);
            }
            
        },

            // 绘制知识图谱的配置项
            configGraph(p1, p2) {
            // 保存传进来的节点和关系数据
            let mydata = p1;
            let links = p2;
            
            // 图谱的配置项
            let option = {
                    // 提示框的配置
                    tooltip: {
                        trigger: 'item'//设置提示框的内容和格式 节点和边都显示name属性
                    },
                    //图形上的文本标签，可用于说明图形的一些数据信息
                    label: {
                        fontSize: 12
                    },
                    legend: {
                        x: "center",
                        show: true
                    },
                    series: [
                        {
                            type: 'graph',// 类型:关系图
                            layout: 'force',//图的布局，类型为力导图
                            symbolSize: 50,//节点大小
                            emphasis: {
                                focus: 'adjacency'
                            },//当鼠标移动到节点上，突出显示节点以及节点的边和邻接节点
                            draggable: true,//指示节点是否可以拖动
                            roam: true,
                            // 两端的样式（无 / 箭头）
                            edgeSymbol: ['none', 'arrow'],
                            // 不同节点的颜色之类的配置
                            categories: [{
                                name: '电影',
                                itemStyle: {
                                        color: "lightgreen"
                                }
                            }, {
                                name: '主演',
                                itemStyle: {
                                        color: "orange",
                                }
                            }, {
                                name: '类型',
                                itemStyle: {
                                        color: "pink",
                                }
                            }, {
                                name: '导演',
                                itemStyle: {
                                        color: "lightblue",
                                }
                            },{
                                name: 'TMDbID',
                                itemStyle: {
                                        color: "#fcce4c",
                                }
                            }],
                            // 节点上的文字
                            label: {
                                    show: true,
                                    fontSize: 12,
                                    color: "black",
                            },
                            force: {
                                repulsion: 1200,//节点之间的斥力因子。支持数组表达斥力范围，值越大斥力越大。
                                gravity: 0.1, //节点受到的向中心的引力因子。该值越大节点越往中心点靠拢。
                            },
                            edgeSymbolSize: [4, 6], // 边两端的标记(箭头)大小，可以是一个数组分别指定两端，也可以是单个统一指定。
                            // 边上显示的文字
                            edgeLabel: {
                                    show: true,
                                    fontSize: 12,
                                    formatter: "{c}"
                            },
                            // 节点数据
                            data: mydata,
                            // 关系数据
                            links: links,
                            // 边的样式
                            lineStyle: {
                                    opacity: 0.9,
                                    width: 1.1,
                                    curveness: 0,
                                    color: "#262626",
                            }
                        }
                    ]
            };

            //节点自定义拖拽不回弹
            const chart = this.myChart;
            chart.on('mouseup', function (params) {
                var option = chart.getOption();
                option.series[0].data[params.dataIndex].x = params.event.offsetX;
                option.series[0].data[params.dataIndex].y = params.event.offsetY;
                option.series[0].data[params.dataIndex].fixed = true;
                chart.setOption(option);
            });

            // 使用刚指定的配置项和数据显示图表。
            this.myChart.setOption(option);
        }
	}
}
</script>
<style scoped>
.dataSearch {
    /* background-color: brown; */
    width: 100%;
    height: 100px;
}
  .text {
    font-size: 14px;
  }

  .item {
    padding: 18px 0;
  }

  .box-card {
    width: 100%;
    height: 100px;
  }
  .information {
      width: 100%;
    height: 500px;
  }
  .graph {
      width: 100%;
    height: 500px;
  }
  .mar-left {
    margin-left: 30px;
  }
</style>
