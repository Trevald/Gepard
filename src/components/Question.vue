<template>
    <div class="question" @click="handleClick()">
        <div class="card" :class="classList" :style="styleList" ref="card" v-if="!question.answered">
            <h2 class="points front">{{ question.points }}</h2>
            <p class="question back">{{ question.question }}</p>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        question: Object
    },

    data() {
        return {
            show: false,
            styleList: {}
        }
    },

    computed: {
        classList() {
            return {
                show: this.show
            }
        }
    },

    methods: {
        handleClick() {
            if (this.question.answered) {
                return
            }

            if (this.show) {
                this.question.answered = true
                return
            }

            const card = this.$refs.card
            const rect = card.getBoundingClientRect()

            this.styleList = {
                position: "fixed",
                zIndex: 20,
                top: rect.top + "px",
                left: rect.left + "px",
                // bottom: rect.bottom + "px",
                // right: rect.right + "px",
                width: rect.width + "px",
                height: rect.height + "px"
            }

            setTimeout(() => {
                this.styleList = {
                    position: "fixed",
                    zIndex: 20,
                    top: 0 + "px",
                    left: 0 + "px",
                    // bottom: 0 + "px",
                    // right: 0 + "px",
                    width: document.body.clientWidth + "px",
                    height: document.body.clientHeight + "px",
                    transition: "all 250ms linear",
                    transform: "scale(2)"
                    // transform: "rotateY(180deg)"
                }
            }, 1)

            this.show = true
        }
    }
}
</script>

<style scoped>
.question {
    flex: 1 1 auto;
    position: relative;
    display: flex;
    justify-content: stretch;
    align-items: stretch;
}

.card {
    flex: 1 1 auto;
    display: flex;
    justify-content: stretch;
    align-items: stretch;
    cursor: pointer;
}
/*

.card {
    perspective: 1000px;
    transform-style: preserve-3d;}

.card.show {
    inset: 0 !important;
    width: 100% !important;
    height: 100% !important;
    z-index: 10;
    transform: rotateY(180deg);
    transition: all 0.5s;
}
*/

.front,
.back {
    flex: 1 1 auto;
    display: grid;
    place-content: center;
    z-index: 15;
    background-color: var(--color-blue-600);
}

.card .back,
.card.show .front {
    display: none;
}

.card .back {
    font-size: 3vw;
}

.card.show .back {
    display: block;
}

/*
.front,
.back {
    position: absolute;
    inset: 0;
    display: grid;
    place-content: center;
    backface-visibility: hidden;
    z-index: 15;
    background-color: var(--color-blue-600);
}

.back {
    z-index: 20;
    background-color: var(--color-blue-600);
    rotate: y 180deg;
}
    */
</style>
