<template>
  <div class="layout">
    <div class="layout__animation">
      <Animation/>
    </div>
    <div class="layout__content">
      <nuxt/>
    </div>
    <div class="layout__navigation">
      <Navigation/>
    </div>
  </div>
</template>

<script>
  import Navigation from '~/components/Navigation';
  import Animation from '~/components/Animation';

  export default{
    components: {
      Navigation,
      Animation
    }
  }
</script>

<style>
body{
  background: #000;
  font-family: helvetica,sans-serif;
  position: relative;
}
.layout__navigation{
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
}

.layout__content{
  font-size: calc(20px + 4vw);
  letter-spacing: -1px;
  font-weight: bold;
  color: #fff;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  min-height: 100vh;
  
  display: flex;
  justify-content: center;
  text-align: center;
  align-items: center;
}

.flip-enter-active{
  transition: all .3s ease-in-out;
}

.flip-leave-active{
  transition: all .3s ease-in-out;
}

.flip-enter{ transform: translateY(-100%);opacity:0 }
.flip-enter-to{ transform: translateY(0%);opacity:1 }

.flip-leave{ transform: translateY(0);opacity:1 }
.flip-leave-to{ transform: translateY(100%);opacity:0; }
.letter{
  display: inline-block;
  position: relative;
}
</style>
