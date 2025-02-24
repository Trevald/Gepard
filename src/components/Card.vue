<template>
    <div class="card">
        <div class="points">
            <h2>{{ data.points }}</h2>
        </div>
        <div class="question">
            <p>{{ data.question }}</p>
        </div>
        <div class="answer">
            <p>{{ data.answer }}</p>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        data: Object
    },

    data() {
        return {
            activeSide: "points"
        }
    }
}
</script>

<style scoped>
.card {
    flex: 1 1 auto;
    display: flex;
    justify-content: stretch;
    align-items: stretch;
    perspective: 1000px;
    transform-style: preserve-3d;
}

.points,
.question,
.answer {
    position: absolute;
    inset: 0;
    display: grid;
    place-content: center;
    backface-visibility: hidden;
    z-index: 15;
    background-color: var(--color-blue-600);
}

.question,
.answer {
    z-index: 20;
    background-color: var(--color-blue-600);
    rotate: y 180deg;
}
</style>
