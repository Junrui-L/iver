<template>
  <div :class="prefixCls">
    <div @touchstart="touchstart" @touchmove.stop.prevent="touchmove"  @touchend="touchend" :class="[{isTransition:isTransition} , prefixCls+'-wrapper']">
      <a href="javascript:;" @click="forward(img.href)" v-for="img in imgs">
        <img :src="img.imgUrl">
      </a>
    </div>
    <ul :class="prefixCls+'-dot'">
      <li v-for="(dot , index) in dots" :class="{active:dots[index]}"></li>
    </ul>
  </div>
</template>



<script type="text/javascript">
  import './swiper.scss';
  export default {
      name:'swiper',
      data(){
          return {
              prefixCls:'iv-swiper',
              isTransition:false,
              currentX:0,
              startX:0,
              width:0,
              index:0,
              dots :[],
              timer:null //timer
          };
      },
      props:{
          imgs:{
              type:Array,
              default(){
                  return [];
              }
          },
          defaultIndex:{
              type:Number,
              default:0
          },
          auto:{
              type:Boolean,
              default:false
          }
      },
      computed:{
          itemLength(){
              return this.imgs.length;
          }
      },
      mounted(){
          this.width = this.$el.offsetWidth;
          this.el = this.$el.querySelector('.iv-swiper-wrapper');

          this.index = this.defaultIndex;

          this.dots = this.imgs.map((item , index)=>{
              return index==this.index ? true : false;
          });

          this.setNowImg();

          this.auto && this.autoSwiper();

      },
      methods:{
          touchstart(e){

              this.auto && clearInterval(this.timer);

              e = e.changedTouches[0];
              this.isTransition = false;
              this.startX = e.pageX;
          },
          touchmove(e){

              e = e.changedTouches[0];

              var distant = e.pageX - this.startX,
                  offset = this.currentX+distant;
              this.el.style.webkitTransform = `translate3d(${offset}px, 0, 0)`;
          },
          touchend(e){
              this.isTransition = true;
              e = e.changedTouches[0];

              var distant = e.pageX - this.startX,
                  distantABS = Math.abs(distant);

              //change img
              if (distantABS>this.width/6) {
                  if (distant<0) {
                      this.index++;
                      this.index = (this.index > this.itemLength-1)?(this.itemLength-1):this.index;
                  }else{
                      this.index--;
                      this.index = (this.index < 0)?0:this.index;
                  }
              }
              setTimeout(()=>{this.setNowImg();},0);
        

              //init
              this.startX = 0;
              this.auto && this.autoSwiper();

          },
          dotHandler(){
              this.dots = this.dots.map((item , index)=>{
                  return index==this.index ? true : false;
              });
          },
          setNowImg(){
              this.currentX = -this.index*this.width;
              var offset = this.currentX;
              this.el.style.webkitTransform = `translate3d(${offset}px, 0, 0)`;

              this.dotHandler();
          },
          // auto swiper
          autoSwiper(){
              this.isTransition = true;
              this.timer = setInterval(()=>{
                  this.index++;

                  if (this.index > this.itemLength-1) {
                      this.index = 0;
                      this.isTransition = false;
                  }else{
                      this.isTransition = true;
                  }
                  this.setNowImg();
              },2000);
          },
          forward(href){
              (!href || href=='') || this.$router.push(href);
          }
      }
  };
</script>
