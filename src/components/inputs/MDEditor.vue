<template>
  <div>
    <div id="md-editor">
      <textarea
        id="inputField"
        ref="inputField"
        :value="contents"
        @input="edited"
      />
      <div
        id="preview"
        ref="preview"
        v-html="compiledMarkdown"
      />
    </div>
    <p>
      This field is Markdown that will be rendered as HTML. For formatting
      rules, read
      <a
        href="https://www.markdownguide.org/basic-syntax/"
        target="_blank"
      >
        here</a>. The style is under the column titled "Markdown." Use two spaces instead
      of tab.
    </p>
  </div>
</template>

<script lang="ts">
import { Component, Vue, Prop, Emit, Watch } from "vue-property-decorator";
import marked from "marked";
import { Debounce, Optional } from "@/util";

@Component
export default class MDEditor extends Vue {
  // displayed value
  contents: string = "";

  get compiledMarkdown() {
    return marked(this.contents);
  }

  // value from parent
  @Prop() value: Optional<string>;

  mounted() {
    if (this.value) {
      this.contents = this.value;
    }
    window.addEventListener("resize", this.debounceResize);
  }

  @Watch("value")
  valueChanged(value: Optional<string>) {
    if (value) {
      this.contents = value;
    }
  }

  // responding to editing
  edited(event: Event) {
    this.update(event);
    this.resize();
  }

  @Debounce(300)
  @Emit("input")
  update(event: Event) {
    const target = event.target as HTMLTextAreaElement;
    const newContents = target.value;

    if (newContents == null) {
      return "";
    }

    this.contents = newContents;
    return newContents;
  }

  // resizing
  updated() {
    this.resize();
  }

  @Debounce(50)
  debounceResize() {
    this.resize();
  }

  resize() {
    let inputField = this.$refs.inputField as HTMLInputElement;
    let preview = this.$refs.preview as HTMLInputElement;

    inputField.style.height = "auto";
    preview.style.height = "auto";

    const inputSize = inputField.scrollHeight;
    const previewSize = preview.clientHeight;

    if (inputSize > previewSize) {
      inputField.style.height = inputSize + "px";
      preview.style.height = inputSize + "px";
    } else {
      inputField.style.height = previewSize + "px";
      preview.style.height = previewSize + "px";
    }
  }
}
</script>

<style lang="scss">
@import "@/shared-style/mixins.scss";
@import "@/shared-style/variables.scss";

#md-editor {
  @include rounded;
  border: 1px solid $boarderColor;

  #inputField,
  #preview {
    display: inline-block;
    width: 49%;
    height: 100%;
    vertical-align: top;
    box-sizing: border-box;
    padding: 10px 20px;
  }

  #inputField {
    border-right: 1px solid $boarderColor;
    resize: none;
    background-color: $insetEditor;
    font-size: 14px;
    font-family: "SFMono-Regular", "Consolas", "Monaco", courier, monospace;
  }

  #preview {
    h2 {
      padding-top: 1.25em;
    }
  }
}
</style>
