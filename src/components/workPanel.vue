<template>
	<div style="text-align: center">
		<h4>先选择本次登录人员的身份，再进入相应的系统 😅</h4>
		
		<div class="work-panel">
			<div class="user">
				<el-button type="primary" round size="small" @click="showUserSelect = true" icon="el-icon-user">选择本次登录者</el-button>
				<div v-if="loginUser !== '' && loginUser !== null">
					<span>{{loginUser.name}}</span>
				</div>
			</div>
			<div class="panel">
				<div class="panel-item" @click="to('workSpace')">
					<div>
						<i class="el-icon-s-platform"></i>
						<span>进入工作区</span>
					</div>
					<p>
						您可以发起、处理及查看审批，进行日常工作任务
					</p>
				</div>
				<div class="panel-item" @click="to('formListPanel')">
					<div>
						<i class="el-icon-s-custom"></i>
						<span>进入管理后台</span>
					</div>
					<p>
						审批工作流创建 、编辑及其他设置操作，均可以在后台进行
					</p>
				</div>
			</div>
		</div>
		
		<org-picker :show="showUserSelect" single onlyUser @close="showUserSelect = false" :selected="select" @selectOver="selected"></org-picker>
	</div>
</template>

<script>
	import orgPicker from "@/components/common/organizationPicker";
	
  export default {
    name: "workPanel",
	  components:{orgPicker},
	  data(){
      return{
        showUserSelect: false,
        select:[],
        loginUser: {
			id: 381496,
name: "付佳威",
selected: false,
sex: true,
type: "user",
		}
      }
	  },
	  mounted(){
      let user = sessionStorage.getItem("user")
		  if (user !== null && user !== ''){
        this.loginUser = JSON.parse(user)
			  this.select.push(this.loginUser)
		  }
	  },
	  methods:{
      selected(select){
        this.select = select
        this.loginUser = select.length > 0 ? select[0]:''
        this.showUserSelect = false
	      sessionStorage.setItem("user", JSON.stringify(this.loginUser))
      },
		  to(path){
        if (this.loginUser === null || this.loginUser === ''){
          this.$message.warning("请先选择一个人员身份进行登录")
        }else {
          this.$router.push(path)
        }
		  }
	  }
  }
</script>

<style lang="less" scoped>
	
	h4 {
		margin: 0 auto;
    color: #38adff;
    margin-top: 150px;
	}
	
	.user{
		position: absolute;
		left: 20%;
		margin-top: 20px;
		div{
			margin-left: 20px;
			display: inline-block;
		}
	}
	
	.work-panel {
		text-align: left;
		display: flex;
		justify-content: center;
		position: relative;
		.panel {
			margin-top: 80px;
			max-width: 700px;
			display: flex;
			justify-content: center;
			
			.panel-item {
				cursor: pointer;
				margin: 0 40px;
				width: 250px;
				padding: 10px;
				display: inline-block;
				background: #ffffff;
				border-radius: 10px;
				border: 1px solid #ffffff;
				box-shadow: 1px 1px 8px 0 #b0b0b1;
				
				&:hover {
					border: 1px solid #2594ff;
					box-shadow: 1px 1px 13px 0 #a4a4a5;
				}
				
				div:nth-child(1) {
					color: #7a7a7a;
					font-weight: bold;
					height: 60px;
					line-height: 60px;
					font-size: large;
					border-bottom: 1px solid #cccdcd;
					
					span {
						margin-left: 30px;
					}
				}
				
				i {
					padding: 8px;
					color: #ffffff;
					background: #2594ff;
					font-size: 25px;
					border-radius: 5px;
				}
				
				.el-icon-s-platform {
					background: rgb(255, 148, 62);
				}
				
				p {
					padding: 10px 0;
					color: #7a7a7a;
					font-size: medium;
				}
			}
		}
		
	}
</style>
