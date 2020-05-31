<template>
  <div>
    <div class="odometerBox">
      <odometer-digit v-for="(digitValue, digitIndex) in digitArray" :key="digitIndex" 
        :value="digitValue" 
        :direction="direction" 
        @forceSpin="forceSpin(digitIndex, ...arguments)"
        @current="updateCurrent(digitIndex, ...arguments)"
      />
    </div>
    <div>
      <button @click="updateTarget(1)">+</button>
      <button @click="updateTarget(-1)">-</button>
      <button @click="updateTarget(null, Math.floor(Math.random()*100000))">rand</button>
      <button @click="setTimer">go</button>
      <br/>
      <input value="" @keydown.enter="updateTarget(null, Number($event.target.value))" />
      <br/>
      current: {{ current }}
      <br/>
      target: {{ target }}
      <br/>
      direction: {{ direction }}
      <br/>
      pending: {{ pending }}
    </div>
  </div>
</template>

<script>
import odometerDigit from './odometerDigit.vue'

export default {
  name: 'Odometer',
  components: {
    odometerDigit,
  },
  data() {
    let v = 1299;
    let arr = Array.from(String(v).padStart(5,'0'), Number).reverse();
    return {
      target: v,
      direction: 'up',
      digitArray: arr,
      pending: null,
      timer: null,
    }
  },
  watch: {
    target(newValue, oldValue) {
      if (oldValue === newValue) return;
      this.resolve();
    }
  },
  computed: {
    current() {
      return Number(this.digitArray.slice().reverse().join(''));
    }
  },
  methods: {
    updateTarget(x, newTarget = null) {
      if (newTarget) 
        this.target = newTarget;
      else
        this.target += x;
    },
    setTimer() {
      if (this.timer) {
        clearInterval(this.timer);
        this.timer = null;
      } else {
        this.timer = setInterval(() => this.updateTarget(1), 300);
      }
    },
    setSpinnerDigit(i, direction, char=null) {
      if (char === null) {
        char = direction == 'up' ? (this.digitArray[i] + 1) % 10 : (this.digitArray[i] + 9) % 10
      }
      console.log(`set index ${i} to ${char} (${direction})`);
      this.direction = direction;
      this.$set(this.digitArray, i, char);
    },
    forceSpin(sourceIndex, indexDelta, direction) {
      let i = sourceIndex+indexDelta;
      console.log('force spin', sourceIndex, indexDelta, i,  direction);
      if (direction == 'up' && !this.digitArray.hasOwnProperty(i)){
        this.digitArray.push(0);
        //this.currArray.push(0);
      } 
      this.setSpinnerDigit(i, direction);
    },
    resolve() {
      if (this.target === this.current) return;
      if (this.target < this.current) {
        this.direction = 'down';
      }
      if (this.target > this.current) {
        this.direction = 'up';
      }
      // const currList = this.currArray.slice();
      const targetList = Array.from(String(this.target).padStart(this.digitArray.length,'0'), Number).reverse();
      console.log('looping over v', targetList, this.digitArray);
      for (const[i, v] of targetList.entries()) {
        console.log(`comparing (${i}): ${v}, ${this.digitArray[i]}`)
        if(v != this.digitArray[i]) {
          this.pending = i;
          this.setSpinnerDigit(i, this.direction, v);
          break;
        }
      }
    },
    updateCurrent(index, value) {
      console.log(`called ${index} ${value}, ${this.pending}`);
      if (this.pending === index) {
        //console.log('splicing');
        this.pending = null;
        console.log(this.pending);
        //this.$set(this.currArray, index, value);
        //console.log("new current: ", this.currArray);
        this.resolve();
      }
    }
  }
}
</script>

<style>
.odometerBox {
  display:flex;
  flex-direction: row-reverse;
  justify-content: center;
}
</style>
