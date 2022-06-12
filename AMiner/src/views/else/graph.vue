<template>
	<div class="page-area">
		<div class="bread-crumb">
			<el-breadcrumb separator=">>">
				<el-breadcrumb-item><span class="el-icon-menu"></span> 基本查询</el-breadcrumb-item>
				<el-breadcrumb-item>图数据展示</el-breadcrumb-item>
			</el-breadcrumb>
		</div>
		<div class="page-body">
            <div class="dataSearch">
                <el-card class="box-card">
                    <el-row>
                        <el-col :span="4">在线智搜</el-col>
                        <el-col :span="10">
                            <el-col :span="10">
								<el-select v-model="form.paperType1" placeholder="请选择实体类型" clearable>
									<el-option :key="item.value" :value="item.value" :label="item.name" :disabled="item.disabled" v-for="item in paperType1"></el-option>
								</el-select>
                            </el-col>
                        </el-col>
                        <el-col :span="10">
                            <el-col :span="12">
                            <el-input v-model="authorName" placeholder="请输入内容"></el-input>
                            </el-col>
                            <el-col :span="12">
                                <el-button round @click="search">搜索</el-button>
                            </el-col>
                        </el-col>
                    </el-row>
                </el-card>
                <el-row>
                    <el-col :span="4">
                        <el-card class="information" >
							<div id="mytable" style="height:800px;">
							<div v-if="category!=-1" >
							<li :key="item.value" v-for="(key,item) in keys,tabledata">{{item}}:{{key}}</li>
							</div>
							</div>
                        </el-card>
                    </el-col>
                    <el-col :span="20">
                        <el-card class="graph">
                            <div id="main" style="width: 1000px;height:800px;"></div>
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
                category:-1,
                tabledata:{
					name: "",
					id:"",
					des: "",
					cn:0,
					hi:0,
					pc:0,
					pi:0,
					upi:0,
					symbolSize: 70,//节点大小
					category: 0,//设置节点所属类别
				},
                form:{
					paperType1:[]
				},
                paperType1:[
					{
						"name":"作者",
						"value":"Author"
					},{
						"name":"论文",
						"value":"Paper",
					},{
						"name":"研究机构/学校",
						"value":"Affiliation"
					},{
						"name":"研究主题",
						"value":"Subject"
					}
					,{
						"name":"会议/期刊",
						"value":"PublicationVenue"
					}
				],
                nodeList: [],
                linkList: [],
                nodeData:[],
                linkData:[],
				input: '',
                authorName: '',//'Rajeevan Chandel',//'Jianbo Liu',
			}
		},
        mounted:function(){

        },
        methods: {
            search() {
                if(this.form.paperType1=="Author") {
                    this.getDataAuthor();
                } else if (this.form.paperType1=="Paper") {
                    console.log("teststets")
                    this.getDataPaper();
                } else if (this.form.paperType1=="Affiliation") {//机构
                    this.getDataAffiliation();
                } else if (this.form.paperType1=="Subject") {
                    this.getDataSubject();
                } else {//期刊
                    this.getDataPublicationVenue();
                }
            },
            getDataAuthor() {
                this.$axios({
                method:"get",
                url: 'http://localhost:9999/BI/searchByName',
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
                //   console.log(this.nodeList[19].properties.AffiliationId);
                for( var i =0;i<this.nodeList.length;i++) {
                //先判断label
                if (this.nodeList[i].properties.label[0] == "Author") {
                    // Author
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.authorName,
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.authorName,
                    symbolSize: 70,//节点大小
                    category: 0,//设置节点所属类别
                })
                } else if (this.nodeList[i].properties.label[0] == "Subject") {
                    // Subject
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.researchInterest.substring(0,15)+'...',
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.researchInterest,
                    symbolSize: 70,//节点大小
                    category: 1,//设置节点所属类别
                })
            }else if (this.nodeList[i].properties.label[0] == "Paper") {
                //Paper
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.title.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.title,
                symbolSize: 70,//节点大小
                category: 2,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Affiliation") {
                //Affiliation
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.AffiliationId.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.AffiliationId,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            } else {
                //publication
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.venue.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.venue,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            }
          }
          for( var i = 0;i<this.linkList.length;i++) {
            this.linkData.push({
                source: ""+this.linkList[i].properties.source,//源节点
                target: ""+this.linkList[i].properties.target,//目标节点
                name: this.linkList[i].properties.label,//关系
                des: this.linkList[i].properties.label
                })
          }
        },err=>{
          console.log(err);
        })
                        this.getGraph();
            },
            getDataPaper() {

                this.$axios({
                method:"get",
                url: 'http://localhost:9999/BI/queryEntityByPageTitle',
                params:{
                    EntityType: "Paper",
                    searchContent: this.authorName,
                    limit: 50
                }
                }).then(res=>{
                console.log(res);

                this.nodeList = res.data.nodes;
                this.linkList = res.data.relations;
                console.log(this.nodeList);
                console.log(this.linkList);
                console.log("lalalal");
                //   console.log(this.nodeList[19].properties.AffiliationId);
                for( var i =0;i<this.nodeList.length;i++) {
                //先判断label
                if (this.nodeList[i].properties.label[0] == "Author") {
                    // Author
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.authorName,
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.authorName,
                    symbolSize: 70,//节点大小
                    category: 0,//设置节点所属类别
                })
                } else if (this.nodeList[i].properties.label[0] == "Subject") {
                    // Subject
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.researchInterest.substring(0,15)+'...',
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.researchInterest,
                    symbolSize: 70,//节点大小
                    category: 1,//设置节点所属类别
                })
            }else if (this.nodeList[i].properties.label[0] == "Paper") {
                //Paper
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.title.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.title,
                symbolSize: 70,//节点大小
                category: 2,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Affiliation") {
                //Affiliation
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.AffiliationId.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.AffiliationId,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            } else {
                //publication
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.venue.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.venue,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            }
          }
          for( var i = 0;i<this.linkList.length;i++) {
            this.linkData.push({
                source: ""+this.linkList[i].properties.source,//源节点
                target: ""+this.linkList[i].properties.target,//目标节点
                name: this.linkList[i].properties.label,//关系
                des: this.linkList[i].properties.label
                })
          }
        },err=>{
          console.log(err);
        })
                        this.getGraph();
            },
            getDataAffiliation() {
                console.log(this.authorName);
                this.$axios({
                method:"get",
                url: 'http://localhost:9999/BI/queryEntityByAffiliationId',
                params:{
                    EntityType: "Affiliation",
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
                //   console.log(this.nodeList[19].properties.AffiliationId);
                for( var i =0;i<this.nodeList.length;i++) {
                //先判断label
                if (this.nodeList[i].properties.label[0] == "Author") {
                    // Author
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.authorName,
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.authorName,
                    symbolSize: 70,//节点大小
                    category: 0,//设置节点所属类别
                })
                } else if (this.nodeList[i].properties.label[0] == "Subject") {
                    // Subject
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.researchInterest.substring(0,15)+'...',
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.researchInterest,
                    symbolSize: 70,//节点大小
                    category: 1,//设置节点所属类别
                })
            }else if (this.nodeList[i].properties.label[0] == "Paper") {
                //Paper
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.title.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.title,
                symbolSize: 70,//节点大小
                category: 2,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Affiliation") {
                //Affiliation
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.AffiliationId.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.AffiliationId,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            } else {
                //publication
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.venue.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.venue,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            }
          }
          for( var i = 0;i<this.linkList.length;i++) {
            this.linkData.push({
                source: ""+this.linkList[i].properties.source,//源节点
                target: ""+this.linkList[i].properties.target,//目标节点
                name: this.linkList[i].properties.label,//关系
                des: this.linkList[i].properties.label
                })
          }
        },err=>{
          console.log(err);
        })
                this.getGraph();
            },
            getDataSubject() {
                this.$axios({
                method:"get",
                url: 'http://localhost:9999/BI/queryEntityByResearchInterest',
                params:{
                    EntityType: "Subject",
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
                //   console.log(this.nodeList[19].properties.AffiliationId);
                for( var i =0;i<this.nodeList.length;i++) {
                //先判断label
                if (this.nodeList[i].properties.label[0] == "Author") {
                    // Author
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.authorName,
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.authorName,
                    symbolSize: 70,//节点大小
                    category: 0,//设置节点所属类别
                })
                } else if (this.nodeList[i].properties.label[0] == "Subject") {
                    // Subject
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.researchInterest.substring(0,15)+'...',
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.researchInterest,
                    symbolSize: 70,//节点大小
                    category: 1,//设置节点所属类别
                })
            }else if (this.nodeList[i].properties.label[0] == "Paper") {
                //Paper
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.title.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.title,
                symbolSize: 70,//节点大小
                category: 2,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Affiliation") {
                //Affiliation
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.AffiliationId.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.AffiliationId,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            } else {
                //publication
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.venue.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.venue,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            }
          }
          for( var i = 0;i<this.linkList.length;i++) {
            this.linkData.push({
                source: ""+this.linkList[i].properties.source,//源节点
                target: ""+this.linkList[i].properties.target,//目标节点
                name: this.linkList[i].properties.label,//关系
                des: this.linkList[i].properties.label
                })
          }
        },err=>{
          console.log(err);
        })
                this.getGraph();
            },
            getDataPublicationVenue() {
                this.$axios({
                method:"get",
                url: 'http://localhost:9999/BI/queryEntityByVenue',
                params:{
                    EntityType: "Publication",
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
                //   console.log(this.nodeList[19].properties.AffiliationId);
                for( var i =0;i<this.nodeList.length;i++) {
                //先判断label
                if (this.nodeList[i].properties.label[0] == "Author") {
                    // Author
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.authorName,
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.authorName,
                    symbolSize: 70,//节点大小
                    category: 0,//设置节点所属类别
                })
                } else if (this.nodeList[i].properties.label[0] == "Subject") {
                    // Subject
                    this.nodeData.push({
                    name: ""+this.nodeList[i].properties.researchInterest.substring(0,15)+'...',
                    id:""+this.nodeList[i].properties.id,
                    des: this.nodeList[i].properties.researchInterest,
                    symbolSize: 70,//节点大小
                    category: 1,//设置节点所属类别
                })
            }else if (this.nodeList[i].properties.label[0] == "Paper") {
                //Paper
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.title.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.title,
                symbolSize: 70,//节点大小
                category: 2,//设置节点所属类别
                })
            } else if (this.nodeList[i].properties.label[0] == "Affiliation") {
                //Affiliation
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.AffiliationId.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.AffiliationId,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            } else {
                //publication
                this.nodeData.push({
                name: ""+this.nodeList[i].properties.venue.substring(0,15)+'...',
                id:""+this.nodeList[i].properties.id,
                des: this.nodeList[i].properties.venue,
                symbolSize: 70,//节点大小
                category: 3,//设置节点所属类别
                })
            }
          }
          for( var i = 0;i<this.linkList.length;i++) {
            this.linkData.push({
                source: ""+this.linkList[i].properties.source,//源节点
                target: ""+this.linkList[i].properties.target,//目标节点
                name: this.linkList[i].properties.label,//关系
                des: this.linkList[i].properties.label
                })
          }
        },err=>{
          console.log(err);
        })
                this.getGraph();
            },

         getGraph() {
            let vim = this
            var myChart = echarts.init(document.getElementById('main'));
            var categories = [{name:"Author"},{name:"Subject"},{name:"Paper"},{name:"Affiliation"},{name:"PublicationVenue"}];
            var option = {
        // 图的标题
        title: {
            text: '关联关系&实体图'
        },
        // 提示框的配置
        tooltip: {
            formatter: function (x) {
                return x.data.name;
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
            emphasis: {focus: 'adjacency'},//当鼠标移动到节点上，突出显示节点以及节点的边和邻接节点
            force: {
                repulsion: 2500,
                edgeLength: [1, 500]
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
            myChart.on('click', function (param){
            console.log('param---->', param);  // 打印出param, 可以看到里边有很多参数可以使用
				// 获取节点点击的数组序号
					vim.tabledata = param.data;
					vim.category = param.data.category
					this.cn = param.data.cn
					console.log(vim.tabledata)
                    console.log(vim.tabledata.des)
					console.log(vim.category)
					var keys= Object.keys(vim.tabledata);
					var values= Object.values(vim.tabledata);
					vim.keys = keys
					vim.values = values
				});
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
