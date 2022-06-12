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
                        <el-col :span="4">在线智搜</el-col>
                        <el-col :span="9">
							<el-col :span="10">
								<el-select v-model="form.paperType1" placeholder="请选择实体类型" clearable>
									<el-option :key="item.value" :value="item.value" :label="item.name" :disabled="item.disabled" v-for="item in paperType1"></el-option>
								</el-select>
                            	<el-input v-model="input1" placeholder="请输入实体1"></el-input></el-col>
                            <el-col :span="10">
							<el-select v-model="form.paperType2" placeholder="请选择实体类型" clearable>
									<el-option :key="item.value" :value="item.value" :label="item.name" :disabled="item.disabled" v-for="item in paperType2"></el-option>
								</el-select>
                            	<el-input v-model="input2" placeholder="请输入实体2"></el-input>
                            </el-col>
                            
                        </el-col>
						<el-col :span="9">
								<el-col :span="10">
									<el-date-picker type="date" placeholder="选择开始日期" :picker-options="pickerOptions"  v-model="form.start" style="width: 100%;"></el-date-picker>
								</el-col>
								<el-col class="line" :span="2">至</el-col>
								<el-col :span="10">
									<el-date-picker type="date" placeholder="选择结束日期" :picker-options="pickerOptions" v-model="form.end" style="width: 100%;"></el-date-picker>
								</el-col>
						</el-col>
						<el-col :span="2">
                                <el-button round @click="search">搜索</el-button>
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
							<div id="myChart" style="width: 600px;height:400px;"></div>
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
				source:{},
				property1:"",
				property2:"",
				form:{
					start:"",
					end:"",
					paperType1:{},
					paperType2:{}
				},
				step:1,
				limit:20,
				pickerOptions:{
					disabledDate (time) {
						return time.getTime() > Date.now();
					}
				},
				input1:'',
				input2:'',
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
						"value":"Affliation",
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
						"value":"Affliation",
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
			}
		},
		methods:{
			 drawChart() {
				var myChart = echarts.init(document.getElementById('myChart'));
				var categories = [{name:"Author"},{name:"Affiliation"},{name:"Paper"},{name:"Publication Venue"},{name:"Subject"}];
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
					data: [
						{
						name: '刘备',
						des: '刘备',
						id:'1',
						symbolSize: 70,//节点大小
						category: 0,//设置节点所属类别
					},
					{
						name: '关羽',
						des: '关羽',
						id:'2',
						symbolSize: 70,//节点大小
						category: 0,//设置节点所属类别
					}
					],
					links: [
						{
						source: '1',//源节点
						target: '2',//目标节点
						name: '义弟',//关系
						des: ''
					}
					], //定义关系，后续省略
					categories: categories,//给类别赋值
				}]
				};
				myChart.setOption(option);
			},
			search(){
				switch (this.form.paperType1){
					case "Author":this.property1 = "authorName";break;
					case "Paper":this.property1 = "title";break;
					case "Affliation":this.property1 = "AffiliationId";break;
					case "Subject":this.property1 = "researchInterest";break;
					case "PublicationVenue":this.property1 = "venue";break;
					default:break;
				}
				switch (this.form.paperType2){
					case "Author":this.property2 = "authorName";break;
					case "Paper":this.property2 = "title";break;
					case "Affliation":this.property2 = "AffiliationId";break;
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
					url:'http://localhost:8080/BI/searchByAuthorAffiliation',
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
					console.log(res)

				})

			},
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