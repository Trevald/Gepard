<template>
    <div class="category">
        <button v-if="mode === 'edit'" type="button" class="button delete small" @click="deleteCategory()">
            Ta bort
        </button>
        <input v-if="mode === 'edit'" type="text" v-model="title" @input="changeTitle($event)" class="title" />
        <h2 v-else class="title">{{ category.name }}</h2>
        <ul>
            <li v-for="(question, index) in questions">
                <QuestionEdit v-if="mode === 'edit'" :question="question" @change="changeQuestion($event, index)" />
                <Question v-else :question="question" :index="index" />
            </li>
        </ul>
    </div>
</template>

<script>
import Question from "./Question.vue"
import QuestionEdit from "./QuestionEdit.vue"

export default {
    components: {
        Question,
        QuestionEdit
    },

    emits: ["changeTitle", "changeQuestion", "deleteCategory"],

    props: {
        category: Object,

        mode: {
            type: String,
            default: "view"
        }
    },

    computed: {
        title: {
            get() {
                return this.category.name
            },
            set(value) {
                this.$emit("changeTitle", value)
            }
        },
        questions() {
            return this.category.questions
        }
    },

    methods: {
        changeTitle(event) {
            console.log("input", event)
            this.$emit("changeTitle", event.target.value)
        },

        changeQuestion(data, index) {
            this.$emit("changeQuestion", {
                index: index,
                question: data.question,
                answer: data.answer
            })
        },

        deleteCategory() {
            this.$emit("deleteCategory")
        }
    }
}
</script>

<style scoped>
.category {
    position: relative;
}

.button.delete {
    position: absolute;
    top: 0.5rem;
    right: 0.5rem;
}
</style>
