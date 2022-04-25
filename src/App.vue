<template>
  <Header />
  <div class="content">
    <LeftContent />
    <RightContent />
  </div>
  <Footer />
</template>

<script>
import Header from './components/layout/Header.vue'
import LeftContent from './components/layout/LeftContent.vue'
import RightContent from './components/layout/RightContent.vue'
import Footer from './components/layout/Footer.vue'

export default {
  name: 'App',
  components: {
    Header,
    LeftContent,
    RightContent,
    Footer
  }
}
</script>

<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: inherit;
  }

  #app {
    height: 100vh;
    width: 100vw;
  }

  .content {
    height: 85%;
    display: flex;
  }
</style>