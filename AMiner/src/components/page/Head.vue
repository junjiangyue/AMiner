<template>
	<div class="header">
		<div class="logo">学术领域知识图谱可视化系统</div>
	</div>
</template>

<script type="text/javascript">
</script>

<style scoped>
	.header {
        position: relative;
        width: 100%;
        height: 70px;
        font-size: 22px;
        line-height: 70px;
        color:#fff;
        background-color:#242f42;
    }
    .header .logo{
        float: left;
        width:250px;
        text-align: center;
    }
    .header .welcome {
    	position:absolute;
    	left:50%;
    	top:50%;
    	-webit-transform:translate3d(-50%,-50%,0);
    	transform:translate3d(-50%,-50%,0);
    }
    .username{
    	color:#ff3333;
    }
    .user-info {
        float: right;
        padding-right: 50px;
        font-size: 16px;
        color: #fff;
        cursor:pointer;
    }
    .user-info img{
        width:40px;
        height:40px;
        border-radius: 50%;
        vertical-align:middle;
        margin-right:15px;
    }
    .el-dropdown-menu__item{
        text-align: center;
    }
</style>