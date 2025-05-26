<template>
    <igu-cloze-reveal num-clozes="3">
        <div slot="pre-cloze">Complete the famous quote:</div>
        <span slot="cloze">
            Whom the <span data-is-gap>gods</span> wish to <span data-is-gap>destroy</span>, they <span
                data-is-gap>give</span> <span data-is-gap>unlimited</span> <span data-is-gap>resources</span>
        </span>
        <div slot="post-cloze">Think about the meaning of this quote.</div>
    </igu-cloze-reveal>
</template>

<style scoped>
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