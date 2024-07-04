<template>
  <div>
    <span v-html="htmlContent"></span>
  </div>
</template>

<script setup lang="ts">
import { watchEffect } from 'vue'
const props = defineProps<{
  content: string
}>()

let htmlContent = "";

// https://developer.mozilla.org/ja/docs/Web/API/Document_Object_Model/Using_the_Document_Object_Model

const parseToHtml = (ssmlContent: string) => {
  let html = ssmlContent
    .replace(/&gt;/g, '>')
    .replace(/&lt;/g, '<')
    .replace(/<speak>(.*?)<\/speak>/g, "$1")
    .replace(/<s>(.*?)<\/s>/g, '$1')
    .replace(/<emphasis level="strong">(.*?)<\/emphasis>/g, '<strong>$1</strong>')
    .replace(/<emphasis level="moderate">(.*?)<\/emphasis>/g, '<em>$1</em>')
    .replace(/<break time="500ms"\/>/g, '<br>');

  const domTree : Document = new DOMParser().parseFromString(html, "text/html");

  const result = checkTree(domTree);

  return result;
}

// treeの中身を確認して<strong>タグの中に他のタグが入っていたらエラーメッセージを返す関数
const checkTree = (tree: Document) => {
/**
 *  https://developer.mozilla.org/ja/docs/Web/API/Document/querySelectorAll
 * https://developer.mozilla.org/ja/docs/Web/API/NodeList
 */

  const strongTags : NodeList = tree.querySelectorAll('strong');

  let hasError = false;

  // https://developer.mozilla.org/ja/docs/Web/API/Node
  strongTags.forEach((strong: Node) => {
    // https://developer.mozilla.org/ja/docs/Web/API/Node/nodeType#%E5%80%A4
    const childNodes = strong.childNodes;

    childNodes.forEach((node: Node) => {
      if(node.nodeType === Node.ELEMENT_NODE){
        hasError = true;
      }
    })
  });

  return hasError ? "strongタグの中にタグが入っています"  : tree.body.innerHTML;
}

watchEffect(() => {
  const newContent = props.content
  console.log('SSMLの値が変更されました', newContent)
  htmlContent = parseToHtml(newContent)
})
</script>

<style scoped lang="scss">
p {
  font-size: 12px;
}
</style>
