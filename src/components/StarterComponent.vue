<template>
    <div id="starter">
        <img alt="Vue logo" src="../assets/logo.svg">
        <div>
            <h1>baldor<span>y</span>a</h1>
            <h2>Share <span>y</span>our part<span>y</span>.</h2>
        </div>
        <slide-unlock
            class="slideunlock"
            ref="vueslideunlock"
            :auto-width="false"
            :width="300"
            :height="60"
            :noanimate="true"
            text="get started"
            success-text="let's start"
            @completed="complete()"
        />
    </div>
</template>

<script>
import SlideUnlock from "vue-slide-unlock"

export default {
    name: 'StarterComponent',
    components: {
        SlideUnlock
    },
    methods: {
        complete: function() {
            setTimeout(() => this.$emit("completed"), 1000)
        }
    }
}
</script>

<style scoped>
    h1 {
        font-family: 'Anton', sans-serif;
        letter-spacing: 1.5px;
        font-size: 3.5rem;
        margin-bottom: 0px;
    }
    h2 {
        margin-top: 0px;
        font-weight: 300;
        margin-bottom: 50px;
    }
    img {
        width: 150px;
    }
    #starter {
        height: 100vh;
        max-width: 400px;
        margin: 0 auto;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: space-evenly;
    }
</style>