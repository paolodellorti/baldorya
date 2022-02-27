<template>
  <div class="main">
      <div class="title" @click="$emit('backHome')">
        <img alt="baldorYa logo" src="../assets/logo.svg">
        <h1>baldor<span>y</span>a</h1>
      </div>
      <controller-component @changeArg="onChangeArg" :argument="argument" />
        <div key="what" v-if="argument === 'what'">
            <h3>What is <span>baldorya</span>?</h3>
            <p>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Veniam consequuntur, dolores quod asperiores velit sapiente dolor molestiae, saepe voluptatibus qui a? Fuga quo ipsa cumque quas a mollitia error vero.</p>
        </div>
        <div key="why" v-else-if="argument === 'why'">
            <h3>Why <span>baldorya</span> is a great idea?</h3>
            <p>Lorem ipsum dolor, sit amet consectetur adipisicing elit. Sequi eius maxime totam quam, dolorem eos reprehenderit necessitatibus in nisi nemo nesciunt autem, ut alias voluptate architecto, quaerat corporis praesentium perspiciatis.</p>
        </div>
        <div key="who" v-else-if="argument === 'who'">
            <h3>Who is <span>baldorya</span>?</h3>
            <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Veniam, vel laboriosam autem alias accusantium explicabo, saepe eaque aspernatur aliquam reiciendis quo itaque at soluta delectus voluptatibus iste, sapiente consequatur maiores.</p>
        </div>
      <social-component />
  </div>
</template>

<script>
import SocialComponent from './SocialComponent.vue'
import ControllerComponent from './ControllerComponent.vue'

export default {
    name: 'MainComponent',
    components: {
        SocialComponent,
        ControllerComponent
    },
    data() {
        return {
            argument: 'what'
        }
    },
    methods: {
        onChangeArg: function(arg) {
            this.argument = arg
        }
    }
}
</script>

<style scoped>
    .main {
        padding-top: 60px;
        max-width: 400px;
        margin: 0 auto;
    }
    .title {
        margin: 0 auto;
        display: flex;
        align-items: center;
        justify-content: space-evenly;
        cursor: pointer;
    }
    h1 {
        font-family: 'Anton', sans-serif !important;
        letter-spacing: 1.5px;
        font-size: 3rem;
    }
    p {
        text-align: left;
    }
    img {
        width: 100px;
    }
</style>