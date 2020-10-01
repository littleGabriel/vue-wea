<template>
<!--  <img alt="Vue logo" src="./assets/logo.png">-->
  <weaMain msg="Vue Weather"/>
</template>

<script>
import weaMain from './components/weaMain.vue'

export default {
  name: 'App',
  components: {
    weaMain
  }
}
</script>

<style>
  @font-face {
    font-family: 'Material Icons Round';
    font-style: normal;
    font-weight: 400;
    src: url('components/font/Material Icons Round.woff2');
  }
  .material-icons-round {
    font-family: 'Material Icons Round',serif;
    font-weight: normal;
    font-style: normal;
    font-size: 24px;
    line-height: 1;
    letter-spacing: normal;
    text-transform: none;
    display: inline-block;
    white-space: nowrap;
    word-wrap: normal;
    direction: ltr;
    -webkit-font-smoothing: antialiased;
  }
  .cloudy{
    background: #16222A;  /* fallback for old browsers */
    background: -webkit-linear-gradient(to right, #3A6073, #16222A);  /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(to right, #3A6073, #16222A); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  }
  .sunny{
    background: #2193b0;  /* fallback for old browsers */
    background: -webkit-linear-gradient(to right, #6dd5ed, #2193b0);  /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(to right, #6dd5ed, #2193b0); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  }
  #app {
    /*font-family: sans-serif;*/
    /*-webkit-font-smoothing: antialiased;*/
    /*-moz-osx-font-smoothing: grayscale;*/
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
