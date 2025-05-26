<template>
    <igu-cloze-reveal :num-clozes="1" @cloze-event="handleClozeEvent" :key="currentClozeHtml">
        <span slot="cloze" v-html="currentClozeHtml"></span>
    </igu-cloze-reveal>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import clozeData from './cloze.json'

// Flatten the nested array of clozes
const allClozes = clozeData.cloze.flat()
const usedClozeIndices = new Set<number>()

const currentClozeHtml = ref('')
const currentClozeGaps = ref<string[]>([])

function transformClozeText(text: string): { html: string, gaps: string[] } {
    const gaps: string[] = []
    // Using a more precise regex to handle multi-word expressions
    const html = text.replace(/==([^=]+)==/g, (match, content) => {
        // Trim to handle any accidental whitespace in the gaps
        const trimmedContent = content.trim()
        gaps.push(trimmedContent)
        return `<span data-is-gap>${trimmedContent}</span>`
    })
    return { html, gaps }
}

function loadRandomCloze() {
    // If we've used all clozes, reset the used indices
    if (usedClozeIndices.size >= allClozes.length) {
        usedClozeIndices.clear()
    }

    // Find an unused index
    let randomIndex: number
    do {
        randomIndex = Math.floor(Math.random() * allClozes.length)
    } while (usedClozeIndices.has(randomIndex))

    usedClozeIndices.add(randomIndex)
    const { html, gaps } = transformClozeText(allClozes[randomIndex])
    currentClozeHtml.value = html
    currentClozeGaps.value = gaps
}

function handleClozeEvent(event: any) {
    console.log(event)
    if (event.detail.type === 'rating') {
        console.log('is rating event')
        loadRandomCloze()
    }
}

onMounted(() => {
    loadRandomCloze()
})
</script>

<style>
.cloze-hidden {
    filter: blur(8px);
    min-width: 2em;
    user-select: none;
}

/* light yellow background */
.cloze-revealed {
    background-color: #fcf8e3;
}
</style>