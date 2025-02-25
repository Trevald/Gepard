<template>
    <div :class="`app mode-${mode}`">
        <div class="menu" v-if="showMenu || mode === 'edit'">
            <div class="group">
                <button type="button" class="button" @click="createNew($event)">Ny</button>
                <input type="file" id="fileinput" @change="fileChange($event)" />
                <label class="button" for="fileinput">Öppna...</label>
                <template v-if="document">
                    <button v-if="mode !== 'edit'" type="button" class="button" @click="setMode('edit')">Ändra</button>
                    <button v-if="mode === 'edit'" type="button" class="button primary" @click="downloadFile($event)">
                        Spara
                    </button>
                </template>
            </div>
            <div class="group">
                <button
                    v-if="this.document && mode === 'edit'"
                    type="button"
                    class="button secondary"
                    @click="newCategory()"
                >
                    Ny kategori
                </button>
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
        showMenu() {
            return this._showMenu || !this.document
        }
    },

    methods: {
        changeCategoryTitle(value, index) {
            this.document.categories[index].name = value
        },

        changeQuestion(value, index) {
            this.document.categories[index].questions[value.index].question = value.question
            this.document.categories[index].questions[value.index].answer = value.answer
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
            window.localStorage.setItem("document", JSON.stringify(this.document))
        },

        downloadFile() {
            const textToSave = stringify(this.document, {
                merge: false,
                uniqueKeys: false,
                stringKeys: true
            })

            const hiddenElement = document.createElement("a")
            hiddenElement.href = "data:attachment/text," + encodeURI(textToSave)
            hiddenElement.target = "_blank"
            hiddenElement.download = "untitled.yaml"
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
        },

        async loadFirst() {
            const response = await fetch("/data/1.yaml")
            const data = await response.text()
            const yaml = parse(data)

            for (const category of yaml.categories) {
                for (let i = 0; i < category.questions.length; i++) {
                    category.questions[i].points = (i + 1) * 100 * this.level
                }
            }
            this.categories = yaml.categories
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
