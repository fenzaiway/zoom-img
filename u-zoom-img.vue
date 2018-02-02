<template>
  <Modal 
    class="u-zoom-img"
    class-name="vertical-center-modal"
    v-model="isShow"
    :closable="false"
    :width="modalWidth"
    :mask-closable="false"
    >
    <div slot="header"></div>
    <div class="light-box-container">
        <span class="light-box-close" @click="closeHandler"><Icon type="ios-close-empty"></Icon></span>
        <span @click="preImg" ><Icon class="light-box-arrow light-box-arrow-left"  type="ios-arrow-left"></Icon></span>
        <div class="img-container">
            <transition 
                mode="out-in" 
                name="fade"
                >
                <img v-if="imgShow"  class="light-box-img" :src="imgs[currentIndex]" alt="图片加载失败" />
            </transition>
            <div class="current-tip"  v-if="imgShow">{{currentIndex+1}}/{{imgCount}}</div>
        </div>
        <span @click="nextImg"><Icon class="light-box-arrow light-box-arrow-right" type="ios-arrow-right"></Icon></span>
    </div>
    <div slot="footer"></div>
  </Modal>
</template>

<script>
let $ = require('jquery')
let lodash = require('lodash')
export default {
    name:'u-zoom-img',
    props:{
        
    },
    mounted(){
        this.$nextTick(()=>{
            this.init()
            this.initEvent()
        })
    },
    data() {
        return {
            currentIndex:-1,
            isShow: false,
            imgs: [],
            imgCount:0,
            imgShow:false,
            modalWidth: 0

        }
    },
    methods:{
        init(){
            $(()=>{
                let imgEls = $('body').find('img.zoom-img')
                this.imgs = []
                lodash.forEach(imgEls, item=>{
                    this.imgs.push(item.src)
                })
                this.imgCount = this.imgs.length
                this.showImg()
            })
        },
        preImg(){
            this.imgShow = false
            this.currentIndex = this.currentIndex - 1
            if(this.currentIndex < 0){
                this.currentIndex = this.imgCount-1
            }
            this.showImg()
        },
        nextImg(){
            this.imgShow = false
            this.currentIndex = this.currentIndex + 1
            if(this.currentIndex >= this.imgCount){
                this.currentIndex = 0
            }
            this.showImg()
        },
        showImg(){
            this.tout && clearTimeout(this.tout)
            this.tout = setTimeout(() =>{
                let img = new Image();
                // 改变图片的src
                img.src = this.imgs[this.currentIndex];
                // 判断是否有缓存
                if(img.complete){
                    this.modalWidth = img.width
                }else{
                    // 加载完成执行
                    img.onload = function(){
                        this.modalWidth = img.width
                    }
                };
                if(this.modalWidth > 1024){
                    this.modalWidth = 1024
                }
                this.imgShow = true
            }, 200);
        },
        initEvent(){
            window.addEventListener('keyup', (event) => {
                if (event.which === 27) {
                    this.imgDialog = false;
                }
                if (event.which === 39) {
                    this.nextImg();
                }
                if (event.which === 37) {
                    this.preImg();
                }
            });
            
            let _that = this
            $('body').on('click', 'img.zoom-img', function(){
                _that.currentIndex = $(this).index()
            })
        },
        closeHandler(){
            this.isShow = false
            this.imgShow = false
            this.currentIndex = -1
            this.$emit('on-close')
        }
    },
    watch:{
        currentIndex(newVal){
            if(newVal >= 0){
                this.init()
                this.isShow = true
            }
            
        }
    }
}
</script>

<style lang=less>
    .u-zoom-img{
        .ivu-modal-mask{
            background-color: rgba(123,123,123,0.3)
        }
        .ivu-modal, .ivu-modal-content, .ivu-modal-body{
            background: rgba(255,255,255,0);
        }
        .ivu-modal-header, .ivu-modal-footer{
            display: none;
        }
        .fade-enter-active,
        .fade-leave-active {
            transition: opacity .2s ease;
        }

        .fade-enter, .fade-leave-to {
            opacity: 0;
        }

        .light-box-container{
            .light-box-close{
                position: absolute;
                display: inline-block;
                width: 38px;
                height: 38px;
                background: #fff;
                text-align: center;
                z-index: 10;
                right: 16px;
                cursor: pointer;
                .ivu-icon-ios-close-empty{
                    font-size: 50px;
                    margin-top: -6px;
                    color: #d9d9d9;
                }
            }
            .img-container{
                position: relative;
                .current-tip {
                    position: absolute;
                    right:0px;
                    bottom: 5px;
                    width:100%;
                    text-align: center;
                    font-size:14px;
                    background: rgba(0,0,0,0.6);
                    color:#fff;
                }
                .light-box-img{
                    width: 100%;
                    max-width: 1024px;
                }
            }
             .light-box-arrow{
                font-size: 50px;
                position: absolute;
                color:#fff;
                top: 50%;
                cursor: pointer;
                transition: all 0.8s;
            }
            .light-box-arrow:hover{
                color:#20A0FF
            }
            .light-box-arrow-left{
                left:-20px;
            }
            .light-box-arrow-right{
                right:-20px;
            }
        }
    }
    .vertical-center-modal{
        display: flex;
        align-items: center;
        justify-content: center;

        .ivu-modal{
            top: 0;
        }
    }
</style>
