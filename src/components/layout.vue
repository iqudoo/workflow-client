<template>
  <div>
    <div class="header">
      <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" @select="handleSelect">
        <el-menu-item index="/layout/baseSetup" @click="to('/layout/baseSetup')">基础设置</el-menu-item>
        <el-menu-item index="/layout/formDesign" @click="to('/layout/formDesign')">表单设计</el-menu-item>
        <el-menu-item index="/layout/processDesign" @click="to('/layout/processDesign')">流程设计</el-menu-item>
        <el-menu-item index="/layout/seniorSetup" @click="to('/layout/seniorSetup')">高级设置</el-menu-item>
      </el-menu>
      <div class="publish">
        <el-button size="mini" @click="preview"><i class="el-icon-view"></i>预览</el-button>
        <el-button size="mini" type="primary" @click="publish"><i class="el-icon-s-promotion"></i>发布</el-button>
      </div>
      <div class="back">
        <el-button @click="exit" size="medium" icon="el-icon-arrow-left" circle></el-button>
        <span>
          <i :class="setup.icon" :style="'background:' + setup.background"></i>
          <span>{{setup.name}}</span>
        </span>
      </div>
    </div>

    <el-dialog title="请使用手机扫码预览" :visible.sync="viewCode" width="300px" :close-on-click-modal="false" center>
      <img src="../assets/image/code.png" width="250" height="250">
    </el-dialog>

    <div class="layout-body">
      <transition name="router-fade" mode="out-in">
        <router-view v-if="!$route.meta.keepAlive"/>
      </transition>
    </div>
  </div>
</template>

<script>
  import {updateFormDetail, updateTemplate} from '@/api/setting'
  
  export default {
    data() {
      return {
        activeIndex: '/layout/baseSetup',
        viewCode: false,
      };
    },
    computed:{
      setup() {
        return this.$store.state.template.baseSetup;
      },
      template() {
        return this.$store.state.template;
      }
    },
    created(){
      this.check()
    },
    mounted() {
			this.activeIndex = this.$route.path
      console.log(document.body.offsetWidth)
      if (document.body.offsetWidth <= 970){
        this.$msgbox.alert("本设计器未适配中小屏幕，建议您在PC电脑端浏览器进行操作")
      }
      this.listener()
    },
    methods: {
      publish() {
        this.$confirm('您确定审批流程已配置完毕,并需要将其发布，发布后立即生效，是否继续?', '提示', {
          confirmButtonText: '发布',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          console.log(this.setup)
          let template = {
            templateId: this.template.id,
            templateName: this.setup.name,
            icon: this.setup.icon,
            group: this.setup.group,
            background: this.setup.background,
            whoCommit: JSON.stringify(this.setup.whoCommit.map(
                    wc => {return {id: wc.id, type: wc.type, name: wc.name}})),
            whoEdit: JSON.stringify(this.setup.whoEdit.map(
                    wc => {return {id: wc.id, type: wc.type, name: wc.name}})),
            whoExport: JSON.stringify(this.setup.whoExport.map(
                    wc => {return {id: wc.id, type: wc.type, name: wc.name}})),
            formItems: JSON.stringify(this.template.form),
            remark: this.setup.remark,
            process: JSON.stringify(this.template.process),
          }
          if (this.valid()){
            updateFormDetail(template).then(rsp => {
              let isAdd = this.template.id === undefined
              let params = {templateId: isAdd ? this.template.id:rsp.data,
                type:'move', groupId: this.setup.group}
              updateTemplate(params).then(rsp => {
                this.$message.success(rsp.data)
                this.$store.commit('clearTemplate')
                this.$router.push('/formListPanel')
              }).catch(err => this.$message.error(err.response.data))
            }).catch(err => this.$message.error(err.response.data))
          }
        })
      },
      preview() {
        this.viewCode = true;
      },
      valid(){
        if (!this.$isNotEmpty(this.setup.group)){
          this.$message.warning('请选择分组')
          this.$router.push('/layout/baseSetup')
          return false;
        }
        return true;
      },
      exit(){
        this.$confirm('未发布的内容将不会被保存，是否直接退出 ?', '提示', {
          confirmButtonText: '退出',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //sessionStorage.setItem('router-path','/formListPanel')
          //window.location.reload()
          this.$store.commit('clearTemplate')
          this.$router.push('/formListPanel')
        })
      },
      to(path) {
        if (path !== this.$route.path){
          this.$router.push(path);
        }
      },
      handleSelect(key, keyPath) {
        console.log(key, keyPath);
      },
      listener(){
        window.onunload = this.closeBefore()
        window.onbeforeunload = this.closeBefore()
        //window.on('beforeunload',this.closeBefore())
      },
      closeBefore(){
        //alert("您将要离开本页")
        return false
      },
      check(){
        if(this.$store.state.isEdit === null){
          this.$router.push("/workPanel");
        }
      }
    }
  }
</script>
<style lang="less" scoped>
  @import "@/assets/global";

  .layout-body{
    min-width: 980px;
  }
  
  /deep/ .header {
    min-width: 980px;
    position: relative;
    .el-menu {
      top: 0;
      z-index: 999;
      display: flex;
      justify-content: center;
      width: 100%;
    }

    .publish {
      position: absolute;
      top: 15px;
      right: 20px;
      z-index: 1000;

      i {
        margin-right: 6px;
      }

      button {
        border-radius: 15px;
      }
    }
  
    .back{
      position: absolute;
      z-index: 1000;
      top: 10px;
      left: 20px;
      font-size: small;
      span{
        i{
          border-radius: 10px;
          padding: 7.8px;
          font-size: 20px;
          color: #ffffff;
          margin: 0 10px;
        }
      }
      
    }
  }
</style>
