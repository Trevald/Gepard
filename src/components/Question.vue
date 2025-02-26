<template>
    <div class="container" ref="container">
        <div class="card-wrapper" :class="classList" :style="wrapperStyleList" ref="wrapper" v-if="!answered">
            <div class="card" :style="cardStyleList" ref="card" @click="handleClick()">
                <p class="points">{{ points }}</p>
                <p class="question">
                    {{ question.question }}
                    <span class="meta">{{ points }}</span>
                </p>
                <p class="answer">
                    {{ question.answer }}
                    <span class="meta">{{ points }}</span>
                </p>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        question: Object,
        index: Number
    },

    emits: ["answered"],

    data() {
        return {
            scaleUp: 1,
            scaleDown: 1,
            show: false,
            cardStyleList: {},
            wrapperStyleList: {},
            state: "show-points"
        }
    },

    watch: {
        question() {
            console.log("question")
        }
    },

    computed: {
        answered() {
            return this.question.answered === true
        },

        classList() {
            return this.state
        },
        points() {
            return 100 * (this.index + 1)
        }
    },

    methods: {
        handleClick() {
            switch (this.state) {
                case "show-points":
                    this.selectQuestion()
                    break
                case "show-question":
                    this.showAnswer()
                    break
                case "show-answer":
                    this.close()
                    break
            }
        },

        showAnswer() {
            this.state = "show-answer"
        },

        close() {
            this.state = "close"

            setTimeout(() => {
                this.$emit("answered")
            }, 500)
        },

        selectQuestion() {
            this.state = "select-question"
            this.wrapperStyleList = {
                transform: `scale(${this.scaleDown})`
            }
            setTimeout(() => {
                this.showQuestion()
            }, 250)
        },

        showQuestion() {
            this.state = "show-question"
            this.wrapperStyleList = {
                transform: "scale(1)"
            }
        }
    },

    mounted() {
        if (!this.$refs.wrapper) {
            return
        }
        const container = this.$refs.container.getBoundingClientRect()
        const wrapper = this.$refs.wrapper.getBoundingClientRect()
        const card = this.$refs.card.getBoundingClientRect()

        this.scaleDown = container.height / wrapper.height
        this.scaleUp = wrapper.height / container.height

        const width = container.width * this.scaleUp
        const top = container.top - wrapper.height / 2 + container.height / 2
        const left = container.left - width / 2 + container.width / 2

        this.wrapperStyleList = {
            top: `${top}px`,
            left: `${left}px`,
            width: `${width}px`,
            transform: `scale(${this.scaleDown})`
        }
    }
}
</script>

<style scoped>
.container {
    flex: 1 1 auto;
    display: flex;
    justify-content: stretch;
    align-items: stretch;
    padding: 1vw;
    max-width: unset;
}

.card-wrapper {
    position: fixed;
    z-index: 10;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;

    display: flex;
    justify-content: stretch;
    align-items: stretch;
    transform-origin: center;
}

.card-wrapper.select-question {
    z-index: 30;
    transition: all 0.25s;
}

.card-wrapper.show-answer,
.card-wrapper.show-question {
    z-index: 30;
    transition: all 0.25s;
    background-color: var(--color-surface-100);
}

.card-wrapper.close {
    z-index: 30;
    transition: all 500ms;
}

.card {
    position: absolute;
    inset: 4vw;

    display: flex;
    justify-content: stretch;
    align-items: stretch;

    box-shadow: 0 1vw 2vw rgba(0, 0, 0, 0.05);
    transform-style: preserve-3d;
    transition: rotate 750ms ease 350ms;
}

.card-wrapper.show-question .card {
    rotate: y 180deg;
}

.card-wrapper.show-answer .card {
    rotate: y 360deg;
    transition: rotate 750ms ease;
}

.card-wrapper.close .card {
    transform: translateY(-125vh);
    transition: transform 500ms;
}

.card > * {
    position: absolute;
    inset: 0;
    flex: 1 1 auto;
    display: flex;
    justify-content: center;
    align-items: center;

    padding: 8vw 8vw 8vw 8vw;
    border-radius: 2vw;
    backface-visibility: hidden;

    background-color: var(--color-surface-500);
    background-image: linear-gradient(-10deg, rgba(0, 0, 0, 0.4) 0%, rgba(0, 0, 0, 0) 100%);
}

.points {
    position: relative;

    font-size: 24vw;
    font-weight: 800;
}

.answer,
.question {
    display: grid;
    font-size: 5vw;
    place-content: center;
    padding-bottom: 8vw;

    background-color: var(--color-surface-500);
    background-image: linear-gradient(-10deg, rgba(0, 0, 0, 0.4) 0%, rgba(0, 0, 0, 0) 100%);
}

.question {
    rotate: y 180deg;
    background-color: var(--color-surface-400);
}

.card > *::after {
    content: "";
    position: absolute;
    inset: 3vw;
    border: 0.4vw solid rgba(255, 255, 255, 0.4);
    border-radius: 1vw;
}

.answer {
    opacity: 0;
    background-color: #17961b;
    transition: none;
}

.card-wrapper.close .points,
.card-wrapper.show-answer .points {
    display: none;
}

.card-wrapper.close .answer,
.card-wrapper.show-answer .answer {
    opacity: 1;
}

.card .meta {
    position: absolute;
    bottom: 4vw;
    right: 0;
    left: 0;

    font-size: 2vw;
}
</style>
