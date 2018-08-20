<template>
  <div id="main">
    <transition-group
      name='fade'
      tag="ul">
      <li
        v-for="(val,index) in childNodes"
        :key="index"
        v-if="page==index"
        v-finger:touch-start="touchStart"
        v-finger:touch-move="touchMove"
        v-finger:touch-end="touchEnd"
        v-finger:touch-cancel="touchCancel">

        <div class="child" v-random_bgc="val.bgC">
          <!--图一-->
          <transition
            name="bounce"
            enter-active-class="bounceInLeft"
            leave-active-class="bounceOutRight"
          >
            <img :src="val.imgArr[0]" alt="" v-if="val.show[0]">
          </transition>
          <!--图二-->
          <transition
            name="bounce"
            enter-active-class="bounceInRight"
            leave-active-class="bounceOutLeft"
          >
            <img :src="val.imgArr[1]" alt="" v-if="val.show[1]">
          </transition>
          <!--文字-->

          <transition
            name="zoom"
            enter-active-class="zoomInLeft"
            leave-active-class="zoomOutLeft"
          >
            <p v-if="val.show[2]">
              {{val.text1}}
            </p>
          </transition>
          <!--btn-->
          <transition
            name="bounce"
            enter-active-class="bounceInUp"
            leave-active-class="bounceOutBottom"
          >
            <button class="next" @click="next" v-if="val.show[3]"></button>
          </transition>

        </div>


      </li>
    </transition-group>
    <img class="music"
         src="../assets/music.png"
         alt=""
         ref="music"
         @click="action">

    <audio src="../assets/kmj.mp3"
           preload="auto"
           loop="loop"
           ref="audio"
    >
    </audio>
    <!--
        <audio :src="audioSource"
               autoplay="autoplay"
               preload="auto"
               loop="loop"
               ref="audio"
               >
        </audio>
    -->
  </div>
</template>


<script>
  export default {
    name: "Main",
    data() {
      return {
        lock: true,//锁
        show: true,
        page: '',
        startClientY: 0,
        endClientY: 0,
        audioSource: require('../assets/kmj.mp3'),
        childNode: [
          {
            val: 1,
            bgC: 'rgb(255,255,255)',
            imgArr: [
              require('../assets/num1/1.jpg'),
              require('../assets/num1/2.jpg'),
            ],
            text: ` Lorem ipsum dolor sit amet, consectetur adipisicing elit. Architecto aspernatur dignissimos ducimus fugit
              harum magnam nesciunt nihil qui sit vel? Aliquam ea eligendi eos eveniet fugiat incidunt minus molestias
              repellendus.`,
            text1: ' ',
            show: [false, false, false, false],
          },
          {
            val: 2,
            bgC: 'rgb(255,255,255)',
            imgArr: [
              require('../assets/num2/1.jpg'),
              require('../assets/num2/2.jpg'),
            ],
            text: ` Lorem ipsum dolor sit amet, consectetur adipisicing elit. Architecto aspernatur dignissimos ducimus fugit
              harum magnam nesciunt nihil qui sit vel? Aliquam ea eligendi eos eveniet fugiat incidunt minus molestias
              repellendus.`,
            text1: ' ',
            show: [false, false, false, false],
          },
          {
            val: 3,
            bgC: 'rgb(255,255,255)',
            imgArr: [
              require('../assets/num3/1.jpg'),
              require('../assets/num3/2.jpg'),
            ],
            text: ` Lorem ipsum dolor sit amet, consectetur adipisicing elit. Architecto aspernatur dignissimos ducimus fugit
              harum magnam nesciunt nihil qui sit vel? Aliquam ea eligendi eos eveniet fugiat incidunt minus molestias
              repellendus.`,
            text1: ' ',
            show: [false, false, false, false],
          },
        ]
      }
    },
    watch: {
      page: function (newPage, oldPage) {

        this.getPromise(newPage, oldPage)
          .then((data) => {
            console.log(data);
            this.lock = false;
          }).catch((err) => {
          this.lock = true;
          throw new Error(err);
        })
        ;


        /*  setTimeout(function(){
            console.log(this)
            this.$set(this.childNode[newPage],'show', [true, false, false]);
            this.$set(this.childNode[newPage],'show', [true, true, false]);
            this.$set(this.childNode[newPage],'show', [true, true, true]);
          }.bind(this), 2000);*/
      }
    }
    ,
    computed: {
      childNodes: function () {
        return this.childNode
      }
    }
    ,
    directives: {
      random_bgc: {
        inserted: function (el, binding) {
          // setTimeout(() => {
          el.className += ' my-transition';
          el.style.backgroundColor = binding.value;
          // }, 500);
        },
        unbind: function (oldNode) {
          console.log(oldNode)
          // oldNode.remove('my-transition')
        }
      }
    }
    ,
    created: function () {
      this.page = -1;
      this.childNode.map(val => {
        let r = this.random();
        let g = this.random();
        let b = this.random();
        val.bgC = `rgb(${r},${g},${b})`;
        return val
      });
      this.next();
    }
    ,
    mounted: function () {
      // this.action(this.audioSource);
      console.log(this.$refs.audio)
      this.action();
    }
    ,
    methods: {
      action() {
        let audio = this.$refs.audio;
        let music = this.$refs.music;

        console.log(audio);
        if (audio.paused) {
          audio.play();
          music.className += ' music-start';
        }
        else {
          audio.pause();
          var reg = new RegExp("(\\s|^)" + 'music-start' + "(\\s|$)");
          music.className = music.className.replace(reg, " ");
        }
      },
      getPromise(newPage, oldPage) {
        return new Promise((resolve) => {
          for (let i = 0; i < this.childNode[newPage].show.length; i++) {
            ((ms, t) => {
              setTimeout(() => {
                this.childNode[newPage].show.splice(t, 1, true);
                if (i == 2) {
                  // console.log(this.childNode[newPage].text)
                  var nodes = this.childNode[newPage].text.split(' ')
                  console.log(nodes)
                  for (let o = 0; o < nodes.length; o++) {
                    ((o) => {
                      setTimeout(() => {
                        this.childNode[newPage].text1 += ' ' + nodes[o] + ' ';
                      }, 1000);
                    })(o);
                    console.log(this.childNode[newPage].text1)
                  }
                }
                console.log(t)
                if (t == this.childNode[newPage].show.length - 1) {
                  return resolve(1)
                }
              }, ms);
            })(1500 + i * 500, i)
          }

          if (oldPage || oldPage === 0) {
            this.childNode[oldPage].text1 = '';
            for (let i = 0; i < this.childNode[oldPage].show.length; i++) {
              this.childNode[oldPage].show.splice(i, 1, false);
            }
          }

        })
      },
      touchStart(e) {
        console.log('onTouchStart');
        console.log(e)
        console.log(e.changedTouches[0].clientY)
        this.startClientY = e.changedTouches[0].clientY;
      },
      touchMove(e) {
      },
      touchEnd(e) {
        console.log('onTouchEnd');
        this.endClientY = e.changedTouches[0].clientY;
        console.log(this.startClientY - this.endClientY)
        if (this.startClientY - this.endClientY > 30 && !this.lock) {
          this.next();
        }
        else if (this.startClientY - this.endClientY < -30 && !this.lock) {
          this.next(1);
        }
        else {
          return;
        }
      },
      touchCancel() {
      },
      random() {
        return Math.floor(Math.random() * 255 - 1);
      }
      ,
      next(arg) {
        this.lock = true;
        console.log(this.lock);
        if (arg) {
          this.page == 0 ? this.page = this.childNode.length - 1 : this.page--;
        }
        else {
          this.page == this.childNode.length - 1 ? this.page = 0 : this.page++;
        }
      }
    }
  }
</script>

<style scoped lang="less">
  @import '../variable';

  #main .my-transition {
    opacity: 1;
  }

  @keyframes myMusic {
    0% {
      transform: rotate(0deg);
    }
    25% {
      transform: rotate(90deg);
    }
    50% {
      transform: rotate(180deg);
    }
    75% {
      transform: rotate(270deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  .music {
    position: fixed;
    z-index: 100;
    width: 30px; /*no*/
    height: 30px; /*no*/
    right: 40px;
    top: 40px;
  }

  .music-start {
    animation: myMusic .4s infinite;
  }

  ul, #main, .child {
    width: @max;
    height: @max;
  }

  li {
    color: white;
    width: @max;
    height: @max;
    position: relative;
    .child {
      opacity: 0;
      padding: 20px; /*no*/
      box-sizing: border-box;
      position: relative;
      transition: opacity 2s;
      img {
        position: absolute;
        width: 200px;
        height: 200px;
        border-radius: 50%;
        left: 20px; /*no*/
        top: 50px; /*no*/
      }
      img:nth-child(2) {
        left: auto;
        top: auto;
        bottom: 50px; /*no*/
        right: 20px; /*no*/
      }
      p {
        position: absolute;
        width: @max;
        height: 200px;
        top: 50%;
        margin-top: -100px;
        font-size: 16px; /*no*/
        color: white;
      }
      .next {
        width: 70px;
        height: 92px;
        position: absolute;
        bottom: 10px;
        left: 50%;
        margin-left: -35px;
        background: url(../assets/shoushi.png) no-repeat;
        -webkit-background-size: cover;
        background-size: cover;
        border: 0;
        animation: mybtn .8s infinite;
      }
    }
  }

  @keyframes mybtn {
    0% {
      opacity: 1;
      bottom: 12px;
    }
    10% {
      opacity: .75;
      bottom: 14px;
    }
    20% {
      opacity: .5;
      bottom: 16px;
    }
    30% {
      opacity: .25;
      bottom: 18px;
    }
    40% {
      opacity: 0;
      bottom: 20px;
    }
    50% {
      opacity: 0;
      bottom: 22px;
    }
    60% {
      opacity: 0;
      bottom: 20px;
    }
    70% {
      opacity: .25;
      bottom: 18px;
    }
    80% {
      opacity: .5;
      bottom: 16px;
    }
    90% {
      opacity: .75;
      bottom: 14px;
    }
    100% {
      opacity: 1;
      bottom: 12px;
    }
  }
</style>
