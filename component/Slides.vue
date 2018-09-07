<template lang="pug">
  .slides-wrapper(:class='[mode]')
    .slides-viewport(:style='viewportStyles' @mouseenter='handleHoverIn' @mouseleave='handleHoverOut')
      template(v-for='(slide, index) in slides')
        Slide(:slide='slide' :index='index')
          template(slot='slide' slot-scope="slide")
            slot(
              name='slide'
              :slide='slide.slide'
              :index='slide.index'
            )
              img(:src='slide.slide')
    .slides-pager(v-if='config.pager')
      .slides-pager-item(
        v-for='(slide, index) in slides'
        :key='index'
        @click='handlePaginationClick(index)'
        :class='{ active: index===debutIndex }'
      )
        slot(name='slides-pager-item' :slide='slide' :index='index')
    .slides-controls(v-if='config.controls')
      a.prev.slides-controls-item(@click='handleControl("anticlockwise")' :class='prevDisabled')
      a.next.slides-controls-item(@click='handleControl("clockwise")' :class='nextDisabled')
</template>

<script>
import Slide from "./Slide";

export default {
  name: "Slides",
  props: {
    slides: Array,
    slidesConfig: Object
  },
  data() {
    return {
      debutIndex: 0,
      waitingDebutIndex: 1,
      slideDirection: "clockwise",
      timer: null,
      defaultConfig: {
        infinite: true,
        speed: 500,
        autoPlay: false,
        loopTime: 5000,
        pager: true,
        controls: true,
        mode: "horizontal"
      }
    };
  },
  components: {
    Slide
  },
  computed: {
    config() {
      return Object.assign({}, this.defaultConfig, this.slidesConfig);
    },
    viewportStyles() {
      return {
        width: `${this.config.width}px`,
        height: `${this.config.height}px`
      };
    },
    mode() {
      return this.config.mode;
    },
    slideCount() {
      return this.slides && this.slides.length;
    },
    prevDisabled() {
      return {
        "slides-controls-item-disabled":
          this.debutIndex === 0 && !this.config.infinite
      };
    },
    nextDisabled() {
      return {
        "slides-controls-item-disabled":
          this.debutIndex === this.slideCount - 1 && !this.config.infinite
      };
    }
  },
  methods: {
    clockwise() {
      let waitingDebutIndex = this.debutIndex + 1;
      if (waitingDebutIndex === this.slideCount) {
        waitingDebutIndex = 0;
      }
      return waitingDebutIndex;
    },
    anticlockwise() {
      let waitingDebutIndex = this.debutIndex - 1;
      if (waitingDebutIndex === -1) {
        waitingDebutIndex = this.slideCount - 1;
      }
      return waitingDebutIndex;
    },
    getNextDebutIndex(type) {
      return this[type]();
    },
    async debut(index) {
      // if infiite is false,end the loop on the first slide or last slide
      if (
        (index === 0 || index === this.slideCount - 1) &&
        !this.config.infinite
      ) {
        return;
      }
      this.pause();
      await this.animate(index);
      this.reset();
    },
    handleControl(direction) {
      this.slideDirection = direction;
      this.debut(this.getNextDebutIndex(direction));
    },
    handlePaginationClick(waitingDebutIndex) {
      // click self return
      if (this.debutIndex === waitingDebutIndex) {
        return;
      }

      if (this.debutIndex > waitingDebutIndex) {
        this.slideDirection = "anticlockwise";
      } else {
        this.slideDirection = "clockwise";
      }
      this.debut(waitingDebutIndex);
    },
    async animate(waitingDebutIndex) {
      const nextActor = this.$children[waitingDebutIndex];
      const stageActor = this.$children[this.debutIndex];
      // if only one slide no loop
      if (nextActor === stageActor) {
        return;
      }
      this.$emit("beforeChange", {
        from: this.debutIndex,
        to: waitingDebutIndex
      });
      await nextActor.reachBehindScene();
      stageActor.exit();
      nextActor.debut();
      this.debutIndex = waitingDebutIndex;
      this.$emit("afterChange", this.debutIndex);
    },
    autoPlay() {
      this.timer = setInterval(() => {
        // only when user in this slide tab, play slide
        if (!document.hidden) {
          this.slideDirection = "clockwise";
          this.debut(this.getNextDebutIndex("clockwise"));
        }
      }, this.config.loopTime);
    },
    handleHoverIn() {
      if (this.config.pauseOnHover) {
        this.pause();
      }
    },
    handleHoverOut() {
      if (this.config.pauseOnHover) {
        this.reset();
      }
    },
    pause() {
      if (this.config.autoPlay) {
        clearInterval(this.timer);
        this.timer = null;
      }
    },
    reset() {
      if (this.config.autoPlay) {
        this.autoPlay();
      }
    },
    prev() {
      this.handleControl("anticlockwise");
    },
    next() {
      this.handleControl("clockwise");
    },
    goToSlide(index) {
      this.debut(index);
    }
  },
  mounted() {
    if (this.config.autoPlay) {
      this.autoPlay();
    }
  }
};
</script>

<style>
.slides-wrapper {
  position: relative;
}
.slides-wrapper.vertical .slide-item {
  transform: translateY(100%);
}
.slides-wrapper.horizontal .slide-item {
  transform: translateX(100%);
}
.slides-wrapper.hide .slide-item {
  opacity: 0;
}
.slides-wrapper.hide .on-stage {
  opacity: 1;
}
.slides-wrapper.horizontal .on-stage {
  z-index: 2;
  transform: translateX(0);
}
.slides-wrapper.vertical .on-stage{
  z-index: 2;
  transform: translateY(0);
}
.slides-wrapper .slides-viewport {
  position: relative;
  overflow: hidden;
}
.slides-wrapper .slides-pager {
  position: relative;
  z-index: 2;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 30px;
  margin-top: -30px;
}
.slides-wrapper .slides-pager .slides-pager-item {
  width: 15px;
  height: 15px;
  border-radius: 15px;
  background: rgba(0, 0, 0, 0.1);
}
.slides-wrapper .slides-controls-item {
  position: absolute;
  width: 26px;
  height: 50px;
  cursor: pointer;
  top: 50%;
  margin-top: -25px;
  z-index: 9999;
  background-position: -78px 0;
  background-repeat: no-repeat;
}
.slides-wrapper .slides-controls-item:hover {
  background-position: 0 0;
}
.slides-wrapper .slides-controls .prev {
  left: 20px;
  background-image: url(//p5.ssl.qhimg.com/t01c56279442f9810e5.png);
}
.slides-wrapper .slides-controls .next {
  right: 20px;
  background-image: url(//p0.ssl.qhimg.com/t016bcd37ca288ebfa7.png);
}
.slides-controls-item-disabled {
  display: none;
  pointer-events: none;
}
</style>
