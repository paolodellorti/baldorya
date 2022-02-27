<template>
  <div class="main">
      <div class="title">
        <img alt="Vue logo" src="../assets/logo.svg">
        <h1>baldor<span>y</span>a</h1>
      </div>
      <div class="content">
          <h3>What <span>baldorya</span> is?</h3>
          <p>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Veniam consequuntur, dolores quod asperiores velit sapiente dolor molestiae, saepe voluptatibus qui a? Fuga quo ipsa cumque quas a mollitia error vero.</p>
          <h3>Why <span>baldorya</span> is a great idea?</h3>
          <p>Lorem ipsum dolor, sit amet consectetur adipisicing elit. Sequi eius maxime totam quam, dolorem eos reprehenderit necessitatibus in nisi nemo nesciunt autem, ut alias voluptate architecto, quaerat corporis praesentium perspiciatis.</p>
      </div>
  </div>
</template>

<script>
export default {
    name: 'MainComponent',
}
</script>

<style scoped>
    .main {
        padding-top: 60px;
        height: 100vh;
        max-width: 400px;
        margin: 0 auto;
    }
    .title {
        margin: 0 auto;
        display: flex;
        align-items: center;
        justify-content: space-evenly;
    }
    h1 {
        font-family: 'Anton', sans-serif;
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