/**
* @title 原生vue抽屉组件
* @author 易建伟
* @time 2019-07-04
*
* ------------使用-------------
* 全局注册
* import drawer from './drawer'
* Vue.component('drawer', drawer)
*
* 方法或属性
*  Attributes || Events || slot       说明                                   类型                   可选值                    默认值
* ------------------------------------------------------ Attributes -------------------------------------------
*     visible.sync            是否显示 Dialog，支持 .sync 修饰符            boolean                   ——                      false
*      title                         标题名称                              string                                            标题
*      headerShow                标题头部是否显示                          boolean                    ——                      true
*   header-background            标题头部背景颜色                          string                     ——                       #fff
*     closeBtnShow                关闭按钮是否显示                          boolean                     ——                    true
*     title-color                 标题头部标题                             string                    ——                        #000
*   main-background               内容背景颜色                             string                    ——                       #fff
*   footerShow                    底部是否显示                             boolean                    ——                       false
*   footer-height                   底部高度                               string                    ——                        60px
*   footer-background            底部背景颜色                              string                     ——                        #fff
*       width                       侧栏宽度                               string                    ——                       500px
*       align                      侧栏位置                               string                  right,left                 right
*   close-on-click-modal       点击遮罩层是否关闭                           boolean                   ——                       false
* ------------------------------------------------------ Events -------------------------------------------
*      open                    Dialog 打开的回调
*      opened                  Dialog 打开动画结束时的回调
*      close                 Dialog 关闭的回调
*      closed                  Dialog 关闭动画结束时的回调
* ------------------------------------------------------ slot  -------------------------------------------
*      footer                  Dialog 按钮操作区的内容
* 例子 (简单）
* <drawer
*   title="测试"
*   :visible.sync='dialogVisible'
*   width="500px"
*   close-on-click-modal
* >
* </drawer>
* 例子（完整属性）
* <drawer
*   :visible.sync='dialogVisible'
*   :headerShow="true"
*   header-background="#f5f5f5"
*   title-color="#000"
*   main-background="#EBEEF5"
*   :footerShow="true"
*   footer-height="60px"
*   footer-background="#f5f5f5"
*   width="500px"
*   align="right"
*   close-on-click-modal
*   @close="ce"
*   @closed="ce"
*   @open="ce"
*   @opend="ce"
*    >
*   <!--内容区-->
*   <div>
*     <p>内容</p>
*   </div>
*
*   <!--这里是底部-->
*   <div slot="footer">
*     <p>底部</p>
*   </div>
* </drawer>
*/

<!--功能代码-->
<template>
  <transition name="drawer" @before-enter="drawerBeforeEnter" @enter="drawerEnter" @after-enter="drawerAfterEnter" @leave="drawerleave" @before-leave="drawerbeforeLeave" v-on:after-leave="drawerafterLeave">
    <div class="drawer" v-if="visible" @click="drawerHide">
      <transition name="drawerC" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter" @leave="leave" @after-leave="afterLeave">
        <div class="Eject" :style="{'width':width?width:'500px','float':aligns}" v-if="isshow" @click.stop="eject" ref="eject">
          <header
            v-if="headerShow == undefined?true:headerShow"
            :style="{'background':headerBackground?headerBackground:'#fff'}">
            <span class="title" :style="{'color':titleColor?titleColor+'!important':''}">{{title?title:"标题"}}</span>
            <span @click.stop="close" class="close" v-if="closeBtnShow == undefined?true:closeBtnShow">x</span>
          </header>
          <main :style="{'background':mainBackground?mainBackground:'#fff'}">
            <slot></slot>
          </main>
          <footer
            v-if="footerShow?footerShow:false"
            :style="{'height':footerHeight?footerHeight:'60px','background':footerBackground?footerBackground:'#fff'}">
            <slot name="footer"></slot>
          </footer>
        </div>
      </transition>
    </div>
  </transition>
</template>
<script>
  export default {
    name:'demo',
    props:["title","closeBtnShow","footerShow","footerHeight","width","visible","footerBackground","headerShow","headerBackground","titleColor","mainBackground","align","closeOnClickModal"],
    data() {
      return {
        isshow: false,
        timer: undefined,
        aligns: "right",
        index: 0
      }
    },
    watch:{
      visible(newVal,oldVal){
        if(newVal){
          //显示的时候
        }else{

        }
      }
    },
    created(){
      this.isshow = this.visible?this.visible:false
      if(this.align){
        console.log(this.align)
        if(this.align != 'right' && this.align != 'left'){
          this.align = 'right'
          alert("目前只支持right和left")
        }else{
          this.aligns = this.align
        }
      }else{
        this.aligns = 'right'
      }
    },
    mounted(){

    },
    beforeDestroy(){
      clearTimeout(this.timer)
    },
    methods: {
      //关闭按钮
      close() {
        this.timer = setTimeout(()=> {
          this.$emit("update:visible",!this.visible);
        },10)
        this.index = 1
        this.isshow = false
        this.$emit("close")
      },
      //点击遮罩层隐藏
      drawerHide(){
        if(this.closeOnClickModal == ''){
          this.close()
        }
      },
      //阻止侧栏冒泡事件
      eject(){},
      //侧栏动画开始之前
      beforeEnter(el,time = 0.4){
        let wid = '500px'
        if(this.width){
          wid = this.width
        }
        switch(this.aligns){
          case "left":
            el.style.transform = "translate(-"+wid+",0px)"
            el.style.transition = "all "+time+"s ease"
            break;
          case "right":
            el.style.transform = "translate("+wid+",0px)"
            el.style.transition = "all "+time+"s ease"
            break;
          default:
            alert("目前只支持right和left")
        }
      },
      //侧栏动画结束
      enter(el,done){
        el.offsetWidth
        el.style.transform = "translate(0px,0px)"
        el.style.transition = "all 0.4s ease"
        done()
      },
      //侧栏动画结束之后
      afterEnter(el){
      },
      //关闭动画
      leave(el){
       this.beforeEnter(el,0.2)
      },
      //关闭动画结束
      afterLeave(){

      },
      //-----------------------------------------------------
      //遮罩动画开始
      drawerBeforeEnter(el){
        this.$emit("open")
        el.style.backgroundColor = "rgba(0,0,0,0)"
        el.style.transition = "all 0.4s ease"
      },
      //遮罩动画结束
      drawerEnter(el,done){
        el.offsetWidth
        el.style.backgroundColor = "rgba(0,0,0,0.4)"
        el.style.transition = "all 0.4s ease"
        done()
      },
      //遮罩动画结束之后
      drawerAfterEnter(el){
        this.isshow = true
        this.$emit("opend")
      },
      drawerleave(el){
        el.style.backgroundColor = "rgba(0,0,0,0)"
        el.style.transition = "all 0.8s ease"
        this.$emit("closed")
      },
      //遮罩层隐藏
      drawerbeforeLeave(){
        if (this.index == 0){
          this.beforeEnter(this.$refs.eject,0.2)
        }
      },
      //遮罩层隐藏之后
      drawerafterLeave(){
        clearTimeout(this.timer)
        this.timer = undefined
        this.isshow = false
        this.index =  0
      }
    }
  }
</script>
<style scoped>
  .drawer{
    width: 100%;
    position: fixed;
    top: 0px;
    left: 0;
    z-index: 2000;
    height: 100%;
    background-color: rgba(0,0,0,0.4);
    transition: 1s;
  }
  .Eject{
    height: 100%;
    background-color: #fff;
    display: flex;
    flex-direction: column;
    transition: 1s;
  }
  header {
    padding: 0 20px;
    border-bottom: 1px solid #e8eaec;
    line-height: 50px;
    display: flex;
  }
  .title{
    flex: 1;
    font-size: 15px;
    color: #17233d;
    font-weight: 700;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .close{
    width: 30px;
    text-align: right;
    font-size: 18px;
    color: #909399;
    cursor: pointer;
  }
  .close:hover{
    color: #409EFF;
  }
  main{
    overflow: auto;
    flex: 1;
    padding: 20px 20px;
    box-sizing: border-box;
  }
  footer{
    border-top: 1px solid #e8eaec;
    padding: 10px 20px;
    box-sizing: border-box;
  }
  /*滚动条凹槽的颜色，还可以设置边框属性*/
  ::-webkit-scrollbar-track-piece {
  background-color:#f8f8f8;
  }
  /*滚动条的宽度*/
  ::-webkit-scrollbar {
  width:9px;
    height:9px;
  }
  /*滚动条的设置*/
  ::-webkit-scrollbar-thumb {
  background-color:#C0C4CC;
    background-clip:padding-box;
    min-height:28px;
    border-radius: 10px;
  }
  ::-webkit-scrollbar-thumb:hover {
    background-color:#bbb;
  }
</style>
