<template>
    <div class="category">
        <h2>{{ category.name }}</h2>
        <ul>
            <li v-for="question in questions">
                <Question :question="question" />
            </li>
        </ul>
    </div>
</template>

<script>
import Question from "./Question.vue"

export default {
    components: {
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
