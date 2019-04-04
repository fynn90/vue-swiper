## vue-swiper

*   基于 Vue2.0 开发，基本满足大部分功能。
*   轻量、高性能轮播插件。目前支持 无缝衔接自动轮播、无限轮播、手势轮播。
*   没有引入第三方库，原生 js 封装,打包之后只有 8.2KB 大小 性能还是杠杠滴。
* [新增] 垂直滑动
* [新增] 可禁止滑动

## [原项目github地址](https://github.com/zwhGithub/vue-swiper/)

## demo

![效果](http://zwhgithub.github.io/vue-swiper/dist/1514291260.png)

🎉 觉得好用给一个 star 哦~ 🎉

## Usage

```javascript
import { Swiper, Slide } from 'vue-swiper-component';

components: {
    Swiper,
    Slide
}

//异步加载轮播图的情况;
  <Swiper v-if="list.length > 0">
       <Slide v-for="(item,index) in list" :key="index">
       </Slide>
  </Swiper>


 //同步加载轮播图情况
  <Swiper>
       <Slide>
               1
       </Slide>
       <Slide>
       		2
       </Slide>
       <Slide>
       		3
       </Slide>
  </Swiper>

    //加一些参数配置情况
  <Swiper v-if="slidesReal.length > 0" :autoPlay='true' :showIndicator='true' interval="2500" duration="500">
        <Slide @click="clickMe" v-for="(item,index) in slidesReal" :key="index">
        	//添加click事件
        </Slide>
   </Swiper>
```

## API

| 属性          | 说明                     | 默认 |
| ------------- | ------------------------ | ---- |
| autoPlay      | 是否自动轮播             | true |
| showIndicator | 是否显示轮播的那个点     | true |
| interval      | 每两次隔多久滚动一次     | 2500 |
| duration      | 每次滚动一页需要多久时间 | 500  |
| vertical      | 是否是垂直运动 | false  |
| stopTouch     | 禁止滑动 | false  |

