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

const originalText = ref('')
const modifiedText = ref('')
const highlightedOriginal = ref('')
const highlightedModified = ref('')

const compareInput = () => {
  const originalLines = originalText.value.split('\n')
  const modifiedLines = modifiedText.value.split('\n')
  let oResult = []
  let mResult = []

  const maxLength = Math.max(originalLines.length, modifiedLines.length)

  for (let i = 0; i < maxLength; i++) {
    const oLine = originalLines[i] || ''
    const mLine = modifiedLines[i] || ''

    if (oLine === mLine) {
      oResult.push(`<div>${escapeHtml(oLine)}</div>`)
      mResult.push(`<div>${escapeHtml(mLine)}</div>`)
    } else {
      const { oHighlighted, mHighlighted } = highlightCharacterDifferences(oLine, mLine)
      oResult.push(oHighlighted)
      mResult.push(mHighlighted)
    }
  }

  highlightedOriginal.value = oResult.join('')
  highlightedModified.value = mResult.join('')
}

const highlightCharacterDifferences = (oLine, mLine) => {
  let oHighlighted = '<div class="px-8 removed">'
  let mHighlighted = '<div class="px-8 added">'
  const maxLength = Math.max(oLine.length, mLine.length)

  for (let i = 0; i < maxLength; i++) {
    const oChar = oLine[i] || ' '
    const mChar = mLine[i] || ' '
    if (oChar === mChar) {
      oHighlighted += escapeHtml(oChar)
      mHighlighted += escapeHtml(mChar)
    } else {
      oHighlighted += `<span class="removed-diff">${escapeHtml(oChar)}</span>`
      mHighlighted += `<span class="added-diff">${escapeHtml(mChar)}</span>`
    }
  }

  oHighlighted += '</div>'
  mHighlighted += '</div>'
  return { oHighlighted, mHighlighted }
}

const escapeHtml = (specialChar) => {
  const escapeChars = {
    '&': '&amp;',
    '<': '&lt;',
    '>': '&gt;',
    '"': '&quot;',
    "'": '&#039;'
  }

  return specialChar.replace(/[&<>"']/g, (char) => escapeChars[char])
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
  resize: none;
  padding: 8px;
  min-height: 250px;
  border-radius: 8px;
}

.btn-container {
  gap: 16px;
  margin: 16px;
  display: flex;
  align-items: center;
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

.added-diff {
  background: var(--diff-add);
}

.removed-diff {
  background: var(--diff-remove);
}
</style>
