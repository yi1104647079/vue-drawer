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
*      loading                是否显示 加载图标，支持 .sync 修饰符            boolean                   ——                     false
*     loadingColor                  加载图标颜色                           string                    ——                     #409EFF
*      title                         标题名称                              string                                            标题
*      headerShow                标题头部是否显示                          boolean                    ——                      true
*   header-background            标题头部背景颜色                          string                     ——                       #fff
*     closeBtnShow                关闭按钮是否显示                          boolean                     ——                    true
*     title-color                 标题头部标题                             string                    ——                        #000
*   main-background               内容背景颜色                             string                    ——                       #fff
*   footerShow                    底部是否显示                             boolean                    ——                       false
*   footer-height                   底部高度                               string                    ——                        60px
*   footer-background            底部背景颜色                              string                     ——                        #fff
*       width              侧栏宽度(align为right,left生效)                  string                    ——                       500px
*       height             侧栏高度(align为top,bottom生效)                 string                    ——                       300px
*       align                      侧栏位置                               string              top,bottom,right,left          right
*       modal                     是否显示遮罩层                           boolean                   ——                      true
*   close-on-click-modal       点击遮罩层是否关闭                           boolean                   ——                       false
* ------------------------------------------------------ Events -------------------------------------------
*      open                    Dialog 打开的回调
*      opened                  Dialog 打开动画结束时的回调
*      close                 Dialog 关闭的回调
*      closed                  Dialog 关闭动画结束时的回调
* ------------------------------------------------------ slot  -------------------------------------------
*      header                  头部内容区域
*      footer                  底部内容区域
*/
<!--* 例子 (简单）
 <drawer
   title="测试"
   :visible.sync='dialogVisible'
   width="500px"
   close-on-click-modal>
 </drawer>
 例子（完整属性）
 <drawer
   :visible.sync='dialogVisible'
   :headerShow="true"
   header-background="#f5f5f5"
   title-color="#000"
   main-background="#EBEEF5"
   :footerShow="true"
   footer-height="60px"
   footer-background="#f5f5f5"
   width="500px"
   height="300px"
   align="right"
   :modal="true"
   close-on-click-modal
   :loading.sync="loading"
   loadingColor="#ff6700"
   @close="ce"
   @closed="ce"
   @open="ce"
   @opend="ce">
   <div slot="header">
     <p>内容</p>
   </div>
   <div>
     <p>内容</p>
   </div>
   <div slot="footer">
     <p>底部</p>
   </div>
 </drawer>
-->

<!--功能代码-->
<template>
    <transition name="drawer" @before-enter="drawerBeforeEnter" @enter="drawerEnter" @after-enter="drawerAfterEnter" @leave="drawerleave" @before-leave="drawerbeforeLeave" v-on:after-leave="drawerafterLeave">



        <div class="drawer" v-if="visible" @click="drawerHide" :style="{'background-color':modalShow?'rgba(0,0,0,0.4)':''}">
            <transition name="drawerC" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter" @leave="leave" @after-leave="afterLeave">
                <div :class="['Eject',aligns == 'left'?'EjectLeft':aligns == 'right'?'EjectRight':aligns == 'top'?'EjectTop':aligns == 'bottom'?'EjectBottom':'EjectRight']" :style="{'width':widths,'height':heights}" v-if="jdShow" @click.stop="eject" ref="eject">
                    <!--加载中-->
                    <div class="loading" v-if="loading == undefined?false:loading">
                        <div class="loader" title="2">
                            <svg version="1.1" id="loader-1" x="0px" y="0px" width="40px" height="40px" viewBox="0 0 50 50" style="enable-background:new 0 0 50 50;">
                                <path d="M43.935,25.145c0-10.318-8.364-18.683-18.683-18.683c-10.318,0-18.683,8.365-18.683,18.683h4.068c0-8.071,6.543-14.615,14.615-14.615c8.072,0,14.615,6.543,14.615,14.615H43.935z" :fill="loadingColor?loadingColor:'#409EFF'">
                                    <animateTransform attributeType="xml" attributeName="transform" type="rotate" from="0 25 25" to="360 25 25" dur="0.8s" repeatCount="indefinite"/>
                                </path>
                            </svg>
                            <p style="color: #606266;">加载中</p>
                        </div>
                    </div>
                    <header
                            v-if="headerShow == undefined?true:headerShow"
                            :style="{'background':headerBackground?headerBackground:'#fff'}">
                        <div class="headDiv" v-if="!headerSlot">
                            <span class="title" :style="{'color':titleColor?titleColor+'!important':''}">{{title?title:"标题"}}</span>
                            <span @click.stop="close" class="close" v-if="closeBtnShow == undefined?true:closeBtnShow">x</span>
                        </div>

                        <slot name="header"></slot>
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
        props:["title","closeBtnShow","footerShow","footerHeight","width","height","visible","footerBackground","headerShow","headerBackground","titleColor","mainBackground","align","closeOnClickModal","loading","loadingColor","modal"],
        data() {
            return {
                jdShow: false,
                timer: undefined,
                aligns: "right",
                heights: '',
                widths: '',
                headerSlot: false, //头部插槽
                modalShow: true, //遮罩层
            }
        },
        watch:{
            visible(newVal,oldVal){
                if(newVal){
                    //显示的时候
                }else{

                }
            },
            loading(newVal,oldVal){
                if(newVal){
                    //显示的时候
                }else{

                }
            },
        },
        created(){
            this.modalShow = this.modal == undefined? true : this.modal
            this.jdShow = this.visible?this.visible:false;
            if(this.align){
                this.aligns = this.align
                if(this.aligns == 'right' || this.aligns == 'left'){
                    if(!this.width){
                        this.widths = '500px'
                    }else{
                        if(this.width.indexOf('px') == -1){
                            let winW = document.body.clientWidth;
                            this.widths = parseInt(this.width)*winW/100+'px'
                        }else {
                            this.widths = this.width
                        }

                    }
                    this.heights = '100%'
                }else if(this.aligns == 'top' || this.aligns == 'bottom'){
                    if(!this.height){
                        this.heights = '300px'
                    }else{
                        if(this.height.indexOf('px') == -1){
                            let winW = document.body.clientHeight;
                            this.heights = parseInt(this.height)*winW/100+'px'
                        }else {
                            this.heights = this.height
                        }
                    }
                    this.widths = '100%'
                }
            }else{
                this.aligns = 'right'
                if(!this.width){
                    this.widths = '500px'
                }else{
                    this.widths = this.width;
                    if(this.width.indexOf('px') == -1){
                        let winW = document.body.clientWidth;
                        this.widths = parseInt(this.width)*winW/100+'px'
                    }else {
                        this.widths = this.width
                    }
                }
                this.heights = '100%'
            }
        },
        mounted(){
            if(this.$scopedSlots.header){
                this.headerSlot = true
            }
        },
        beforeDestroy(){
        },
        methods: {
            //关闭按钮
            close() {
                this.$emit("update:visible",false);
                this.jdShow = false
                this.$emit("close")
                if(this.loading != undefined){
                    this.$emit("update:loading",false);
                }

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
                let hit = '300px'
                if(this.width){
                    wid = this.widths
                }
                if(this.height){
                    hit = this.heights
                }
                el.style.transition = "all "+time+"s ease"
                switch(this.aligns){
                    case "left":
                        el.style.transform = "translate(-"+wid+",0px)"
                        break;
                    case "right":
                        el.style.transform = "translate("+wid+",0px)"
                        break;
                    case "bottom":
                        el.style.transform = "translate(0px,"+hit+")"
                        break;
                    case "top":
                        el.style.transform = "translate(0px,-"+hit+")"
                        break;
                    default:
                        alert("目前只支持top,bottom,left,right")
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
                if(this.modalShow){
                    el.style.backgroundColor = "rgba(0,0,0,0.4)"
                }
                el.style.transition = "all 0.4s ease"
                done()
            },
            //遮罩动画结束之后
            drawerAfterEnter(el){
                this.jdShow = true
                this.$emit("opend")
            },
            drawerleave(el){
                el.style.backgroundColor = "rgba(0,0,0,0)"
                el.style.transition = "all 0.8s ease"
                this.$emit("closed")
            },
            //遮罩层隐藏
            drawerbeforeLeave(){
                this.beforeEnter(this.$refs.eject,0.2)
            },
            //遮罩层隐藏之后
            drawerafterLeave(){
                this.jdShow = false
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

        transition: 1s;
        overflow: hidden;
    }
    .Eject{
        position: absolute;
        background-color: #fff;
        display: flex;
        flex-direction: column;
        transition: 1s;
        box-shadow: 10px -20px 10px 10px rgba(0,0,0,0.2);
    }
    .EjectTop{
        left: 0;
        top: 0;
    }
    .EjectBottom{
        left: 0;
        bottom: 0;
    }
    .EjectLeft{
        left: 0;
        top: 0;
    }
    .EjectRight{
        right: 0px;
        top: 0;
        margin: 0;
    }
    .loading{
        position: absolute;
        width: 100%;
        height: 100%;
        background-color: hsla(0,0%,100%,.9);
        z-index: 2099;
        /*background-color: rgba(0,0,0,0.7);*/
    }
    header {
        border-bottom: 1px solid #e8eaec;
    }
    .headDiv{
        height: 100%;
        display: flex;
        padding: 0 20px;
        line-height: 50px;
    }
    .title{
        flex: 1;
        font-size: 15px;
        color: #17233d;
        font-weight: 700;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        text-align: left;
    }
    .close{
        width: 30px;
        text-align: right;
        font-size: 18px;
        color: #909399;
        cursor: pointer;
        z-index: 2100;
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
    /*加载样式*/
    .loader {
        height: 100px;
        width: 20%;
        text-align: center;
        padding: 1em;
        display: inline-block;
        vertical-align: top;
        position: absolute;
        top: 40%;
        left: 50%;
        transform: translate(-50%,-50%);
    }
    /*
      Set the color of the icon
    */
    /*svg path{*/
    /*  fill: #409EFF;*/
    /*}*/

</style>
