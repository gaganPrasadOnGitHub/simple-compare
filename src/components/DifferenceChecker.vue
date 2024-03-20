<template>
  <div class="compare-container">
    <div class="labels">
      <small class="flex-1">Original text</small>
      <small class="flex-1">Modified text</small>
    </div>
    <div class="input-wrapper">
      <textarea v-model="originalText" placeholder="Paste the original text here."></textarea>
      <textarea
        v-model="modifiedText"
        placeholder="Paste the modified text here to see differences."
      ></textarea>
    </div>
    <div class="btn-container">
      <button
        class="btn btn-compare"
        @click="compareInput"
        :disabled="!originalText || !modifiedText"
      >
        Compare
      </button>
      <button v-if="originalText || modifiedText" class="btn btn-clear" @click="clearTexts">
        Clear
      </button>
    </div>
    <div v-if="highlightedOriginal || highlightedModified" class="result-container">
      <div class="result original-results" v-html="highlightedOriginal"></div>
      <div class="result modified-results" v-html="highlightedModified"></div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import DiffMatchPatch from 'diff-match-patch'

const originalText = ref('')
const modifiedText = ref('')
const highlightedOriginal = ref('')
const highlightedModified = ref('')

const dmp = new DiffMatchPatch()

const compareInput = () => {
  const diffs = dmp.diff_main(originalText.value, modifiedText.value)
  dmp.diff_cleanupSemantic(diffs)

  let htmlOriginal = ''
  let htmlModified = ''

  diffs.forEach(([change, text]) => {
    const escapedText = escapeHtml(text)
    if (change === 0) {
      htmlOriginal += escapedText
      htmlModified += escapedText
    } else if (change === -1) {
      htmlOriginal += `<span class="removed">${escapedText}</span>`
    } else if (change === 1) {
      htmlModified += `<span class="added">${escapedText}</span>`
    }
  })

  highlightedOriginal.value = htmlOriginal
  highlightedModified.value = htmlModified
}

const escapeHtml = (text) => {
  const escapeChars = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#039;',
    '\n': '<br>'
  }
  return text.replace(/[\n&<>"']/g, (char) => escapeChars[char])
}

const clearTexts = () => {
  originalText.value = ''
  modifiedText.value = ''
  highlightedOriginal.value = ''
  highlightedModified.value = ''
}
</script>

<style>
.compare-container {
  display: flex;
  min-height: 80dvh;
  align-items: center;
  flex-direction: column;
  justify-content: flex-start;
}

.labels {
  gap: 16px;
  width: 100%;
  display: flex;
  margin-bottom: 16px;
}

.input-wrapper,
.result-container {
  gap: 16px;
  width: 100%;
  display: flex;
  align-items: baseline;
  justify-content: flex-start;
}

.result-container {
  border-radius: 8px;
  padding-top: 16px 0;
  background: var(--bg-card);
  border: 1px solid var(--main-color);
  justify-content: center;
}

.input-wrapper textarea {
  flex: 1;
  resize: none;
  padding: 8px;
  border: none;
  height: 250px;
  border-radius: 8px;
  color: var(--text-color);
  background: var(--bg-card);
  outline: 1px solid var(--light-gray);
}

.input-wrapper textarea:focus {
  outline: 1px solid var(--main-color);
}

.result {
  flex: 1;
  padding: 8px;
  overflow: auto;
  min-height: 250px;
  border-radius: 8px;
  white-space: pre-wrap;
  word-wrap: break-word;
}

.btn-container {
  top: 0px;
  gap: 16px;
  margin: 16px;
  padding: 16px;
  display: flex;
  position: sticky;
  align-items: center;
  border-radius: 0 0 16px 16px;
  background: var(--background-color);
}

.btn-compare {
  color: var(--white);
  background: var(--main-color);
}

.btn-clear:hover {
  background: var(--light-gray);
}

.btn-compare:hover {
  background: rebeccapurple;
}

.added {
  background: var(--highlight-add);
}

.removed {
  background: var(--highlight-remove);
}
</style>
