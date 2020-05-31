<template>
  <div class="digit-display">
      <div class="three" @animationend="endSpin" ref="spinnerEl">
        <span v-for="(digit,i) in spinnerDigits" :key="i">{{ digit }}</span>
      </div>
  </div>
</template>

<script>
export default {
  name: 'OdometerDigit',
  props: ['value', 'direction'],
  data() {
    return {
      current: this.value,
    }
  },
  computed: {
    spinnerDigits() {
      return [
        (this.current + 2) % 10,
        (this.current + 1) % 10,
        this.current,
        (this.current + 9) % 10,
        (this.current + 8) % 10
      ];
    },
  },
  watch: {
    value(newValue) {
      if (this.current != newValue) {
        this.$refs.spinnerEl.classList.add(this.direction == 'up' ? 'increment' : 'decrement');
        this.carryDigits();
      }
    }
  },
  methods: {
    endSpin() {
      let className = null;
      if (this.direction == 'up') {
        this.current = this.spinnerDigits[1];
        className = 'increment';
      } else {
        this.current = this.spinnerDigits[3];
        className = 'decrement';
      }
      this.$refs.spinnerEl.classList.remove(className);
      void this.$refs.spinnerEl.offsetWidth;
      if (this.current != this.value) {
        this.$refs.spinnerEl.classList.add(className);
        this.carryDigits();
      } else {
         this.$emit('current', this.current);
      }
    },
    carryDigits() {
      if (this.direction == 'up' && this.current == 9) this.$emit('forceSpin', 1, 'up');
      else if (this.direction == 'down' && this.current == 0) this.$emit('forceSpin', 1, 'down');
    }
  }
}
</script>

<style scoped>
.digit-display{
  display: inline-block;
  line-height: .9rem;
  height: 1.4rem;
  overflow: hidden;
  border: thin solid grey;
  position: relative;
  padding: 0 .1rem;
}
.three{
  position: relative;
  top: -1.5rem;
  display: flex;
  flex-direction: column;
}
.increment {
  animation: increment .12s ease;
}
.decrement {
  animation: decrement .12s ease;
}
@keyframes increment {
  from {top: -1.5rem;}
  to {top: -.6rem;}
}
@keyframes decrement {
  from {top: -1.5rem;}
  to {top: -2.4rem;}
}
.digit-display:after{
    content: "";
    position: absolute; top: 0; bottom: 0; left: -1rem; right: -1rem;
    box-shadow: inset white 0 0 .3rem .2rem;
}
</style>