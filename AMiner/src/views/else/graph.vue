<template>
	<div class="page-area">
		<div class="bread-crumb">
			<el-breadcrumb separator=">>">
				<el-breadcrumb-item><span class="el-icon-menu"></span> 其它</el-breadcrumb-item>
				<el-breadcrumb-item>树形组件</el-breadcrumb-item>
			</el-breadcrumb>
		</div>
		<div class="page-body">
            <div class="dataSearch">
                <el-card class="box-card">
                    <el-row>
                        <el-col :span="12">在线智搜</el-col>
                        <el-col :span="12">
                            <el-col :span="12">
                            <el-input v-model="input" placeholder="请输入内容"></el-input>
                            </el-col>
                            <el-col :span="12">
                                <el-button round @click="search">搜索</el-button>
                            </el-col>
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
                            <div id="main" style="width: 600px;height:400px;"></div>
                        </el-card>
                    </el-col>
                </el-row>
            </div>
        </div>
	</div>
</template>
<script>
import * as echarts from 'echarts';
	export default {
		data () {
			return {
                nodeList: [],
                linkList: [],
                nodeData:[],
                linkData:[],
				input: '',
                authorName: 'Rajeevan Chandel',//'Jianbo Liu',
			}
		},
        mounted:function(){
            this.getData();
        },
        methods: {
            search() {
                this.getGraph();
            },
            getData() {
                this.$axios({
          method:"get",
          url: 'http://localhost:9999/BI/queryEntityByAuthorName',
          params:{
            EntityType: "Author",
            searchContent: this.authorName,
            limit: 20
          }
        }).then(res=>{
          console.log(res);

          this.nodeList = res.data.nodes;
          this.linkList = res.data.relations;
          console.log(this.nodeList);
          console.log(this.linkList);
          console.log("lalalal");
          console.log(this.nodeList[19].properties.AffiliationId);
          for( var i =0;i<this.nodeList.length;i++) {
            //先判断label
            if (this.nodeList[i].properties.label[0] == "Author") {
                // Author
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.authorName,
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.id,
                symbolSize: 70,//节点大小
                category: 0,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Subject") {
                // Subject
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.researchInterest,
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.id,
                symbolSize: 70,//节点大小
                category: 1,//设置节点所属类别
                })
            }else if (this.nodeList[i].properties.label[0] == "Paper") {
                //Paper
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.title,
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.id,
                symbolSize: 70,//节点大小
                category: 2,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Affiliation") {
                //Affiliation
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.AffiliationId,
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.id,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            }
            // this.nodeData.push({
            //     name: this.nodeList[i].authorName,
            //     des: this.nodeList[i].authorName,
            //     symbolSize: 70,//节点大小
            //     category: 0,//设置节点所属类别
            // })
          }
          for( var i = 0;i<this.linkList.length;i++) {
            this.linkData.push({
                // name: this.nodeList[i].AffiliationId,
                // des: this.nodeList[i].id,
                // symbolSize: 70,//节点大小
                // category: 3,//设置节点所属类别
                source: ""+this.linkList[i].properties.source,//源节点
                target: ""+this.linkList[i].properties.target,//目标节点
                name: this.linkList[i].properties.label,//关系
                des: this.linkList[i].properties.id
                })
          }
        },err=>{
          console.log(err);
        })
            },

         getGraph() {
            var myChart = echarts.init(document.getElementById('main'));
            var categories = [{name:"Author"},{name:"Subject"},{name:"Paper"},{name:"Affiliation"}];
            var option = {
        // 图的标题
        title: {
            text: '关联关系&实体图'
        },
        // 提示框的配置
        tooltip: {
            formatter: function (x) {
                return x.data.des;
            }
        },
        // 工具箱
        toolbox: {
            // 显示工具箱
            show: true,
            feature: {
                mark: {
                    show: true
                },
                // 还原
                restore: {
                    show: true
                },
                // 保存为图片
                saveAsImage: {
                    show: true
                }
            }
        },
        legend: [{
            // selectedMode: 'single',
            //设置可以根据类别显示or隐藏节点
            data: categories.map(function (a) {
                return a.name;
            })
        }],
        series: [{
            type: 'graph', // 类型:关系图
            layout: 'force', //图的布局，类型为力导图
            symbolSize: 40, // 调整节点的大小
            roam: true, // 是否开启鼠标缩放和平移漫游。默认不开启。如果只想要开启缩放或者平移,可以设置成 'scale' 或者 'move'。设置成 true 为都开启
            edgeSymbol: ['circle', 'arrow'],
            edgeSymbolSize: [2, 10],
            edgeLabel: {
                normal: {
                    textStyle: {
                        fontSize: 20
                    }
                }
            },
            force: {
                repulsion: 2500,
                edgeLength: [10, 50]
            },
            draggable: true,
            lineStyle: {
                normal: {
                    width: 2,
                    color: '#4b565b',
                }
            },
            edgeLabel: {
                normal: {
                    show: true,
                    formatter: function (x) {
                        return x.data.name;
                    }
                }
            },
            label: {
                normal: {
                    show: true,
                    textStyle: {}
                }
            },
 
            // 数据
            data: this.nodeData,//...后续数据省略
            links: this.linkData, //定义关系，后续省略
            categories: categories,//给类别赋值
           }]
        };
        myChart.setOption(option);
         }
	}
}
</script>
<style scoped>
/* #main {
    width: 100%;
    height: 100%;
} */
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
</style>
