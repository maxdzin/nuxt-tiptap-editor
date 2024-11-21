---
title: Image Upload Example
---

# Image Upload

In this example we will go through using the custom image upload extension added **exclusively** to this Nuxt plugin.

::: tip EXCLUSIVE TO PLUGIN
This custom extension is built into Nuxt Tiptap Editor plugin exclusively.
:::

::: warning Image Upload is available only after v2 release
If you are using an older version of `nuxt-tiptap-editor` you will need to upgrade package to version **>=2.0.0** to be able to use this feature.

```
npm install nuxt-tiptap-editor@^2.0.0

yarn add nuxt-tiptap-editor@^2.0.0

pnpm add nuxt-tiptap-editor@^2.0.0
```
:::

## Usage

The plugin exposes 2 extensions `ImageUpload` and `ImagePlaceholder`. Both are dependent on `Image` extension (installed by default).

`ImageUpload` requires async function with `file` and `id` as arguments to be passed to `upload` config.  
Check [this](https://github.com/modbender/nuxt-tiptap-editor/blob/25c370599a691f93bcaf401819c4f8136a085c47/src/custom-extensions/extension-image-upload/imageUpload.ts#L4) part of the code to see all options.

`ImagePlaceholder` exposes the `inline` config which adds `display: inline-block` CSS to the placeholder image.  
Check [this](https://github.com/modbender/nuxt-tiptap-editor/blob/25c370599a691f93bcaf401819c4f8136a085c47/src/custom-extensions/extension-image-upload/imagePlaceholder.ts#L5) part of code to see all options.


## Example component

```vue
<template>
  <div>
    <div v-if="editor">
      <button
        :disabled="!editor.can().chain().focus().toggleBold().run()"
        :class="{ 'is-active': editor.isActive('bold') }"
        @click="editor.chain().focus().toggleBold().run()"
      >
        bold
      </button>
      <button
        :disabled="!editor.can().chain().focus().toggleItalic().run()"
        :class="{ 'is-active': editor.isActive('italic') }"
        @click="editor.chain().focus().toggleItalic().run()"
      >
        italic
      </button>
      <button
        :disabled="!editor.can().chain().focus().toggleStrike().run()"
        :class="{ 'is-active': editor.isActive('strike') }"
        @click="editor.chain().focus().toggleStrike().run()"
      >
        strike
      </button>
      <button
        :disabled="!editor.can().chain().focus().toggleCode().run()"
        :class="{ 'is-active': editor.isActive('code') }"
        @click="editor.chain().focus().toggleCode().run()"
      >
        code
      </button>
      <button @click="editor.chain().focus().unsetAllMarks().run()">
        clear marks
      </button>
      <button @click="editor.chain().focus().clearNodes().run()">
        clear nodes
      </button>
      <button
        :class="{ 'is-active': editor.isActive('paragraph') }"
        @click="editor.chain().focus().setParagraph().run()"
      >
        paragraph
      </button>
      <button
        :class="{ 'is-active': editor.isActive('heading', { level: 1 }) }"
        @click="editor.chain().focus().toggleHeading({ level: 1 }).run()"
      >
        h1
      </button>
      <button
        :class="{ 'is-active': editor.isActive('heading', { level: 2 }) }"
        @click="editor.chain().focus().toggleHeading({ level: 2 }).run()"
      >
        h2
      </button>
      <button
        :class="{ 'is-active': editor.isActive('heading', { level: 3 }) }"
        @click="editor.chain().focus().toggleHeading({ level: 3 }).run()"
      >
        h3
      </button>
      <button
        :class="{ 'is-active': editor.isActive('heading', { level: 4 }) }"
        @click="editor.chain().focus().toggleHeading({ level: 4 }).run()"
      >
        h4
      </button>
      <button
        :class="{ 'is-active': editor.isActive('heading', { level: 5 }) }"
        @click="editor.chain().focus().toggleHeading({ level: 5 }).run()"
      >
        h5
      </button>
      <button
        :class="{ 'is-active': editor.isActive('heading', { level: 6 }) }"
        @click="editor.chain().focus().toggleHeading({ level: 6 }).run()"
      >
        h6
      </button>
      <button
        :class="{ 'is-active': editor.isActive('bulletList') }"
        @click="editor.chain().focus().toggleBulletList().run()"
      >
        bullet list
      </button>
      <button
        :class="{ 'is-active': editor.isActive('orderedList') }"
        @click="editor.chain().focus().toggleOrderedList().run()"
      >
        ordered list
      </button>
      <button
        :class="{ 'is-active': editor.isActive('codeBlock') }"
        @click="editor.chain().focus().toggleCodeBlock().run()"
      >
        code block
      </button>
      <button
        :class="{ 'is-active': editor.isActive('blockquote') }"
        @click="editor.chain().focus().toggleBlockquote().run()"
      >
        blockquote
      </button>
      <button @click="editor.chain().focus().setHorizontalRule().run()">
        horizontal rule
      </button>
      <button @click="editor.chain().focus().setHardBreak().run()">
        hard break
      </button>
      <button
        :disabled="!editor.can().chain().focus().undo().run()"
        @click="editor.chain().focus().undo().run()"
      >
        undo
      </button>
      <button
        :disabled="!editor.can().chain().focus().redo().run()"
        @click="editor.chain().focus().redo().run()"
      >
        redo
      </button>
    </div>
    <TiptapEditorContent :editor="editor" />
  </div>
</template>

<script setup>
const toast = useToast()

async function uploadImage(file, id) {
  try {
    const formData = new FormData()
    formData.append(id, file)

    const urls = await $fetch('/api/upload', {
      method: 'POST',
      body: formData,
    })

    return urls[0]
  }
  catch (e) {
    return toast.add({
      severity: 'error',
      summary: e.statusMessage,
      detail: e.statusMessage,
      life: 3000,
    })
  }
}

const editor = useEditor({
  content: '<p>I\'m running Tiptap with Vue.js. 🎉</p>',
  extensions: [
    TiptapStarterKit,
    TiptapImage,
    TiptapImageUpload.configure({
      acceptMimes: ['image/jpeg', 'image/gif', 'image/png', 'image/jpg'],
      upload: uploadImage,
    }),
    TiptapImagePlaceholder.configure({
      inline: false,
    }),
  ],
})
</script>
```