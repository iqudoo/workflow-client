<template>
	<div>
		<div class="scale">
			<el-button icon="el-icon-plus" size="small" @click="scale += 10" :disabled="scale >= 150" circle></el-button>
			<span>{{scale}}%</span>
			<el-button icon="el-icon-minus" size="small" @click="scale -= 10" :disabled="scale <= 50" circle></el-button>
		</div>
		<div class="design" :style="'transform: scale('+ scale / 100 +'); transform-origin: 50% 0px 0px; width: 100%;'">
			<!--<el-row v-for="(node, id) in nodes" :key="id">
				<arrow :node="ch" :index="id" v-for="(ch, i) in node" :key="i" @addNode="addNode" @delNode="delNode(id)" @select="selectNode"></arrow>
			</el-row>-->
			<process @select="selectNode"></process>
			<el-row>
				<div class="end">
					<div></div>
					<div></div>
					<!-- <arrow @addNode="addNode" @delNode="delNode(nodes.length - 1)" @select="selectNode"></arrow>-->
					<div class="end-node">流程结束</div>
				</div>
			</el-row>
		
		</div>
		<el-drawer :title="select.name" :visible.sync="drawer" size="400px" direction="rtl" :modal="false">
			<div slot="title">
				<el-input v-model="select.name" size="medium" v-if="showInput"
				          style="width: 300px" @blur="showInput = false"></el-input>
				<el-link v-else @click="showInput = true" style="font-size: medium">
					<i class="el-icon-edit" style="margin-right: 10px"></i> {{select.name}}
				</el-link>
			</div>
			
			<node-config :node="select"></node-config>
		</el-drawer>
	</div>

</template>

<script>
    import process from "./process/processView";
    import tp from "@/assets/approvalTemplate";
		import nodeConfig from "./process/nodeConfig";

    export default {
        name: "processDesign",
        components: { process, nodeConfig},
        data() {
            return {
                select: {},
								showInput: false,
                drawer: false,
                scale: 100,
                nodes: []
            }
        },
        mounted() {
            this.templateDecode();
        },
        methods: {
            selectNode(node) {
                this.select = node;
                this.drawer = true;
            },
            templateDecode() {
                console.log(tp)
            }
        },
    }
</script>

<style lang="less" scoped>
	@import "@/assets/theme";
	
	::-webkit-scrollbar {
		width: 8px;
		height: 8px;
		background-color: #e1e1e1;
	}
	
	::-webkit-scrollbar-thumb {
		border-radius: 16px;
		background-color: #99999a;
	}
	.design {
		margin-top: 100px;
		//overflow: auto;
		/deep/ .el-row {
			display: flex;
			justify-content: center;
			.end{
              /*div:first-child{
                margin: 0 auto;
                border-radius: 50%;
                border: 5px solid #a9a9a9;
                width: 0;
                height: 0;
              }
              div:nth-child(2){
                height: 30px;
                margin: 0 auto;
                width: 0;
                border: 1px solid #a9a9a9;
              }*/
              .end-node {
                margin-bottom: 20px;
                border-radius: 15px;
                padding: 5px 10px;
                font-size: small;
                color: #747474;
                background-color: #f2f2f2;
                box-shadow: 0 0 10px 0 #bcbcbc;
              }
            }
			
		}
	}

	/deep/ .el-link{
		display: inline;
		width: 50px;
	}
	
	/deep/ .el-drawer__body{
		overflow-y: auto;
		&::-webkit-scrollbar {
			width: 8px;
			height: 8px;
			background-color: #e1e1e1;
		}
		
		&::-webkit-scrollbar-thumb {
			border-radius: 5px;
			background-color: #a8a8a9;
		}
	}
	.scale {
		z-index: 999;
		position: fixed;
		top: 80px;
		right: 40px;
		
		span {
			margin: 0 10px;
			font-size: 15px;
			color: #7a7a7a;
			width: 50px;
		}
	}
</style>
