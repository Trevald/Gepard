<template>
    <div :class="`app mode-${mode}`">
        <div class="menu" v-if="showMenu || mode === 'edit'">
            <div class="group">
                <button type="button" class="button" @click="createNew($event)">Ny</button>
                <input type="file" id="fileinput" @change="fileChange($event)" />
                <label class="button" for="fileinput">Öppna...</label>
                <button v-if="!gameHasStarted" type="button" class="button" @click="setMode('view')">Spela</button>
                <button v-if="gameHasStarted" type="button" class="button" @click="restart()">Börja om</button>

                <template v-if="document">
                    <button v-if="mode !== 'edit'" type="button" class="button" @click="setMode('edit')">Ändra</button>
                    <button v-if="mode === 'edit'" type="button" class="button primary" @click="downloadFile($event)">
                        Spara
                    </button>
                </template>
            </div>
            <div class="group" v-if="this.document && mode === 'edit'">
                <input
                    type="text"
                    class="block w-48 rounded-sm bg-slate-800 px-3 py-2 text-base text-gray-50 outline-1 -outline-offset-1 outline-slate-700 placeholder:text-gray-400 focus:outline-2 focus:-outline-offset-2 focus:outline-indigo-600 sm:text-sm/6"
                    style="padding: 4px 8px"
                    v-model="document.title"
                    @input="changeTitle($event)"
                    placegholder="Namn"
                />
                <button type="button" class="button secondary" @click="newCategory()">Ny kategori</button>
            </div>
        </div>
        <div v-if="document" :class="classList">
            <ul class="categories">
                <li v-for="(category, index) in document.categories" :key="index">
                    <Category
                        :category="category"
                        :mode="mode"
                        @change-title="changeCategoryTitle($event, index)"
                        @change-question="changeQuestion($event, index)"
                        @delete-category="deleteCategory($event, index)"
                        @question-answered="questionAnswered($event, index)"
                    />
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import { parse, stringify } from "yaml"
import { toRaw } from "vue"

import Category from "./components/Category.vue"

export default {
    components: {
        Category
    },

    data() {
        return {
            categories: [],
            document: undefined,
            level: 1,
            _showMenu: false,
            mode: "view"
        }
    },

    computed: {
        classList() {
            return `mode-${this.mode}`
        },

        gameHasStarted() {
            if (!this.document) {
                return false
            }

            for (const category of this.document.categories) {
                for (const question of category.questions) {
                    if (question.answered === true) {
                        return true
                    }
                }
            }

            return false
        },

        showMenu() {
            return this._showMenu || !this.document
        }
    },

    methods: {
        changeTitle(event) {
            this.document.title = event.target.value
            this.cacheCurrentFile()
        },

        changeCategoryTitle(value, index) {
            this.document.categories[index].name = value
            this.cacheCurrentFile()
        },

        changeQuestion(value, index) {
            this.document.categories[index].questions[value.index].question = value.question
            this.document.categories[index].questions[value.index].answer = value.answer
            this.cacheCurrentFile()
        },

        deleteCategory(index) {
            this.document.categories.splice(index, 1)
            this.cacheCurrentFile()
        },

        questionAnswered(value, index) {
            this.document.categories[index].questions[value].answered = true
            this.cacheCurrentFile()
        },

        restart() {
            for (let i = 0; i < this.document.categories.length; i++) {
                for (let j = 0; j < this.document.categories[i].questions.length; j++) {
                    this.document.categories[i].questions[j].answered = undefined
                }
            }

            this._showMenu = false
            this.cacheCurrentFile()
            window.location.reload()
        },

        fileChange(event) {
            const supportedTypes = ["application/x-yaml", "text/yaml"]
            const file = event.target.files[0]

            if (!supportedTypes.includes(file.type)) {
                alert("Invalid file type")
                return
            }

            const reader = new FileReader()
            reader.onload = () => {
                this.loadFile(reader.result)
            }
            reader.onerror = () => {
                alert("Error reading file")
            }
            reader.readAsText(file)
        },

        loadFile(data) {
            const yaml = parse(data)
            for (const category of yaml.categories) {
                for (let i = 0; i < category.questions.length; i++) {
                    category.questions[i].points = (i + 1) * 100 * this.level
                }
            }
            this.document = yaml
            this.cacheCurrentFile()
        },

        cacheCurrentFile() {
            window.localStorage.setItem("document", JSON.stringify(this.document))
        },

        downloadFile() {
            this.restart()
            const textToSave = stringify(this.document, {
                merge: false,
                uniqueKeys: false,
                stringKeys: true
            })

            const hiddenElement = document.createElement("a")
            hiddenElement.href = "data:attachment/text," + encodeURI(textToSave)
            hiddenElement.target = "_blank"
            hiddenElement.download = this.document.title ? this.document.title + ".yaml" : "untitled.yaml"
            hiddenElement.click()
            this._showMenu = false
        },

        setMode(mode) {
            this.mode = mode
            this._showMenu = false
        },

        createNew() {
            this.document = {
                categories: []
            }

            this.newCategory()
            this.mode = "edit"
        },

        newCategory() {
            const name = "Ny kategori " + parseInt((this.document?.categories.length || 0) + 1)
            const questions = []
            for (let i = 1; i < 6; i++) {
                questions.push({
                    question: `Ny fråga ${i}`,
                    answer: "Svar"
                })
            }

            this.document.categories.push({
                name,
                questions
            })

            this.cacheCurrentFile()
        }
    },

    mounted() {
        window.document.addEventListener("keydown", (event) => {
            if (!this.document) {
                return
            }

            if (event.code === "Space" || event.code === "Escape") {
                this._showMenu = !this._showMenu
            }
        })

        const stored = window.localStorage.getItem("document")
        if (stored) {
            this.document = JSON.parse(stored)
        }
    }
}
</script>

<style scoped>
input[type="file"] {
    display: none;
}
</style>
