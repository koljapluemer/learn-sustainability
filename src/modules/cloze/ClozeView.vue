<template>
    <igu-cloze-reveal :num-clozes="1" @cloze-event="handleClozeEvent">
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
    const html = text.replace(/==([^=]+)==/g, (_, content) => {
        gaps.push(content)
        return `<span data-is-gap>${content}</span>`
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

.cloze-revealed {
    border-radius: 3px;
    font-weight: bold;
    border-bottom: 2px solid #1976d2;
}
</style>