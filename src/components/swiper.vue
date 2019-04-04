<template>
  <section class="wh_content" @touchmove="fn">
    <div :class="className" class="wh_swiper" @touchstart="s" @touchmove="m" @touchend="e">
      <slot/>
    </div>

    <div v-if="showIndicator" class="wh_indicator">
      <div
        v-for="(tag,$index) in slidesLength"
        :key="$index"
        v-bind:class="{ wh_show_bgcolor: index-1==$index }"
        class="wh_indicator_item"
      ></div>
    </div>
  </section>
</template>

<script>
    export default {
        data() {
            return {
                slidesLength: 1,
                _width: 0,
                _height: 0,
                auto: true,
                slideing: true,
                timer1: '',
                className: '',
                dom: {},
                t: {
                    sx: 0,
                    sy: 0,
                    s: 0,
                    m: 0,
                    e: 0
                },
                index: 1
            }
        },
        props: {
            //滑动所需要的时间
            autoPlay: {
                default: true
            },
            //一次滑动需要走多久
            duration: {
                default: 500
            },
            //两次滑动间隔的时间
            interval: {
                default: 2500
            },
            showIndicator: {
                default: true
            },
            // 垂直滑动
            vertical: {
                default: false
            },
            // 禁止滑动
            stopTouch: {
                default: false
            }
        },
        mounted() {
            this.className = `wh_swiper_${Math.random().toFixed(3) * 1000}`
            window.addEventListener('load', () => {
                setTimeout(() => {
                // 克隆dom
                this.starDom()
                if (this.vertical) {
                    this.dom.transform = `translate3d(0px, ${this._height * -1}px, 0px)`
                    this.dom['-webkit-transform'] = `translate3d( 0px, ${this._height * -1}px, 0px)`
                    this.dom['-ms-transform'] = `translate3d( 0px, ${this._height * -1}px, 0px)`
                } else {
                    this.dom.transform = `translate3d(${this._width * -1}px, 0px, 0px)`
                    this.dom['-webkit-transform'] = `translate3d(${this._width * -1}px, 0px, 0px)`
                    this.dom['-ms-transform'] = `translate3d(${this._width * -1}px, 0px, 0px)`
                }
                if (this.autoplay) {
                    this.setTime()
                }
                }, 50)
            })

        },
        methods: {
            s(x) {
                if (this.stopTouch) return
                if (this.slideing) {
                    this.clearTimeOut()
                    if (this.vertical){
                        this.t.sy = this.getTransform()
                        this.t.s = x.touches[x.touches.length - 1].clientY
                    } else {
                        this.t.sx = this.getTransform()
                        this.t.s = x.touches[x.touches.length - 1].clientX
                    }
                }
            },
            m(x) {
                if (this.stopTouch) return
                if (this.slideing && this.t.s != -1) {
                    this.clearTimeOut()
                    if (this.vertical) {
                        this.t.m = x.touches[x.touches.length - 1].clientY - this.t.s
                        this.setTransform(this.t.m + this.t.sy)
                    } else {
                        this.t.m = x.touches[x.touches.length - 1].clientX - this.t.s
                        this.setTransform(this.t.m + this.t.sx)
                    }

                }
            },
            e(x) {
                if (this.stopTouch) return
                if (this.slideing && this.t.s != -1) {
                    this.clearTimeOut()
                    if (this.vertical) {
                        this.setTransform(this.t.m + this.t.sy)
                        var x = this.getTransform()
                        x += this.t.m > 0 ? this._height * 0.3 : this._height * -0.3
                        this.index = Math.round(x / this._height) * -1
                        this.h('touch')
                    } else {
                        this.setTransform(this.t.m + this.t.sx)
                        var x = this.getTransform()
                        x += this.t.m > 0 ? this._width * 0.3 : this._width * -0.3
                        this.index = Math.round(x / this._width) * -1
                        this.wh('touch')
                    }
                }
            },
            setTransform(num) {
                if (this.vertical) {
                this.dom.transform = `translate3d(0px, ${num}px, 0px)`
                this.dom['-webkit-transform'] = `translate3d(0px, ${num}px, 0px)`
                this.dom['-ms-transform'] = `translate3d(0px, ${num}px, 0px)`
                } else {
                this.dom.transform = `translate3d(${num}px, 0px, 0px)`
                this.dom['-webkit-transform'] = `translate3d(${num}px, 0px, 0px)`
                this.dom['-ms-transform'] = `translate3d(${num}px, 0px, 0px)`
                }
            },
            getTransform() {
                var x = this.dom.transform || this.dom['-webkit-transform'] || this.dom['-ms-transform'];
                var val = x.split('(')[1].split(',');
                var s = 0
                if(this.vertical) {
                    s = val[1]
                } else {
                    s = val[0]
                }
                s = s.match(/(\S*)px/)[1]
                return Number(x)
            },
            fn(e) {
                e.preventDefault()
            },
            prevSlide() {
                this.clearTimeOut()
                this.index--
                this.vertical ? this.h() : this.wh()
            },
            nextSlide() {
                this.clearTimeOut()
                this.index++
                this.vertical ? this.h() : this.wh()
            },
            slideTo(index) {
                this.clearTimeOut()
                this.index = index + 1
                this.vertical ? this.h() : this.wh()
            },
            wh(type) {
                this.slideing = false
                this.dom.transition = type == 'touch' ? '250ms' : this.duration + 'ms'
                this.setTransform(this.index * -1 * this._width)
                this.t.m = 0
                this.t.s = -1 //保证下次重新赋值
                if (this.autoPlay) {
                    this.setTime()
                }
                var timeDuration = type == 'touch' ? '250' : this.duration
                setTimeout(() => {
                    this.dom.transition = '0s'
                    if (this.index >= this.slidesLength + 1) {
                        this.index = 1
                        this.setTransform(this.index * -1 * this._width)
                    }
                    if (this.index <= 0) {
                        this.index = this.slidesLength
                        this.setTransform(this.index * -1 * this._width)
                    }
                    this.$emit('transtionend', this.index - 1)
                    this.auto = true
                    this.slideing = true
                }, timeDuration)
            },
            h(type) {
                this.slideing = false
                this.dom.transition = type == 'touch' ? '250ms' : this.duration + 'ms'
                this.setTransform(this.index * -1 * this._height)
                this.t.m = 0
                this.t.s = -1 // 保证下次重新赋值
                if (this.autoplay) {
                this.setTime()
                }
                var timeDuration = type == 'touch' ? '250' : this.duration
                setTimeout(() => {
                this.dom.transition = '0s'
                if (this.index >= this.slidesLength + 1) {
                    this.index = 1
                    this.setTransform(this.index * -1 * this._height)
                }
                if (this.index <= 0) {
                    this.index = this.slidesLength
                    this.setTransform(this.index * -1 * this._height)
                }
                this.$emit('transtionend', this.index - 1)
                this.auto = true
                this.slideing = true
                }, timeDuration)
            },
            setTime() {
                this.timer1 = window.setTimeout(() => {
                    if (this.auto) {
                        this.index++
                       this.vertical ? this.h() : this.wh()
                    } else {
                        window.clearTimeout(this.timer1)
                    }
                }, this.interval)
            },
            starDom() {
                var SlideDom = document.querySelector('.' + this.className).getElementsByClassName('wh_slide')
                this.slidesLength = SlideDom.length
                if (this.slidesLength > 1) {
                    var cloneDom1 = SlideDom[0].cloneNode(true) //向最后append
                    var cloneDom2 = SlideDom[this.slidesLength - 1].cloneNode(true) //向最前append
                    document.querySelector('.' + this.className).insertBefore(cloneDom2, SlideDom[0])
                    document.querySelector('.' + this.className).appendChild(cloneDom1)
                    this._width = document.querySelector('.' + this.className).offsetWidth
                    this._height = document.querySelector('.' + this.className).offsetHeight
                    this.dom = document.querySelector('.' + this.className).style
                }
            },
            clearTimeOut() {
                this.auto = false
                window.clearTimeout(this.timer1)
            }
        }
    }
</script>

<style>
.wh_content {
  position: relative;
  z-index: 1;
  overflow: hidden;
  width: 100%;
}

.wh_swiper {
  width: 100%;
  display: -webkit-box;
  display: -moz-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -moz-transition-duration: 0s linear;
  -webkit-transition-duration: 0s linear;
  -o-transition-duration: 0s linear;
  transition-duration: 0s linear;
}

.wh_indicator {
  position: absolute;
  bottom: 8px;
  width: 100%;
  text-align: center;
  background: 0 0;
}

.wh_indicator_item {
  display: inline-block;
  width: 8px;
  height: 8px;
  margin: 1px 7px;
  cursor: pointer;
  border-radius: 100%;
  background: #aaa;
}

.wh_show_bgcolor {
  background: #0fc37c;
}
</style>