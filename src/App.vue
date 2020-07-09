<template>
  <div id="app">
    <Search/>
  </div>
</template>

<script>
import Search from './components/Search'

export default {
  name: 'App',
  components: {
    Search
  }
}
</script>

<style>
html, body {
  padding: 0;
  margin: 0;
  overflow-x: hidden;
  width: 100%;
  bottom: 0;
  height: 100vh;
  background: linear-gradient(145.74deg, #9BDBFF -33.02%, #B4DEDA 52.01%, #FFD66B 137.04%);
}
#app {
  font-family: Poppins;
  font-style: normal;
  font-weight: 600;
  font-size: 12px;
  line-height: 24px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

}
</style>
