<template>
  <div >
    <div  v-if="userType=='editor'">
      <i v-if="value==2" class="dituhong diyuicon"></i>
      <i v-else="value==1" class="ditulan diyuicon"></i>
    </div>


    <el-select class="left-select" v-if="userType=='editor'"size="small" v-model="value" @change="diyu(value)">
      <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value"></el-option>
    </el-select>
    <!--<el-menu mode="vertical" theme="dark" :default-active="$route.path" :unique-opened=true>-->
    <sidebar-item :routes='leftListShow'></sidebar-item>
    <!--</el-menu>-->
  </div>

</template>

<script>
  import Bus from '../../js/bus.js';
  import Vue from 'vue'
  import {mapGetters,mapActions} from 'vuex'
  import SidebarItem from './SidebarItem';
  import Cookies from 'js-cookie';
  export default {
    data() {
      return {
        options: [{
          value: 1,
          label: '资源域'
        }, {
          value: 2,
          label: '服务域'
        } ],
        value: '服务域',
        firstckick:false,
        leftListShow:[],
        addPs:true,

      }
    },
    components: { SidebarItem },
    computed: {
      ...mapGetters([
        'leftrouter','sidebar','userType','fuwuyu','ziyuanyu',
        'leftList',
      ])
    },
    methods:{
      ...mapActions(
        []
      ),
      diyu(res,url){
        if(res==1&&this.firstckick){
          this.firstckick=false;//让他只有第一次能进来 因为默认页面展示资源域 但是 selection 还可能在一次重复选择资源域 所以让他下次只能点击服务域才发生事件
//          console.log(this.leftList)
          $('.togglepage').css('display','block')
          for(var i=0;i<this.leftListShow.length;i++){
            if(this.leftListShow[i].diyu!==undefined){
              if(this.leftListShow[i].diyu.indexOf('fuwuyu')!==-1){
                this.leftListShow.splice(i,1);
                i--;
              }else{}
            }
          }
//          this.leftListShow=this.leftListShow.concat(this.ziyuanyu).reverse()
          this.leftListShow=this.ziyuanyu.concat(this.leftListShow);
//          console.log(url==undefined)
          if(url!==undefined){
            this.$router.push({path:url});
          }else{
            this.$router.push({path:'/console/survey'});
          }

          localStorage.setItem("diyu", "ziyuanyu");
          this.value='资源域'
        }else if(res==2&&!this.firstckick){
          this.firstckick=true;
          $('.togglepage').css('display','none')
          for(var i=0;i<this.leftListShow.length;i++){
            if(this.leftListShow[i].diyu!==undefined){
              if(this.leftListShow[i].diyu.indexOf('ziyuanyu')!==-1){
                this.leftListShow.splice(i,1);
                i--;
              }
              else {}
            }
          }
          this.leftListShow=this.fuwuyu.concat(this.leftListShow);
          console.log(url==undefined)
          if(url!==undefined){
            this.$router.push({path:url});
          }else{
            this.$router.push({path:'/console/excelserver/cloudGateway'});
          }
          localStorage.setItem("diyu", "fuwuyu");

        }
        $( 'li').children('div').children('a').children('i:last-child').removeClass('fanzhuan');
        if(this.addPs){
          this.addP()
        }
        this.addPs=false
      },
      editorOrAdmin(){
        if(this.userType=='editor'){
          this.leftListShow=$.extend([],this.leftList);
          if(localStorage.getItem("diyu")=='fuwuyu'){
            for(var i=0;i<this.leftListShow.length;i++){
              if(this.leftListShow[i].diyu!==undefined){
                if(this.leftListShow[i].diyu.indexOf('ziyuanyu')!==-1){
                  this.leftListShow.splice(i,1);
                  i--;
                }
                else {}
              }
            }
            this.leftListShow=this.fuwuyu.concat(this.leftListShow)
            this.value=2;
            this.firstckick=true;
          }
        }else{
          this.leftListShow=this.leftrouter;
        }
      },
      addP(){
        setTimeout(function(){
          var left1LiLen=$('#anLeftList .left1>ul>li').length;
          var left2LiLen=$('#anLeftList .left2>ul>li').length;
          if(Cookies.get('Admin-Token')=='editor'){
            if($('#anLeftList .left1>ul>p').length<1){
              $('#anLeftList .left1>ul>li').eq(left1LiLen-4).before(' <p class="admin-line" style="display: inline-block;margin:10px 0px 6px 12px;color: #fff;color: #6e6f7e;">用户中心</p>');
            }
            if($('#anLeftList .left2>ul>p').length<1){
              $('#anLeftList .left2>ul>li').eq(left2LiLen-4).before(`
                <p data-v-46b0a5fa="">
                  <span data-v-46b0a5fa="" class="admin-line admin-cuxian" style="width: 17%;">用户中心</span>
                </p>
              `);

            }
             }
        },8)
      }


    },
    created:function(){

    },
    mounted:function () {
      this.$nextTick(function () {
        var me=this;
        Bus.$on("newNodeEvent", function (a) {
          me.diyu(1,a)
        })
        me.editorOrAdmin();

        me.addP();

      })
    }
  }
</script>

<style>
  .el-menu {
    min-height: 100%;
  }
  .left-select .el-input__inner{
    background: #333744;
    border:none;
    color: #fff;
    padding-left: 49px;
    padding-right: 9px;

  }
  .left-select{
    border-bottom:1px solid #333;
    width:100%;
    padding:8px 0;
  }
  .diyuicon{
    position: fixed;
    top: 64px;
    left: 18px;
    background-color: #fff;
    z-index: 1500;
  }
  .left2 .anclear>div>a>span{
    margin-left:10px;
  }
  .left-select .el-input__icon.el-icon-caret-top{
    margin: 0 10px 0 0;
  }
  .dituhong{
    width: 10px;
    height: 14px;
    float: left;
    background: url('../../images/o_region.png') no-repeat;
    display: inline-block;
  }
  .ditulan{
    width: 10px;
    height: 14px;
    float: left;
    background: url('../../images/o_region2.png') no-repeat;
    display: inline-block;
  }
  .el-select-dropdown{
    width: 195px !important;
    min-width: 195px !important;
  }
</style>
