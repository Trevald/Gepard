<template>
    <ul class="categories">
        <li v-for="category in categories">
            <Category :category="category" />
        </li>
    </ul>
</template>

<script>
import { parse } from "yaml"

import Category from "./components/Category.vue"

export default {
    components: {
        Category
    },

    data() {
        return {
            categories: [],
            level: 1
        }
    },

    methods: {
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
        this.loadFirst()
    }
}
</script>
