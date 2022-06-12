<template>
	<div class="chart-area">
		 <div class="crumbs">
            <el-breadcrumb separator=">>">
                <el-breadcrumb-item><i class="el-icon-star-on"></i> 图表</el-breadcrumb-item>
                <el-breadcrumb-item>vue-echart</el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        
		<div class="dataSearch">
                <el-card class="box-card">
                    <el-row  :gutter="20">
                        <el-col :span="2">在线智搜</el-col>
						 <el-col :span="4">
						 <el-select v-model="step" placeholder="请选择最深关系度" clearable>
									<el-option :key="item.value" :value="item.value" :label="item.name" :disabled="item.disabled" v-for="item in steplist"></el-option>
								</el-select>
						 </el-col>
                        <el-col :span="16">
							<el-col :span="4">
								<el-select v-model="form.paperType1" placeholder="请选择实体" clearable>
									<el-option :key="item.value" :value="item.value" :label="item.name" :disabled="item.disabled" v-for="item in paperType1"></el-option>
								</el-select>
							</el-col>
                            <el-col :span="8"><el-input v-model="input1" placeholder="请输入实体1"></el-input></el-col>
							<el-col :span="4">
								<el-select v-model="form.paperType2" placeholder="请选择实体" clearable>
									<el-option :key="item.value" :value="item.value" :label="item.name" :disabled="item.disabled" v-for="item in paperType2"></el-option>
								</el-select>
                            </el-col>
                            <el-col :span="8"><el-input v-model="input2" placeholder="请输入实体2"></el-input></el-col>
                        </el-col>
						<!-- <el-col :span="9">
								<el-col :span="10">
									<el-date-picker type="date" placeholder="选择开始日期" :picker-options="pickerOptions"  v-model="form.start" style="width: 100%;"></el-date-picker>
								</el-col>
								<el-col class="line" :span="2">至</el-col>
								<el-col :span="10">
									<el-date-picker type="date" placeholder="选择结束日期" :picker-options="pickerOptions" v-model="form.end" style="width: 100%;"></el-date-picker>
								</el-col>
						</el-col> -->
						<el-col :span="2">
                                <el-button round @click="search">搜索</el-button>
                            </el-col>
                    </el-row>
                </el-card>
                <el-row>
                    <el-col :span="5">
                        <el-card class="information" >
							<div id="mytable" style="height:800px;">
							<div v-if="category!=-1" >
							<li :key="item.value" v-for="(key,item) in keys,tabledata">{{item}}:{{key}}</li>
							</div>
							</div>
                        </el-card>
                    </el-col>
                    <el-col :span="19">
                        <el-card class="graph">
							<div id="myChart" style="width: 1000px;height:800px;"></div>
                        </el-card>
                    </el-col>
                </el-row>
            </div>
	</div>
</template>

<script>
import * as echarts from 'echarts';
	export default {
		data () {
			return {
				cn:0,
				keys:[],
				value:[],
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
				source:{},
				property1:"",
				property2:"",
				form:{
					start:"",
					end:"",
					paperType1:{},
					paperType2:{}
				},
				nodeData:[],
				linkData:[],
				nodeList: [],
                linkList: [],
				step:null,
				limit:20,
				pickerOptions:{
					disabledDate (time) {
						return time.getTime() > Date.now();
					}
				},
				input1:'Puming Zhan',
				input2:'End-to-End Evaluation in JANUS: A Speech-to-speech Translation System',
				paperType1:[
					{
						"name":"作者",
						"value":"Author",
						"property":"authorName",
					},{
						"name":"论文",
						"value":"Paper",
						"property":"title",
					},{
						"name":"研究机构/学校",
						"value":"Affiliation",
						"property":"AffiliationId",
					},{
						"name":"研究主题",
						"value":"Subject",
						"property":"researchInterest",
					}
					,{
						"name":"会议/期刊",
						"value":"PublicationVenue",
						"property":"venue",
					}
				],
				paperType2:[
					{
						"name":"作者",
						"value":"Author",
						"property":"authorName",
					},{
						"name":"论文",
						"value":"Paper",
						"property":"title",
					},{
						"name":"研究机构/学校",
						"value":"Affiliation",
						"property":"AffiliationId",
					},{
						"name":"研究主题",
						"value":"Subject",
						"property":"researchInterest",
					}
					,{
						"name":"会议/期刊",
						"value":"PublicationVenue",
						"property":"venue",
					}
				],
				steplist:[
					{
						"value":1,
					},
					{
						"value":2,
					},
					{
						"value":3,
					},
					{
						"value":4,
					},
				]
			}
		},
		methods:{
			setIndex(){
				console.log(this.category)
			},
			 drawChart() {
				let vim = this
				var myChart = echarts.init(document.getElementById('myChart'));
				var categories = [{name:"Author"},{name:"Subject"},{name:"Paper"},{name:"Affiliation"},{name:"PublicationVenue"}];
				var option = {
				// 图的标题
				title: {
					text: ''
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
						edgeLength: [1, 200]
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
								return x.data.des;
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
					data: this.nodeData,
					links:this.linkData,
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
					console.log(vim.category)
					var keys= Object.keys(vim.tabledata);
					var values= Object.values(vim.tabledata);
					vim.keys = keys
					vim.values = values
				});
			},
			
			search(){
				switch (this.form.paperType1){
					case "Author":this.property1 = "authorName";break;
					case "Paper":this.property1 = "title";break;
					case "Affiliation":this.property1 = "AffiliationId";break;
					case "Subject":this.property1 = "researchInterest";break;
					case "PublicationVenue":this.property1 = "venue";break;
					default:break;
				}
				switch (this.form.paperType2){
					case "Author":this.property2 = "authorName";break;
					case "Paper":this.property2 = "title";break;
					case "Affiliation":this.property2 = "AffiliationId";break;
					case "Subject":this.property2 = "researchInterest";break;
					case "PublicationVenue":this.property2= "venue";break;
					default:break;
				}
				console.log("时间",this.form)
				console.log("实体1",this.input1)
				console.log("实体2",this.input2)
				console.log("类型",this.property1)
				console.log("类型",this.property2)
				this.$axios({
					methods:"get",
					url:'http://localhost:9999/BI/searchByAuthorAffiliation',
					params:{
						step:this.step,
						limit:this.limit,
						first:this.input1,
						second:this.input2,
						type1:this.form.paperType1,
						type2:this.form.paperType2,
						property1:this.property1,
						property2:this.property2,
					}
				}).then(res=>{
					console.log(res.data)
					this.nodeList = res.data.nodes;
					this.linkList = res.data.relations;
					for(var i=0;i<this.nodeList.length;i++){
						//先判断label
						if (this.nodeList[i].properties.label[0] == "Author") {
							// Author
							this.nodeData.push({
							name: ""+this.nodeList[i].properties.authorName.substring(0,10),
							id:""+this.nodeList[i].properties.id,
							des: ""+this.nodeList[i].properties.authorName,
							cn:this.nodeList[i].properties.cn,
							hi:this.nodeList[i].properties.hi,
							pc:this.nodeList[i].properties.pc,
							pi:this.nodeList[i].properties.pi,
							upi:this.nodeList[i].properties.upi,
							symbolSize: 70,//节点大小
							category: 0,//设置节点所属类别
							})
						} else if (this.nodeList[i].properties.label[0] == "Subject") {
							// Subject
							this.nodeData.push({
							name: ""+this.nodeList[i].properties.researchInterest.substring(0,10),
							id:""+this.nodeList[i].properties.id,
							des: ""+this.nodeList[i].properties.researchInterest,
							symbolSize: 70,//节点大小
							category: 1,//设置节点所属类别
							})
						}else if (this.nodeList[i].properties.label[0] == "Paper") {
							//Paper
							this.nodeData.push({
							name: ""+this.nodeList[i].properties.title.substring(0,10),
							id:""+this.nodeList[i].properties.id,
							des: ""+this.nodeList[i].properties.title,
							year:this.nodeList[i].properties.year,
							author:this.nodeList[i].properties.author,
							affiliation:this.nodeList[i].properties.affiliation,
							symbolSize: 70,//节点大小
							category: 2,//设置节点所属类别
							})
						} else if (this.nodeList[i].properties.label[0] == "Affiliation") {
							//Affiliation
							this.nodeData.push({
							name: ""+this.nodeList[i].properties.AffiliationId.substring(0,10),
							id:""+this.nodeList[i].properties.id,
							des: ""+this.nodeList[i].properties.AffiliationId,

							symbolSize: 70,//节点大小
							category: 3,//设置节点所属类别
							})
						} else if(this.nodeList[i].properties.label[0] == "PublicationVenue"){
							this.nodeData.push({
							name: ""+this.nodeList[i].properties.venue.substring(0,10),
							id:""+this.nodeList[i].properties.id,
							des: ""+this.nodeList[i].properties.venue,
							symbolSize: 70,//节点大小
							category: 4,//设置节点所属类别
							})
						}
					}
					for( var i = 0;i<this.linkList.length;i++) {
						this.linkData.push({
							// name: this.nodeList[i].AffiliationId,
							// des: this.nodeList[i].id,
							// symbolSize: 70,//节点大小
							// category: 3,//设置节点所属类别
							source: ""+this.linkList[i].properties.source,//源节点
							target: ""+this.linkList[i].properties.target,//目标节点
							name: ""+this.linkList[i].properties.label,//关系
							des: ""+this.linkList[i].properties.label,
							id: this.linkList[i].properties.id
							})
					}
					this.drawChart();
				},err=>{
          			console.log(err);
				})

			},
		},
		mounted() {
			
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