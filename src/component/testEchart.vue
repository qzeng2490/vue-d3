<template>
  <div class="iCountUp">
    <ICountUp
      :startVal="startVal"
      :endVal="endVal"
      :decimals="decimals"
      :duration="duration"
      :options="options"
    />
  </div>
</template>

<script type="text/babel">
  import ICountUp from 'vue-countup-v2';
  export default {
    name: 'demo',
    components: {
      ICountUp
    },
    data() {
      return {
        startVal: 0,
        endVal: 9,
        decimals: 0,
        duration: 2.5,
        options: {
          useEasing: true,
          useGrouping: true,
          separator: ',',
          decimal: '.',
          prefix: '',
          suffix: ''
        }
      };
    },
    methods: {
      
    }
  };
</script>

<style scoped>
  .iCountUp {
    font-size: 12em;
    margin: 0;
    color: #191a22;
  }
</style>