---
layout: post
title:  'Vue 3 组合式 API 使用 Swiper 插件的 Demo'
date:   2025-03-27 16:00:00 +0800
image:  
tags:   '前端'
---
### 下面是一个使用 Vue 3 组合式 API 和 Swiper 插件的完整示例：
### 使用说明
1.这个示例使用了两个 Swiper 实例：一个主轮播和一个缩略图轮播\
2.主轮播使用了淡入淡出效果，缩略图轮播可以控制主轮播的导航\
3.底部有三个控制按钮：上一张、下一张和播放/暂停\
4.组件卸载时会正确销毁 Swiper 实例以避免内存泄漏\

#### 完整组件代码

```javascript
<template>
  <div class="swiper-demo">
    <h2>Swiper Demo (Composition API)</h2>
    
    <!-- 主 Swiper -->
    <swiper
      v-bind="mainSwiperOptions"
      @swiper="setMainSwiper"
      @slide-change="onMainSlideChange"
      class="main-swiper"
    >
      <swiper-slide v-for="(slide, index) in slides" :key="index">
        <div class="slide-content">
          <img :src="slide.image" :alt="slide.title" class="slide-image" />
          <h3>{{ slide.title }}</h3>
          <p>{{ slide.description }}</p>
        </div>
      </swiper-slide>
    </swiper>
    
    <!-- 缩略图 Swiper -->
    <swiper
      v-bind="thumbsSwiperOptions"
      @swiper="setThumbsSwiper"
      class="thumbs-swiper"
    >
      <swiper-slide v-for="(slide, index) in slides" :key="index">
        <img :src="slide.image" :alt="slide.title" class="thumb-image" />
      </swiper-slide>
    </swiper>
    
    <div class="controls">
      <button @click="prevSlide">上一张</button>
      <button @click="nextSlide">下一张</button>
      <button @click="toggleAutoplay">
        {{ isAutoplay ? '暂停' : '播放' }}
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import { Swiper, SwiperSlide } from 'swiper/vue'
import { 
  Autoplay, 
  Pagination, 
  Navigation, 
  Thumbs,
  EffectFade 
} from 'swiper/modules'

// 导入 Swiper 样式
import 'swiper/css'
import 'swiper/css/pagination'
import 'swiper/css/navigation'
import 'swiper/css/effect-fade'

// 幻灯片数据
const slides = [
  {
    title: '风景 1',
    description: '美丽的自然风光',
    image: 'https://picsum.photos/800/500?random=1'
  },
  {
    title: '风景 2',
    description: '壮丽的山川河流',
    image: 'https://picsum.photos/800/500?random=2'
  },
  {
    title: '风景 3',
    description: '宁静的湖泊',
    image: 'https://picsum.photos/800/500?random=3'
  },
  {
    title: '风景 4',
    description: '日落的余晖',
    image: 'https://picsum.photos/800/500?random=4'
  },
  {
    title: '风景 5',
    description: '星空下的帐篷',
    image: 'https://picsum.photos/800/500?random=5'
  }
]

// Swiper 实例
const mainSwiper = ref(null)
const thumbsSwiper = ref(null)

// 控制状态
const isAutoplay = ref(true)
const activeIndex = ref(0)

// 主 Swiper 配置
const mainSwiperOptions = ref({
  modules: [Autoplay, Pagination, Navigation, Thumbs, EffectFade],
  slidesPerView: 1,
  spaceBetween: 20,
  effect: 'fade',
  fadeEffect: {
    crossFade: true
  },
  pagination: {
    clickable: true,
    dynamicBullets: true
  },
  navigation: true,
  thumbs: {
    swiper: thumbsSwiper.value
  },
  autoplay: {
    delay: 3000,
    disableOnInteraction: false
  },
  loop: true,
  grabCursor: true,
  on: {
    slideChange: (swiper) => {
      activeIndex.value = swiper.realIndex
    }
  }
})

// 缩略图 Swiper 配置
const thumbsSwiperOptions = ref({
  modules: [Navigation],
  slidesPerView: 4,
  spaceBetween: 10,
  freeMode: true,
  watchSlidesProgress: true,
  breakpoints: {
    640: {
      slidesPerView: 3
    },
    768: {
      slidesPerView: 4
    },
    1024: {
      slidesPerView: 5
    }
  }
})

// 设置 Swiper 实例
const setMainSwiper = (swiper) => {
  mainSwiper.value = swiper
}

const setThumbsSwiper = (swiper) => {
  thumbsSwiper.value = swiper
  // 更新主 Swiper 的 thumbs 配置
  mainSwiper.value.thumbs.swiper = swiper
}

// 幻灯片变化事件
const onMainSlideChange = () => {
  console.log('当前活动幻灯片索引:', activeIndex.value)
}

// 控制方法
const prevSlide = () => {
  mainSwiper.value?.slidePrev()
}

const nextSlide = () => {
  mainSwiper.value?.slideNext()
}

const toggleAutoplay = () => {
  if (isAutoplay.value) {
    mainSwiper.value?.autoplay.stop()
  } else {
    mainSwiper.value?.autoplay.start()
  }
  isAutoplay.value = !isAutoplay.value
}

// 组件挂载时
onMounted(() => {
  console.log('Swiper 组件已挂载')
})

// 组件卸载前
onBeforeUnmount(() => {
  if (mainSwiper.value) {
    mainSwiper.value.destroy()
    mainSwiper.value = null
  }
  if (thumbsSwiper.value) {
    thumbsSwiper.value.destroy()
    thumbsSwiper.value = null
  }
})
</script>

<style scoped>
.swiper-demo {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

h2 {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}

.main-swiper {
  height: 500px;
  margin-bottom: 20px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.thumbs-swiper {
  height: 100px;
  margin-bottom: 20px;
}

.slide-content {
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  padding: 20px;
  box-sizing: border-box;
  background-color: #f9f9f9;
  color: #333;
}

.slide-image {
  width: 100%;
  height: 300px;
  object-fit: cover;
  border-radius: 4px;
  margin-bottom: 15px;
}

.thumb-image {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 4px;
  cursor: pointer;
  opacity: 0.6;
  transition: opacity 0.3s;
}

.thumb-image:hover {
  opacity: 1;
}

.thumb-image.swiper-slide-thumb-active {
  opacity: 1;
  border: 2px solid #007aff;
}

.controls {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-top: 20px;
}

.controls button {
  padding: 8px 16px;
  background-color: #007aff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.controls button:hover {
  background-color: #005bb5;
}

/* 自定义分页器样式 */
:deep(.swiper-pagination-bullet) {
  width: 12px;
  height: 12px;
  background-color: #fff;
  opacity: 0.8;
}

:deep(.swiper-pagination-bullet-active) {
  background-color: #007aff;
}

/* 自定义导航按钮样式 */
:deep(.swiper-button-next),
:deep(.swiper-button-prev) {
  color: #007aff;
  width: 44px;
  height: 44px;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 50%;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

:deep(.swiper-button-next:after),
:deep(.swiper-button-prev:after) {
  font-size: 20px;
  font-weight: bold;
}
</style>
```
