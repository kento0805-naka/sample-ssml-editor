<template>
  <div class="editor-container">
    <div class="editor-toolbar">
      <button class="toolbar-button" type="button">hoge</button>
      <div class="toolbar-separator"></div>
      <button class="toolbar-button" type="button">&lt;</button>
      <button class="toolbar-button" type="button">&gt;</button>
      <div class="preview-toggle">
        <button class="toolbar-button" type="button">SSML表示</button>
      </div>
    </div>
    <div class="editor-wrapper">
      <div class="editor-content-wrapper">
        <EditorContent :editor="editor" class="editor-content" />
      </div>
      <div class="preview-wrapper">
        <div class="preview-component">
          <SsmlPreview :content="content" />
        </div>
        <div class="preview-component">
          <HtmlPreview :content="content" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from 'vue'
import { Editor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'

import HtmlPreview from '../HtmlPreview/HtmlPreview.vue'
import SsmlPreview from '../SsmlPreview/SsmlPreview.vue'

const content = ref('') // 入力値そのまま

const editor = new Editor({
  content: 'hoge',
  extensions: [StarterKit],
})

const parseToSsml = (htmlContent: string) => {
  let ssml = htmlContent
    .replace(/<p>(.*?)<\/p>/g, '<s>$1</s>')
    .replace(/<strong>(.*?)<\/strong>/g, '<emphasis level="strong">$1</emphasis>')
    .replace(/<em>(.*?)<\/em>/g, '<emphasis level="moderate">$1</emphasis>')
    .replace(/<br\s*\/?>/g, '<break time="500ms"/>');


  ssml = `<speak>${ssml}</speak>`;
  return ssml;
}

watchEffect(() => {
  const newContent = editor.isEmpty ? '' : editor.getHTML()
  content.value = parseToSsml(newContent)
})

</script>

<style scoped lang="scss">
.editor-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 800px; 
  margin: 0 auto;
}

.editor-toolbar {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  background-color: #f0f0f0;
  padding: 5px;
  border: 1px solid #000;
  border-radius: 5px;
  width: 800px;
}

.toolbar-separator {
  width: 1px;
  height: 24px;
  margin: 0 8px;
  background-color: #000;
}

.toolbar-button {
  padding: 4px 8px;
  margin: 2px;
  font-size: 12px;
  background: none;
  border: 1px solid #000;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s, color 0.2s;

  &:hover:not(:disabled) {
    background-color: #ddd;
  }

  &:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  &.is-active {
    color: #3490dc;
  }

  &.is-disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
}

.editor-wrapper {
  display: flex;
  justify-content: center;
  width: 100%;
}

.editor-content-wrapper {
  flex: 2;
  display: flex;
  flex-direction: column;
  min-height: 400px;
  background-color: white;
  padding: 8px;
  border: 1px solid #000;
  border-radius: 5px;
  margin-right: 20px;
  width: 400px;
  max-width: 400px;
}

.editor-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-height: 0;

  .ProseMirror {
    letter-spacing: 0.5px;
    flex: 1;
    overflow-y: auto;
    padding: 8px;
    font-size: 14px;
    color: #333;

    &:focus {
      outline: none;
    }
  }
}

.preview-wrapper {
  display: flex;
  flex-direction: column;
  flex: 1;
}

.preview-component {
  background-color: #f0f0f0;
  border: 1px solid #000;
  padding: 8px;
  margin-bottom: 10px;
  border-radius: 5px;
  font-size: 12px;
  min-height: 200px;
  width: 400px;
  max-width: 400px;
}
</style>
