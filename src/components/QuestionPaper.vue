<template>
  <div class="grid-cols-1 md:grid-cols-2 grid"  id="qapp">
    <div class="md:border-r-2 border-b-2 md:border-b-0 border-red-500" id="editor-container">
      <div id="editor"></div>
    </div>
    <div id="preview-container">
      <div id="preview" v-html="previewHtml"></div>
    </div>
    <div class="md:col-span-2" id="toolbar">
      <button class="py-2 font-bold px-4 bg-blue-600 hover:bg-blue-500 text-white rounded-lg cursor-pointer" @click="printPreview">Print Preview</button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Vditor from 'vditor';
import 'vditor/dist/index.css';

const markdown = ref('# Welcome to the Editor\n\nWrite Markdown with **bold**, *italic*, and LaTeX math like $E=mc^2$ or $$\\int_a^b f(x)\\,dx$$.');
const previewHtml = ref('');
let vditor = null;

const updatePreview = (md) => {
    const previewEl = document.getElementById('preview');
    Vditor.preview(previewEl, md, {
        markdown: {
            toc: true,
            sanitize: false,
        },
        render: {
            math: {
                engine: 'KaTeX',
                inlineDigit: true
            },
            hljs: {
                style: 'github'
            },
            media: {
                enable: true,
            }
        }
    });
    previewHtml.value = previewEl.innerHTML;
};

const printPreview = () => {
    window.print();
};

onMounted(() => {
    vditor = new Vditor('editor', {
        mode: 'wysiwyg',
        height: '100%',
        minHeight: 300,
        placeholder: 'Start typing your Markdown here...',
        preview: {
            math: {
                engine: 'KaTeX',
                inlineDigit: true
            },
            hljs: {
                style: 'github'
            }
        },
        input: (value) => {
            markdown.value = value;
            updatePreview(value);
        },
        after: () => {
            vditor.setValue(markdown.value);
            updatePreview(markdown.value);
        },
        lang: 'en_US',
        cache: {
            enable: false
        }
    });
});
</script>

<style>
body {
    margin: 0;
    padding: 0;
    font-family: serif;
}

#editor-container {
    overflow: auto;
}

#preview-container {
    overflow: auto;
    padding: 20px;
    background-color: #f9f9f9;
    flex: 1;
}

#toolbar {
    padding: 10px;
    text-align: center;
}

#preview {
  white-space: pre-wrap !important; /* Preserve whitespace and line breaks */
}
/* Print styles */
@media print {
    @page {
        size: A4 landscape;
        margin: 1cm;
    }
    body * {
        visibility: hidden;
    }
    #preview-container, #preview-container * {
        visibility: visible;
    }
    #preview-container {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: auto;
        padding: 0;
        background: none;
        overflow: hidden;

    }

    #preview {
        width: 277mm; /* A4 landscape width minus margins (297mm - 2cm) */
        /* height: 190mm;  */
        height: auto; /* A4 landscape height minus margins (210mm - 2cm) */
        margin: 0;
        box-sizing: border-box;
        column-count: 2; /* Two-column layout */
        column-gap: 20px; /* Space between columns */
        column-fill: auto; /* Fill first column before second */
        overflow: visible; /* Allow content to flow to new pages */
    }
    #qapp #editor-container, #toolbar {
        display: none;
    }
}
</style>
