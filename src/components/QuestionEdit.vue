<template>
    <div class="question-edit">
        <div class="flex flex-col gap-1">
            <label for="question" class="block text-left text-sm/6 font-medium text-gray-200">Fråga</label>
            <div>
                <input
                    type="question"
                    name="question"
                    id="question"
                    class="block w-full rounded-sm bg-slate-800 px-3 py-2 text-base text-gray-50 outline-1 -outline-offset-1 outline-slate-700 placeholder:text-gray-400 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 sm:text-sm/6"
                    placeholder="Skriv en fråga som egentligen är ett svar"
                    v-model="_question"
                />
            </div>
        </div>
        <div>
            <label for="answer" class="block text-left text-sm/6 font-medium text-gray-200">Svar</label>
            <div>
                <input
                    type="answer"
                    name="answer"
                    id="answer"
                    class="block w-full rounded-sm bg-slate-800 px-3 py-1.5 text-base text-gray-50 outline-1 -outline-offset-1 outline-slate-700 placeholder:text-gray-400 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 sm:text-sm/6"
                    placeholder="Formulera svaret som en fråga"
                    v-model="_answer"
                />
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        question: {
            type: Object,
            required: true
        }
    },
    emits: ["change"],

    computed: {
        _question: {
            get() {
                return this.question.question
            },
            set(value) {
                this.$emit("change", {
                    question: value,
                    answer: this._answer
                })
            }
        },
        _answer: {
            get() {
                return this.question.answer
            },
            set(value) {
                this.$emit("change", {
                    question: this._question,
                    answer: value
                })
            }
        }
    },

    methods: {
        updateQuestion(event) {
            this.$emit("change", {
                question: event.target.value,
                answer: this._answer
            })
        },
        updateAnswer(event) {
            this.$emit("change", {
                question: this._question,
                answer: event.target.value
            })
        }
    }
}
</script>

<style scoped>
.question-edit {
    padding: 1rem 1.5rem;
    display: flex;
    flex-direction: column;
    gap: 1rem;
    width: 100%;
}

.question-edit input {
    padding: 4px 8px;
}
</style>
