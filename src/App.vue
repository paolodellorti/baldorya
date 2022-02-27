<template>
  <transition name="slide-fade">
    <starter-component v-if="showStarter" @completed="showStarter = false" />
  </transition>
  <transition name="slide-fade">
    <main-component v-if="!showStarter" @backHome="showStarter = true"/>
  </transition>
</template>

<script>
import StarterComponent from './components/StarterComponent.vue'
import MainComponent from './components/MainComponent.vue'

export default {
  name: 'App',
  components: {
    StarterComponent,
    MainComponent
  },
  data() {
    return {
      showStarter: true
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Anton&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Titillium+Web:ital,wght@0,300;0,400;0,600;1,400;1,600&display=swap');


#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #fff;
}
body {
  background-color: black;
  margin: 0;
  font-family: 'Titillium Web', sans-serif;
}
span {
  color: #41B883;
}
.slide-fade-enter-active {
  transition: all 0.5s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}
body::-webkit-scrollbar {
    width: 1px;
}
body::-webkit-scrollbar-track {
    background-color: #000;
}
</style>
