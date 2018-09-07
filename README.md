# vue-awesome-slides

## why another slides
most slides are implement by set a visible view,make all slides in one row and use position or transform to make the slide show in the view. the problem of this method is when you in slide one and you select slide three pager, the slide two must cross the view,this is the reason why another slides. I use 'debut' concept to resolve this problem,you can find it in code.

## Project setup
``` bash
npm i vue-awesome-slides --save
```
## Project use

``` bash
import Slide from 'vue-awesome-slides/component/Slides.vue'

# default use
<Slides :slides="slidesData" :slidesConfig="config" />

#custom slide
<Slides :slides="slidesData" :slidesConfig="config" />
  <template slot="slide" slot-scope="props">
      # your custom slide template, you can get data from props.slide
      <div>{{props.slide.xxx}}</div>   
  </template>

#custom slide control
<Slides :slides="slidesData" :slidesConfig="config" />
  <template slot="slide-pager-item" slot-scope="props">
      # your custom slide template, you can get data from props.slide
      <div>{{props.slide.xxx}}</div>   
  </template>
```

## demo 
with vue cli 3.0 [prototyping](https://cli.vuejs.org/guide/prototyping.html),you can start the demo quickly, just go demo directory and serve it

## Props

| Name           | Type     | Desc          |
| ---            | -------- |-----------    |
| slides         | Array    | slides data   |
| slidesConfig   | Object   | slides config |


#### slidesConfig

| Name           | Type     | Defalut          | Desc          |
| ---            | -------- |-----------    | ---- |
| mode   | String  | horizontal   | Type of transition between slides **'horizontal' 'vertical' 'fade'**|
| autoPlay        | Boolean  | false   | Slides will automatically transition |
| infinite        | Boolean  | true   | If true, clicking "Next" while on the last slide will transition to the first slide and vice-versa |
| speed | Number | 500 | Slide transition duration (in ms)|
| loopTime | Number | 5000 | The amount of time (in ms) between each auto transition|
| pager        | Boolean  | true   | If true, a pager will be added |
| controls        | Boolean  | true   | If true, a controls will be added |

## Methods
| Name           | Desc          |
| ---            |-----------    |
| goToSlide         | Performs a slide transition to the supplied slide index (zero-based)   |
| goToNextSlide   | Performs a "Next" slide transition |
| goToPrevSlide   | Performs a "Prev" slide transition |

## Events
| Name           | Desc          |
| ---            |-----------    |
| beforeChange         | when slide start transition emit   |
| afterChange   |  when slide end transition emit |


## custom 

you can custom your own slide and slide pager and with [vue scoped slots](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots) every slide data is back to you the slot name is **slide** and **slide-pager-item** 