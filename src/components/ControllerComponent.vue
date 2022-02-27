<template>
  <div>
      <button :class="{ active: this.argument === 'what' }" @click="$emit('changeArg', 'what')">What</button>
      <button :class="{ active: this.argument === 'why' }" @click="$emit('changeArg', 'why')">Why</button>
      <button :class="{ active: this.argument === 'who' }" @click="$emit('changeArg', 'who')">Who</button>
  </div>
</template>

<script>
export default {
    name: 'ControllerComponent',
    data() {
        return {
        }
    },
    props: {
      argument: {
        type: String,
        required: true
      }
    }
}
</script>

<style scoped>
    div {
        display: flex;
        align-items: center;
        justify-content: center;
    }
    button {
        background-color: transparent;
        color: white;
        border: none;
        cursor: pointer;
        border-bottom: 2px solid transparent;
        transition: all 0.2s;
        font-family: 'Titillium Web', sans-serif;
    }
    button:hover {
        color: #41B883;
        transition: all 0.2s;
    }
    .active {
        border-bottom: #41B883 solid 2px;
    }
</style>