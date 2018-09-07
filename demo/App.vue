<template lang="pug">
  #app
    Slides(:slides='slides1' :slidesConfig='config1' ref="slide")
    Slides(:slides='slides2' :slidesConfig='config2')
      .item(slot="slide" slot-scope="props")
        img(:src="props.slide.img")
        .text {{props.slide.text}}
</template>

<script>
import Slides from '../component/Slides'
export default {
  name: 'app',
  data () {
    return {
      config1: {
        width: 234,
        height: 126,
        autoPlay: false,
        infinite: true,
        pager: true,
        controls: true,
        speed: 500,
        loopTime: 3000,
        mode: 'hide'
      },
      config2: {
        width: 234,
        height: 126,
        autoPlay: false,
        infinite: true,
        pager: true,
        controls: true,
        speed: 500,
        loopTime: 5000,
        mode: 'vertical'
      },
      slides1: [
        'https://p5.ssl.qhimg.com/t01ded53a04954318dd.png',
        'https://p0.ssl.qhimg.com/t0174714a81675527b4.png',
        'https://p0.ssl.qhimg.com/t014e83eddbe0859601.png'
      ],
      slides2: [
        {img: 'https://p5.ssl.qhimg.com/t01ded53a04954318dd.png',text: 'pic1'},
        {img: 'https://p0.ssl.qhimg.com/t0174714a81675527b4.png',text: 'pic2'},
        {img: 'https://p0.ssl.qhimg.com/t014e83eddbe0859601.png',text: 'pic3'}  
      ]
    }
  },
  components: {
    Slides
  },
  mounted () {
    // setTimeout(() => {
    //   this.$refs.slide.goToSlide(1)
    // }, 2000);
  }
}
</script>

<style>
 body{
   width: 234px;
 }
.item{
  position: relative;
}
.item .text{
  position: absolute;
  bottom: 0;
  z-index: 100;
}
</style>
