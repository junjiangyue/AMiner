<template>
	<div class="page-area">
		<div class="bread-crumb">
			<el-breadcrumb separator=">>">
				<el-breadcrumb-item><span class="el-icon-menu"></span> 其它</el-breadcrumb-item>
				<el-breadcrumb-item>领域查询</el-breadcrumb-item>
			</el-breadcrumb>
		</div>
		<div class="page-body">
      <div class="dataSearch">
        <el-card class="box-card">
          <el-row>
            <el-col :span="3">领域查询</el-col>
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
                请输入查询领域：
              </el-col>
              <el-col :span="15">
                <el-input v-model="input" placeholder="请输入查询领域"></el-input>
              </el-col>
            </el-col>
            <el-col :span="5">
              <el-button class="mar-left" @click="getSubject()">查询</el-button>
            </el-col>
          </el-row>
        </el-card>
        <el-row>
          <el-col :span="6">
            <el-table :data="tableData" stripe style="width: 100%">
              <el-table-column prop="researchInterest" label="领域" width="180">
              </el-table-column>
              <el-table-column fixed="right" label="操作" width="100">
                <template slot-scope="scope">
                  <el-button @click="handleClick(scope.row)" type="text" size="small">查看</el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-col>
          <el-col :span="18">
            <el-row><p class="txt-field">查询领域：{{field}}</p></el-row>
            <el-row v-if="value=='Author'">
              <el-table :data="authorTable" stripe style="width: 100%">
                <el-table-column prop="authorName" label="作者姓名">
                </el-table-column>
                <el-table-column prop="pc" label="发表文章总数">
                </el-table-column>
                <el-table-column prop="cn" label="文章被引用总数">
                </el-table-column>
                <el-table-column prop="hi" label="H-index">
                </el-table-column>
                <el-table-column prop="pi" label="等A-index的P-index">
                </el-table-column>
                <el-table-column prop="upi" label="不等A-index的P-index">
                </el-table-column>
                <el-table-column prop="authorId" label="作者ID">
                </el-table-column>
              </el-table>
            </el-row>
            
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
            // 防止出现多个echarts初始化的情况
        myChart: '',
        options: [{
          value: 'Author',
          label: '关键作者'
        }, {
          value: 'Affliation',
          label: '关键单位'
        }, {
          value: 'PublicationVenue',
          label: '关键期刊或会议'
        }],
        value: '',
        tableData: [],
        authorTable: [],
        field:"",
			}
		},
    methods: {
      getSubject() {
        this.$axios({
          method:"get",
          url: 'http://localhost:3000/findResearchInterest',
          params:{
            researchInterest:this.input,
            entity:this.value
          }
        }).then(res=>{
          console.log(res);
          this.tableData=res.data;
          console.log(res.data);
        },err=>{
          console.log(err);
        })
      },
      handleClick(index) {
        console.log('index:',index);
        console.log(index.researchInterest);
        this.field=index.researchInterest;
        this.$axios({
          method:"get",
          url: 'http://localhost:3000/findKeyAuthor',
          params:{
            researchInterest:index.researchInterest
          }
        }).then(res=>{
          console.log("关键作者：",res);
          this.authorTable=res.data;
          console.log(res.data);
        },err=>{
          console.log(err);
        })
      },
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
  .mar-left {
    margin-left: 30px;
  }
  .txt-field {
    text-align:center;
    margin-top: 20px;
    margin-bottom: 20px;
  }
</style>
