<template>
    <div class="category">
        <h2>{{ category.name }}</h2>
        <ul>
            <li v-for="question in questions">
                <div class="question">
                    <Card :data="question" />
                </div>
            </li>
        </ul>
    </div>
</template>

<script>
import Card from "./Card.vue"
import Question from "./Question.vue"

export default {
    components: {
        Card,
        Question
    },

    props: {
        category: Object
    },

    computed: {
        questions() {
            return this.category.questions
        }
    }
}
</script>
