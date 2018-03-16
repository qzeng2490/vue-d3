<template>
  <div class="rollNumber" ref="roll" @changeNum='handleChange'>
    <div class="item" v-for="(i, index) in N" :key="index" >
      <div class="cur" >{{i}}</div>
      <div class="next" ></div>
    </div>
  </div>
</template>

<script type="text/babel">
export default {
  name: "demo",
  mounted: function() {
    this.numSpilt();
    // this.counter();
    setTimeout(() => {
      console.log("=====");
      this.$emit('changeNum', { message: "12345"});
    },4000)
  },
  props: {
    num: {
      type: Number,
      default: 342
    }
  },
  data() {
    return {
      N: []
    };
  },
  methods: {
    counter() {
      // console.log(document.querySelectorAll(".rollNumber"));

      // var counterTens = new Counter(
      //     document.querySelector('[data-js="counter-tens"]')
      //   ),
      //   counterOnes = new Counter(
      //     document.querySelector('[data-js="counter-ones"]')
      //   ),
      //   count = 0;
      
      // [].forEach.call(this.$refs.roll.querySelectorAll(".item"), function(){　
      // 　　console.log("=========");　　　　　　　
      // });

      

      function Counter(el) {
        var current = el.querySelector('[data-js="current"]'),
          next = el.querySelector('[data-js="next"]'),
          count,
          timeout;

        function update(value) {
          if (count === value) {
            return;
          }
          count = value;
          next.innerHTML = count;
          el.classList.add("is-changing");
          window.clearTimeout(timeout);
          timeout = window.setTimeout(function() {
            current.innerHTML = next.innerHTML;
            el.classList.remove("is-changing");
          }, 210);
        }

        return {
          update: update
        };
      }

      function increment() {
        count++;

        if (count > 99) {
          count = 0;
        }

        var tens = Math.floor(count / 10);
        var ones = count % 10;

        counterTens.update(tens);
        counterOnes.update(ones);

        setTimeout(increment, 1000);
      }

    },
    handleChange: function(payload) {
      // console.log(e);
      // console.log(payload);
      console.log("changeNumchangeNum");
    },
    numSpilt() {
      this.N = (this.num + "").split("");
    },
    watch: {
      num(newVale, oldValue){
        this.numSpilt()
      }
    }

  }
};
</script>

<style lang="less">
.item {
  align-items: center;
  color: #dddddd;
  display: flex;
  font: 100px Helvetica;
  justify-content: center;
}

.is-changing {
  transform: translateY(-100px);
  transition: transform 200ms cubic-bezier(0.68, -0.55, 0.265, 1.55);
}
</style>