<template>
  <div class="markdown-editor">
    <div v-if='!state_preview_mode'>
      <b-form-textarea v-model="state_textarea" :placeholder="state_placeholder" 
        :rows="state_rows" :maxlength="state_max_length"
        :state="state_item(state_textarea,state_must)" 
        @blur="state_focus()" />
    </div>
    <div v-if='!state_preview_mode' class="mt-1 ml-2 float-right">
      <b-button variant="outline-secondary" size="sm" @click="mode_change">{{ lang('preview') }}</b-button>
    </div>
    <div v-if='state_preview_mode' class="preview_mode">
      <div class="markdown-body" v-html="state_result"></div>
    </div>
    <div v-if='state_preview_mode' class="mt-2 ml-2 float-right">
      <b-button variant="outline-secondary" size="sm" @click="mode_change">{{ lang('back') }}</b-button>
    </div>
    <div class="text-secondary mt-1 mb-0 small">{{ state_description }}</div>
    <br>
  </div>
</template>

<script>
import lang from './lang.json';
// ブラウザからデフォルトの言語を取得する
let locale = navigator.language;
if( locale != "ja" && locale != "en" ) locale = "en";

import markdownit from 'markdown-it';
import 'github-markdown-css/github-markdown.css';

export default {
  name: 'sw-markdown-editor',
  components: {
  },
  props: {
    // マークダウンテキスト初期値（編集時）
    md_text: {
      type: String,
      default: ""
    },
    // 行数
		rows: {
      type: Number,
      default: 0
    },
    // 最大文字数
    max_length: {
      type: Number,
      default: 30,
    },
    // 必須
    must: {
      type: Boolean,
      default: false
    },
    // プレースホルダー
    placeholder: {
      type: String,
      default: ""
    },
    // 補足説明
    description: {
      type: String,
      default: ""
    },
  },
  // ローカルデータ変数
  data () {
    return {
      markdownit: null, //マークダウンオブジェクト
      state_rows: 3, // 行数
      state_max_length: 10, // 最大文字数
      state_must: false, // 必須
      state_placeholder: "",
      state_description: "",
      state_preview_mode: false,  //プレビューモード
      state_textarea: "",
      state_result: "",
      state_data_focus: false
    }
  },
  // 既定計算
  computed: {
    state_item: function() {
      return function(item_data,item_required){
        if( item_required ){
          if( item_data === "" || item_data === null ){
            if( this.state_data_focus ){
              return false;
            } else {
              return null;
            }
          } else {
            return true;
          }
        } else {
          if( item_data === "" || item_data === null ){
            return null;
          } else {
            return true;
          }
        }
      }
    },
  },
  // 監視
  watch: {
    md_text: function(){
      this.state_textarea = this.md_text;
    },
    rows: function(){
      this.state_rows = this.rows;
    },
    max_length: function(){
      this.state_max_length = Number(this.max_length);
    },
    must: function(){
      this.state_must = this.must;
    },
    placeholder: function(){
      this.state_placeholder = this.placeholder;
    },
    description: function(){
      this.state_description = this.description;
    },
    state_textarea: function(){
      this.$emit('input',this.state_textarea);
    },
  },
  // インスタンス初期化後
  created(){
    this.markdownit = new markdownit();
  },
  // インスタンス破棄後
  destroyed() {
  },
  // インスタンスマウント後
  mounted(){
    this.state_textarea = this.md_text;
    this.state_rows = this.rows;
    this.state_max_length = this.max_length;
    this.state_must = this.must;
    this.state_placeholder = this.placeholder;
    this.state_description = this.description;
  },
  // ローカル関数
  methods: {
    lang: function( param ){
      return lang[locale][param];
    },
    state_focus: function(){
      this.state_data_focus = true;
    },
    mode_change: function(){
      this.state_preview_mode = !this.state_preview_mode;
      if( this.state_textarea == null || this.state_textarea == "" ){
        this.state_result = "";
      } else {
        this.state_result = this.markdownit.render(this.state_textarea);
      }
    }
  }
};
</script>
<style scoped>
.markdown-editor {
  text-align: left;
  padding: 4px;
}
.preview_mode {
  box-sizing: border-box;
  margin: 7px 0px 7px 0px;
}
</style>