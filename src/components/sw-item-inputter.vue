<template>
  <div class="item-inputter text-left" v-if="state_data.loaded && state_data.state">
    <template v-if='bind_data.item_info.item_type=="text" || 
      bind_data.item_info.item_type=="texts" || 
      bind_data.item_info.item_type=="number" || 
      bind_data.item_info.item_type=="radio" || 
      bind_data.item_info.item_type=="checkbox" ||
      bind_data.item_info.item_type=="toggle" || 
      bind_data.item_info.item_type=="telephone" || 
      bind_data.item_info.item_type=="date" ||
      bind_data.item_info.item_type=="time" || 
      bind_data.item_info.item_type=="datetime" ||
      bind_data.item_info.item_type=="table" || 
      bind_data.item_info.item_type=="markdown" ||
      bind_data.item_info.item_type=="image" ||
      bind_data.item_info.item_type=="password" ||
      bind_data.item_info.item_type=="email" ||
      bind_data.item_info.item_type=="pulldown" ||
      bind_data.item_info.item_type=="url"' >
      <label class="text-secondary mt-2 mb-0" v-bind:class="bind_data.font_info">{{ bind_data.item_info.item_name }}</label>
      <b-badge v-if='bind_data.item_info.item_required && bind_data.item_info.item_required_badge=="checked"' variant="danger" class="mx-2">{{ lang('mandatory') }}</b-badge>
    </template>

    <!-- 短文テキスト -->
    <template v-if='bind_data.item_info.item_type=="text"' >
      <b-form-input type="text" v-model="bind_data.item_data" class="mt-0 mb-0" 
        :placeholder="bind_data.item_info.item_placeholder" 
        :maxlength="bind_data.item_info.item_length" 
        :state="state_item(bind_data.item_data,bind_data.item_info)" 
        @blur="state_focus()" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 長文テキスト -->
    <template v-if='bind_data.item_info.item_type=="texts"'>
      <b-form-textarea type="text" v-model="bind_data.item_data" class="mt-0 mb-0" 
        :placeholder="bind_data.item_info.item_placeholder" 
        :rows="bind_data.item_info.item_rows" 
        :maxlength="bind_data.item_info.item_length" 
        :state="state_item(bind_data.item_data,bind_data.item_info)" 
        @blur="state_focus()" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 数値 -->
    <template v-if='bind_data.item_info.item_type=="number"'>
      <b-input-group>
        <b-form-input type="number" v-model="bind_data.item_data" class="mt-0 mb-0" 
          :placeholder="bind_data.item_info.item_placeholder" 
          :min="bind_data.item_info.item_limit_min" :max="bind_data.item_info.item_limit_max" 
          :state="state_item(bind_data.item_data,bind_data.item_info)" 
          @blur="state_focus()" />
        <b-input-group-append v-if='bind_data.item_info.item_unit_name!==""'>
          <b-input-group-text>{{ bind_data.item_info.item_unit_name }}</b-input-group-text>
        </b-input-group-append>
      </b-input-group>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- ラジオボタン -->
    <template v-if='bind_data.item_info.item_type=="radio"'>
      <b-form-radio-group v-model="bind_data.item_data" 
          :options="bind_data.item_info.item_options" 
          :stacked="bind_data.item_info.item_stacked" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- チェックボックス -->
    <template v-if='bind_data.item_info.item_type=="checkbox"'>
      <b-form-checkbox-group v-model="bind_data.item_data" 
          :options="bind_data.item_info.item_options" 
          :stacked="bind_data.item_info.item_stacked" 
          :state="state_item(bind_data.item_data,bind_data.item_info)" 
          @input="state_focus()" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- スイッチ -->
    <template v-if='bind_data.item_info.item_type=="toggle"'>
      <b-form-checkbox v-model="bind_data.item_data" switch size="lg"
        :value="bind_data.item_info.item_checked_value" 
        :unchecked-value="bind_data.item_info.item_unchecked_value"
        :state="state_item(bind_data.item_data,bind_data.item_info)" 
        @input="state_focus()" >
        <span class="small">{{ bind_data.item_info.item_placeholder }}</span>
      </b-form-checkbox>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 氏名 -->
    <template v-if='bind_data.item_info.item_type=="name"' >
      <b-row>
        <b-col v-for="n in 2" :key="n" cols="6">
          <label class="text-secondary mt-2 mb-0" v-bind:class="bind_data.font_info">{{ bind_data.item_info.item_name[n-1] }}</label>
          <b-badge v-if='bind_data.item_info.item_required && bind_data.item_info.item_required_badge=="checked"' variant="danger" class="mx-2">{{ lang('mandatory') }}</b-badge>
          <b-form-input type="text" v-model="bind_data.item_data[n-1]" class="mt-0 mb-0" 
            :placeholder="bind_data.item_info.item_placeholder[n-1]" 
            :maxlength="bind_data.item_info.item_length"
            :state="state_item(bind_data.item_data,bind_data.item_info)" 
            @blur="state_focus()" />
        </b-col>
      </b-row>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 電話番号 -->
    <template v-if='bind_data.item_info.item_type=="telephone"' >
      <!-- <b-row>
        <b-col v-for="n in 3" :key="n" cols="4">
          <b-form-input type="text" v-model="bind_data.item_data[n-1]" class="mt-0 mb-0" 
            :placeholder="bind_data.item_info.item_placeholder[n-1]" 
            :maxlength="bind_data.item_info.item_length"
            :state="state_item(bind_data.item_data,bind_data.item_info)"
            @blur="state_focus()" />
        </b-col>
      </b-row> -->
      <b-form-input type="tel" v-model="bind_data.item_data" class="mt-0 mb-0" 
        :placeholder="bind_data.item_info.item_placeholder" 
        :maxlength="bind_data.item_info.item_length" 
        :state="state_item(bind_data.item_data,bind_data.item_info)" 
        @blur="state_focus()" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 日付 -->
    <template v-if='bind_data.item_info.item_type=="date"'>
      <b-input-group>
        <b-form-datepicker v-model="bind_data.item_data" class="mb-0" :placeholder="bind_data.item_info.item_placeholder" locale="ja"
            :date-format-options="{ year: 'numeric', month: '2-digit', day: '2-digit', weekday: 'short' }" 
            :state="state_item(bind_data.item_data,bind_data.item_info)" 
            @hidden="state_focus()" />
        <b-input-group-append is-text style="height:38px;">
          <b-icon icon="eraser" class="mt-0 item-curosr" @click="erase_item_data()"></b-icon>
        </b-input-group-append>
      </b-input-group>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 時刻 -->
    <template v-if='bind_data.item_info.item_type=="time"'>
      <b-input-group>
        <b-form-timepicker v-model="bind_data.item_data" class="mb-0" :placeholder="bind_data.item_info.item_placeholder" 
            now-button label-now-button="現在" now-button-variant="outline-secondary" label-close-button="閉じる" label-no-time-selected="未設定"
            :state="state_item(bind_data.item_data,bind_data.item_info)" 
            @hidden="state_focus()" />
        <b-input-group-append is-text style="height:38px;">
          <b-icon icon="eraser" class="mt-0 item-curosr" @click="erase_item_data()" ></b-icon>
        </b-input-group-append>
      </b-input-group>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>
    
    <!-- 日時 -->
    <template v-if='bind_data.item_info.item_type=="datetime"' >
      <b-row>
        <b-col :cols="bind_data.item_info.item_cols">
          <b-input-group>
            <b-form-datepicker v-model="bind_data.item_data[0]" class="mb-2" :placeholder="bind_data.item_info.item_placeholder[0]" locale="ja"
                :date-format-options="{ year: 'numeric', month: '2-digit', day: '2-digit', weekday: 'short' }" 
                :state="state_item(bind_data.item_data,bind_data.item_info)" 
                @hidden="state_focus()" />
            <b-input-group-append is-text style="height:38px;">
              <b-icon icon="eraser" class="mt-0 item-curosr" @click="erase_item_data(0)" ></b-icon>
            </b-input-group-append>
          </b-input-group>
        </b-col>
        <b-col :cols="bind_data.item_info.item_cols">
          <b-input-group>
            <b-form-timepicker v-model="bind_data.item_data[1]" class="mb-0" :placeholder="bind_data.item_info.item_placeholder[1]" 
                now-button label-now-button="現在" now-button-variant="outline-secondary" label-close-button="閉じる" label-no-time-selected="未設定"
                :state="state_item(bind_data.item_data,bind_data.item_info)" 
                @hidden="state_focus()" />
            <b-input-group-append is-text style="height:38px;">
              <b-icon icon="eraser" class="mt-0 item-curosr" @click="erase_item_data(1)" ></b-icon>
            </b-input-group-append>
          </b-input-group>
        </b-col>
      </b-row>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- 表 -->
    <template v-if='bind_data.item_info.item_type=="table"' >
      <b-table bordered responsive :items="bind_data.table_items" :fields="bind_data.table_fields" class="mb-0 text-nowrap">
        <template #cell()="data">
          <div v-if='data.field.key=="row_name"' class="text-nowrap">{{ data.value }}</div>
          <div v-else><b-form-input size="sm" v-model="data.value" maxlength="100" @input="table_data_change(data)"></b-form-input></div>
        </template>
      </b-table>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- マークダウン -->
    <template v-if='bind_data.item_info.item_type=="markdown"' >
      <sw-markdown-editor :md_text="bind_data.item_data" 
          :rows="bind_data.item_info.item_rows"
          :max_length="bind_data.item_info.item_length" 
          :must="bind_data.item_info.item_required"
          :placeholder="bind_data.item_info.item_placeholder" 
          :description="bind_data.item_info.item_description" 
          v-model="bind_data.item_data" />
    </template>

    <!-- 写真 -->
    <template v-if='bind_data.item_info.item_type=="image"' >
      <div v-if="state_image_preview_show">
        <b-img :src="bind_data.image_preview_src" class="my-1 p-1 image-shadow" style="max-height:400px;max-width:100%;"></b-img>
      </div>
      <b-input-group class="mt-1">
        <b-form-file v-model="bind_data.image_file" @input="item_image_input"
          :placeholder="bind_data.item_info.item_placeholder"
          :drop-placeholder="lang('drop_here')" accept=".jpg,.png,.JPG,.PNG" 
          :state="state_item(bind_data.item_data,bind_data.item_info)"
          @change="state_focus()"  />
        <b-input-group-append>
          <b-button variant="info" @click="item_image_clear">Clear</b-button>
        </b-input-group-append>
      </b-input-group>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- パスワード -->
    <template v-if='bind_data.item_info.item_type=="password"' >
      <b-input-group>
        <b-form-input :type="state_data.password_type" v-model="bind_data.item_data" class="mt-0 mb-0" 
          :placeholder="bind_data.item_info.item_placeholder" 
          :maxlength="bind_data.item_info.item_length_max" 
          :state="state_item(bind_data.item_data,bind_data.item_info)" 
          @blur="state_focus()" />
        <b-input-group-append is-text>
          <b-link v-on:click="password_icon"><b-icon :icon="state_data.password_icon"></b-icon></b-link>
        </b-input-group-append>
      </b-input-group>
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- メールアドレス -->
    <template v-if='bind_data.item_info.item_type=="email"' >
      <b-form-input type="text" v-model="bind_data.item_data" class="mt-0 mb-0" 
        :placeholder="bind_data.item_info.item_placeholder" 
        :maxlength="bind_data.item_info.item_length" 
        :state="state_item(bind_data.item_data,bind_data.item_info)" 
        @blur="state_focus()" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- プルダウン -->
    <template v-if='bind_data.item_info.item_type=="pulldown"'>
      <b-form-select v-if='bind_data.item_info.item_method=="static"' v-model="bind_data.item_data" :options="bind_data.item_info.item_options" />
      <b-form-select v-if='bind_data.item_info.item_method=="dynamic"' v-model="bind_data.item_data" :options="bind_data.pulldown_list" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- ラベル -->
    <template v-if='bind_data.item_info.item_type=="label"'>
      <div v-if='bind_data.item_info.item_markdown_checked=="true"'>
        <sw-markdown-viewer :md_text="bind_data.item_info.item_description" />
      </div>
      <div v-else class="text-secondary mt-1 mb-0 long_text">{{ bind_data.item_info.item_description }}</div>
    </template>

    <!-- URL -->
    <template v-if='bind_data.item_info.item_type=="url"' >
      <b-form-input type="text" v-model="bind_data.item_data" class="mt-0 mb-0" 
        :placeholder="bind_data.item_info.item_placeholder" 
        :maxlength="bind_data.item_info.item_length" 
        :state="state_item(bind_data.item_data,bind_data.item_info)" 
        @blur="state_focus()" />
      <div class="text-secondary mt-1 mb-0 small">{{ bind_data.item_info.item_description }}</div>
    </template>

    <div v-if="state_data.debug" class="text-left">{{ JSON.stringify(bind_data,null,2) }}</div>
  </div>
  <div v-else>
    <div class="text-center">
      <b-icon icon="three-dots" animation="cylon" font-scale="3" variant="secondary"></b-icon>
    </div>
  </div>
  
</template>

<script>
import lang from './lang.json';
// ブラウザからデフォルトの言語を取得する
let locale = navigator.language;
if( locale != "ja" && locale != "en" ) locale = "en";

import validator from 'validator';
import swMarkdownEditor from '@/components/sw-markdown-editor';
import swMarkdownViewer from '@/components/sw-markdown-viewer';
export default {
  name: 'sw-item-inputter',
  components: {
    swMarkdownEditor,swMarkdownViewer
  },
  props: {
    // Item情報
    item_info: {
      type: Object,
      default: () => null
    },
    item_data: {
      type: [ String, Array, Object ],
      default: () => null,
    },
    list_info: {
      type: Array, 
      default: null
    },
    font_info: {
      type: String, 
      default: "small"
    }
  },
  // ローカルデータ変数
  data () {
    return {
      // バインドデータ
      bind_data: {
        item_info: null,
        item_data: null,
        table_fields: [],
        table_items: [],
        image_file: null,
        image_preview_src: "",
        pulldown_list: [],
        font_info: "small",//font-weight-bold small
      },
      local_data: {
        table_field: { key: 'row_name', label: '', stickyColumn: true, }, 
      },
      state_data: {
        loaded: false,
        state: false,
        preview: false,
        debug: false,
        show: false,
        focus: false,
        password_type: "password",
        password_icon: "eye",
      }
    }
  },
  // 既定計算
  computed: {
    state_item: function() {
      return function(item_data,item_info){
        if( item_info.item_required ){
          return this.required_state_item(item_data,item_info,this.state_data);
        } else {
          return this.any_state_item(item_data,item_info);
        }
      }
    },
    inputedValue: {
      get() {
        return this.value;
      },
      set(newValue) {
        this.$emit("input", newValue);
      },
    },
    state_image_preview_show(){
      if( this.bind_data.image_preview_src == null || this.bind_data.image_preview_src == "" ){
        return false;
      } else {
        return true;
      }
    },
  },
  // 監視
  watch: {
    'item_info': {
      handler: function(){
        //console.log("ItemInputter:watch:item_info:"+JSON.stringify(this.item_info,null,2))
        this.bind_data.item_info = this.item_info;
        if( this.bind_data.item_info.item_type == "table" ){
          this.table_data_set();
        }
        if( this.bind_data.item_info.item_type == "pulldown" ){
          if( this.bind_data.item_info.item_method == "dynamic" && this.list_info !== null ){
            this.pulldown_data_set();
          }
        }
      },
      deep: true,
    },
    'bind_data.item_data': {
      handler: function(){
        if( this.bind_data.item_info.item_type == "number" ){
          this.number_data_set();
        }
        //console.log("ItemInputter:watch"+JSON.stringify(ret));
        this.$emit('input',this.get_ret_data());
      },
      deep: true,
    },
    'state_data.show': {
      handler: function(){
        if( this.bind_data.item_info.item_type == "number" ){
          this.number_data_set();
        }
        //console.log("ItemInputter:watch"+JSON.stringify(ret));
        this.$emit('input',this.get_ret_data());
      },
      deep: true,
    },
    font_info(){
      this.bind_data.font_info = this.font_info;
    }
  },
  // インスタンス初期化後
  created(){
  },
  // インスタンス破棄後
  destroyed() {
  },
  // インスタンスマウント後
  mounted(){
    this.is_show();
    this.bind_data.item_info = this.item_info;
    this.bind_data.font_info = this.font_info;
    if( this.item_data !== null ){
      //console.log("ItemInputter:mounted:"+JSON.stringify(this.item_data))
      this.bind_data.item_data = this.item_data;
    }
    if( this.bind_data.item_info.item_type == "table" ){
      this.table_data_set();
    }
    if( this.bind_data.item_info.item_type == "pulldown" ){
      if( this.bind_data.item_info.item_method == "dynamic" && this.list_info !== null ){
        this.pulldown_data_set();
      }
    }
    if( this.bind_data.item_info.item_type == "image" ){
      this.image_data_set();
    }
    
    this.item_check();
    this.state_data.loaded = false;
    this.$nextTick(function() {
      this.state_data.loaded = true;
    });
    this.$emit('input',this.get_ret_data());
  },
  // ローカル関数
  methods: {
    lang: function( param ){
      return lang[locale][param];
    },
    state_focus: function(){
      this.state_data.focus = true;
      this.$emit('focusin',true);
    },
    item_check: function(){
      if( this.bind_data.item_data === null ){
        this.init_item_data();
      }
      this.state_data.state = false;
      this.$nextTick(function() {
        this.state_data.state = this.bind_data.item_info.item_state;
      });
    },
    //
    // 必須項目の時の状態チェック
    //
    required_state_item: function(item_data,item_info,state_data){
      //console.log(item_info.item_type+":"+item_data+":"+state_data.focus);
      if( state_data.focus ){
        // 短いテキスト
        if( item_info.item_type == "text" ){
          return ( this.is_allowed_type(item_data,item_info) ? true : false );
        }
        // パスワード
        if( item_info.item_type == "password" ){
          return ( this.is_password_type(item_data,item_info) ? true : false );
        }
        // メールアドレス
        if( item_info.item_type == "email" ){
          return ( this.is_email_type(item_data,item_info) ? true : false );
        }
        // スイッチ
        if( item_info.item_type == "toggle" ){
          return ( this.is_toggle_type(item_data,item_info) ? true : false );
        }
        // チェックボックス 氏名 日時
        if( item_info.item_type == "checkbox" || item_info.item_type == "name" || item_info.item_type == "datetime" ){
          return ( this.is_array_type( item_data ) ? true : false );
        }
        // 長いテキスト 数値 日付 時刻 写真
        if( item_info.item_type == "texts" || item_info.item_type == "number" || item_info.item_type == "date" 
            || item_info.item_type == "time" || item_info.item_type == "markdown" || item_info.item_type == "image" ){
          return ( this.is_string_type( item_data ) ? true : false );
        }
        // ラジオボタン プルダウン 表
        if( item_info.item_type == "radio" || item_info.item_type == "pulldown" || item_info.item_type == "table" 
            || item_info.item_type == "label" ){
          return true;
        }
        // 電話番号
        if( item_info.item_type == "telephone" ){
          return ( this.is_telephone_type( item_data,item_info ) ? true : false );
        }
        // URL
        if( item_info.item_type == "url" ){
          return ( this.is_url_type(item_data,item_info) ? true : false );
        }
        // その他
        return false;
      } else {
        // 短いテキスト
        if( item_info.item_type == "text" ){
          return this.is_allowed_type(item_data,item_info);
        }
        // パスワード
        if( item_info.item_type == "password" ){
          return this.is_password_type(item_data,item_info);
        }
        // メールアドレス
        if( item_info.item_type == "email" ){
          return this.is_email_type(item_data,item_info);
        }
        // スイッチ
        if( item_info.item_type == "toggle" ){
          return this.is_toggle_type(item_data,item_info);
        }
        // チェックボックス 氏名 日時
        if( item_info.item_type == "checkbox" || item_info.item_type == "name" || item_info.item_type == "datetime" ){
          return this.is_array_type( item_data );
        }
        // 長いテキスト 数値 日付 時刻 写真
        if( item_info.item_type == "texts" || item_info.item_type == "number" || item_info.item_type == "date" 
            || item_info.item_type == "time" || item_info.item_type == "markdown" || item_info.item_type == "image" ){
          return this.is_string_type( item_data );
        }
        // ラジオボタン プルダウン 表
        if( item_info.item_type == "radio" || item_info.item_type == "pulldown" || item_info.item_type == "table" 
            || item_info.item_type == "label" ){
          return true;
        }
        // 電話番号
        if( item_info.item_type == "telephone" ){
          return this.is_telephone_type( item_data,item_info );
        }
        // URL
        if( item_info.item_type == "url" ){
          return ( this.is_url_type(item_data,item_info) ? true : false );
        }
        // その他
        return null;      
      }
    },
    //
    // 任意項目の時の状態チェック
    //
    any_state_item: function(item_data,item_info){
      // 短いテキスト
      if( item_info.item_type == "text" ){
        return this.is_allowed_type(item_data,item_info);
      }
      // パスワード
      if( item_info.item_type == "password" ){
        return this.is_password_type(item_data,item_info);
      }
      // メールアドレス
      if( item_info.item_type == "email" ){
        return this.is_email_type(item_data,item_info);
      }
      // スイッチ
      if( item_info.item_type == "toggle" ){
        return this.is_toggle_type(item_data,item_info);
      }
      // チェックボックス 氏名 日時
      if( item_info.item_type == "checkbox" || item_info.item_type == "name" || item_info.item_type == "datetime" ){
        return this.is_array_type( item_data );
      }
      // 長いテキスト 数値 日付 時刻 写真
      if( item_info.item_type == "texts" || item_info.item_type == "number" || item_info.item_type == "date" 
          || item_info.item_type == "time" || item_info.item_type == "markdown" || item_info.item_type == "image" ){
        return this.is_string_type( item_data );
      }
      // ラジオボタン プルダウン 表 ラベル
      if( item_info.item_type == "radio" || item_info.item_type == "pulldown" || item_info.item_type == "table" 
          || item_info.item_type == "label" ){
        return true;
      }
      // 電話番号
      if( item_info.item_type == "telephone" ){
        return this.is_telephone_type( item_data,item_info );
      }
      // URL
      if( item_info.item_type == "url" ){
        return this.is_url_type(item_data,item_info);
      }
      // その他
      return null;
    },
    get_state_item: function(item_data,item_info){
      if( item_info.item_required ){
        return this.required_state_item(item_data,item_info,this.state_data);
      } else {
        return this.any_state_item(item_data,item_info);
      }  
    },
    get_ret_data: function(){
      //console.log("ItemInputter:get_ret_data="+JSON.stringify(this.bind_data.item_info,null,2));
      let ret = {};
      ret.item_uuid = this.bind_data.item_info.item_uuid;
      ret.item_key = this.bind_data.item_info.item_key;
      ret.item_type = this.bind_data.item_info.item_type;
      ret.item_name = this.bind_data.item_info.item_name;
      ret.item_show = this.state_data.show;
      ret.item_data = this.bind_data.item_data;
      //console.log("ret.item_data:"+this.bind_data.item_data+"@");
      if( this.bind_data.item_info.item_type == "number" ){
        ret.item_unit_name = this.bind_data.item_info.item_unit_name;
      }
      if( this.bind_data.item_info.item_type == "table" ){
        ret.item_data = {};
        ret.item_data.table_fields = this.bind_data.table_fields;
        ret.item_data.table_items = this.bind_data.table_items;
      }
      if( this.bind_data.item_info.item_type == "label" ){
        ret.item_data = {};
        ret.item_data.item_markdown_checked = this.bind_data.item_info.item_markdown_checked;
        ret.item_data.item_display_checked = this.bind_data.item_info.item_display_checked;
        ret.item_data.item_description = this.bind_data.item_info.item_description;
      }
      if( this.bind_data.item_info.item_type == "url" ){
        ret.item_link_when_view = this.bind_data.item_info.item_link_when_view;
      }
      ret.item_state = this.get_state_item(this.bind_data.item_data,this.bind_data.item_info);
      return ret;
    },
    erase_item_data: function( index ){
      if( index === undefined || index === null ){
        this.bind_data.item_data = "";
      } else {
        this.bind_data.item_data[index] = "";
      }
      this.state_data.state = false;
      this.$nextTick(function() {
        this.state_data.state = this.bind_data.item_info.item_state;
      });
      this.$emit('input',this.get_ret_data());
    },
    number_data_set: function(){
      if( this.bind_data.item_data.length > 0 ){
        if( Number(this.bind_data.item_data) < Number(this.bind_data.item_info.item_limit_min) ){
          this.bind_data.item_data = this.bind_data.item_info.item_limit_min;
        }
      }
      if( this.bind_data.item_data.length > 0 ){
        if( Number(this.bind_data.item_data) > Number(this.bind_data.item_info.item_limit_max) ){
          this.bind_data.item_data = this.bind_data.item_info.item_limit_max;
        }
      }
    },
    table_data_set: function(){
      this.bind_data.table_items = [];
      this.bind_data.table_fields = [];
      this.bind_data.table_fields.push(this.local_data.table_field);
      if( this.bind_data.item_info.table_cols !== undefined ){
        for( let i=0;i<this.bind_data.item_info.table_cols.length;i++ ){
          //console.log(this.bind_data.item_info.table_cols[i].text);
          let field = {};
          field.key = this.bind_data.item_info.table_cols[i].text;
          field.label = this.bind_data.item_info.table_cols[i].text;
          this.bind_data.table_fields.push(field);
        }
        for( let j=0;j<this.bind_data.item_info.table_rows.length;j++ ){
          let row = {}
          for( let i=0;i<this.bind_data.table_fields.length;i++ ){
            if( i==0 ){
              row[this.bind_data.table_fields[i].key] = this.bind_data.item_info.table_rows[j].value;
            } else {
              if( this.bind_data.item_data === null ){
                row[this.bind_data.table_fields[i].key] = "";
              } else {
                //console.log(JSON.stringify(this.bind_data.item_data.table_items[j]));
                row[this.bind_data.table_fields[i].key] = this.bind_data.item_data.table_items[j][this.bind_data.table_fields[i].key];
                //row[this.bind_data.table_fields[i].key] = "";
              }
            }
          }
          this.bind_data.table_items.push(row);
        }
      }
      //console.log(JSON.stringify(this.bind_data.table_items,null,2));
    },
    table_data_change: function( data ){
      //console.log("ItemInputter:table_data_change:bind_data"+JSON.stringify(this.bind_data))
      //console.log("ItemInputter:table_data_change:data:"+JSON.stringify(data))
      data.item[data.field.key]=data.value;
      this.$emit('input',this.get_ret_data());
    },
    pulldown_data_set: function(){
      //console.log("pulldown_data_set="+JSON.stringify(this.list_info,null,2));
      if( Array.isArray(this.list_info) ){
        for( let i=0;i<this.list_info.length;i++ ){
          if( this.list_info[i].item_key == this.bind_data.item_info.item_key ){
            this.bind_data.pulldown_list = this.list_info[i].item_options;
            //console.log("pulldown_data_set3="+JSON.stringify(this.bind_data.pulldown_list,null,2));
            this.bind_data.item_data = this.bind_data.pulldown_list[0].value;
          }
        }
      }
    },
    image_data_set: function(){
      if( this.bind_data.item_data !== null ){
        if( this.bind_data.item_data.length > 0 ){
          if( this.bind_data.item_data.startsWith("data:image/") ){
            this.bind_data.image_preview_src = this.bind_data.item_data;
          }
        }
      }
    },
    // 画像の選択
    item_image_input: async function(){

      if( this.bind_data.image_file === null ){
        return;
      }
      let image_base64 = await this.get_image_base64(this.bind_data.image_file);
      let image_data = await this.get_image_data(image_base64);
      this.bind_data.image_preview_src = this.resize_image(image_data);
      this.bind_data.item_data = this.bind_data.image_preview_src;
    },
    item_image_clear: function(){
      this.bind_data.image_file = null;
      this.bind_data.image_preview_src = null;
      this.bind_data.item_data = null;
    },
    resize_image: function(image_data){
      let bounds = {};
      bounds.width = this.bind_data.item_info.item_width;
      bounds.height = this.bind_data.item_info.item_height;
      let rect = this.fit_rect_into_bounds(image_data,bounds)
      //console.log("rect="+JSON.stringify(rect));
      //console.log("bounds="+JSON.stringify(bounds));
      let canvas = document.createElement('canvas');
      canvas.width = rect.width;
      canvas.height = rect.height;
      let ctx = canvas.getContext('2d');
      ctx.drawImage(image_data, 0, 0, rect.width, rect.height);
      return canvas.toDataURL('image/png');
    },
    get_image_base64: function(image_file) {
      return new Promise((resolve, reject) => {
          let reader = new FileReader();
          reader.onload = () => { resolve(reader.result); };
          reader.onerror = () => { reject(reader.error); };
          reader.readAsDataURL(image_file);
      });
    },
    get_image_data: function(image_base64){
      return new Promise((resolve, reject) => {
          let image = new Image();
          image.onload = () => { resolve(image); };
          image.onerror = () => { reject(image); };
          image.src = image_base64;
      });
    },
    fit_rect_into_bounds: function(rect, bounds) {
      let rectRatio = rect.width / rect.height;
      let boundsRatio = bounds.width / bounds.height;
      let newDimensions = {};
      if(rectRatio > boundsRatio) {
        newDimensions.width = bounds.width;
        newDimensions.height = rect.height * (bounds.width / rect.width);
      }
      else {
        newDimensions.width = rect.width * (bounds.height / rect.height);
        newDimensions.height = bounds.height;
      }
      return newDimensions;
    },
    is_allowed_type: function( item_data, item_info ){
      if( item_data === "" || item_data === null ){
        return null;
      }
      if( item_info.item_allowed_type.toLowerCase() == "all" ){
        return true;
      }
      if( item_info.item_allowed_type.toLowerCase() == "alpha" ){
        return validator.isAlpha(item_data);
      }
      if( item_info.item_allowed_type.toLowerCase() == "numeric" ){
        return validator.isNumeric(item_data);
      }
      if( item_info.item_allowed_type.toLowerCase() == "alphanumeric" ){
        return validator.isAlphanumeric(item_data);
      }
      if( item_info.item_allowed_type.toLowerCase() == "ascii" ){
        return validator.isAscii(item_data);
      }
      return false;
    },
    is_password_type: function( item_data, item_info ){
      if( item_data === "" || item_data === null ){
        return null;
      }
      //item_selected: ["lowercase","uppercase","numbers","symbols"], 
      let options = {};
      options.minLength = item_info.item_length;  //最小文字数
      if( item_info.item_selected.includes("lowercase") ){
        options.minLowercase = 1;
      } else {
        options.minLowercase = 0;
      }
      if( item_info.item_selected.includes("uppercase") ){
        options.minUppercase = 1;
      } else {
        options.minUppercase = 0;
      }
      if( item_info.item_selected.includes("numbers") ){
        options.minNumbers = 1;
      } else {
        options.minNumbers = 0;
      }
      if( item_info.item_selected.includes("symbols") ){
        options.minSymbols = 1;
      } else {
        options.minSymbols = 0;
      }
      return validator.isStrongPassword(item_data, options);
    },
    // パスワード（eye）アイコン パスワードのマスク有無
    password_icon: function(){
      if( this.state_data.password_type == "password" ){
        this.state_data.password_type = "text";
        this.state_data.password_icon = "eye-slash";
      } else {
        this.state_data.password_type = "password";
        this.state_data.password_icon = "eye";
      }
    },
    is_email_type: function( item_data, item_info ){
      if( item_data === "" || item_data === null ){
        return null;
      }
      if( item_info.item_allow_multiple == "checked" ){
        let matx = item_data.split(",");
        let ret = true;
        for( let i=0;i<matx.length;i++ ){
          if( !validator.isEmail(matx[i].trim()) ){
            ret = false;
          }
        }
        return ret;
      } else {
        return validator.isEmail(item_data);
      }
    },
    is_url_type: function( item_data ){
      if( item_data === "" || item_data === null ){
        return null;
      }
      return validator.isURL(item_data);
    },
    is_toggle_type: function( item_data, item_info ){
      if( item_data === "" || item_data === null ){
        return null;
      }
      if( item_data == item_info.item_checked_value ){
        return true;
      }
      return null;
    },
    is_telephone_type: function( item_data, item_info ){
      // let ret1 = this.is_array_type( item_data );
      // if( ret1 ){
      //   for( let i=0;i<item_data.length;i++ ){
      //     let ret2 = this.is_allowed_type(item_data[i],item_info);
      //     if( !ret2 ){
      //       return ret2;
      //     }
      //   }
      //   return true;
      // } else {
      //   return ret1;
      // }
      if( item_data === "" || item_data === null ){
        return null;
      }
      if( item_info.item_hyphen && !item_info.item_international ){
        // ハイフンあり
        if( item_data.match(/^0\d{1,4}-\d{1,4}-\d{1,5}$/) ) {
          return true;
        } else {
          return false;
        }
      } else if( !item_info.item_hyphen && !item_info.item_international ){
        // ハイフンなし
        if( item_data.match(/^0\d{9,14}$/) ) {
          return true;
        } else {
          return false;
        }
      } else if( item_info.item_hyphen && item_info.item_international ){
        // 国際電話識別番号対応ハイフンあり
        if( item_data.match(/^[+][-0-9]{10,18}$/) ) {
          return true;
        } else {
          return false;
        }
      } else { //if( !item_info.item_hyphen && item_info.item_international ){
        // 国際電話識別番号対応国際ハイフンなし
        if( item_data.match(/^[+][0-9]{10,14}$/) ) {
          return true;
        } else {
          return false;
        }
      }
    },
    is_array_type: function( item_data ){
      if( !Array.isArray(item_data) ){
        return null;
      }
      if( item_data.length > 0 ){
        for( let i=0;i<item_data.length;i++ ){
          if( item_data[i] == "" || item_data[i] == null ){
            return null;
          }
        }
        return true;
      } else {
        return null;
      }
    },
    is_string_type: function( item_data ){
      if( item_data == "" || item_data == null ){
        return null;
      }
      return true;
    },
    is_show: function(){
      let rect = this.$el.getBoundingClientRect();
      //console.log("is_show="+JSON.stringify(rect));
      if( rect.height > 0 ){
        this.state_data.show = true;
      } else {
        this.state_data.show = false;
        // 初期化
        if( this.bind_data.item_data !== null ){
          this.init_item_data();
        }
      }
      setTimeout(()=>{
				this.is_show();
			}, 1000);
    },
    init_item_data: function(){
      if( this.bind_data.item_info.item_type == "text" || this.bind_data.item_info.item_type == "texts" ||
          this.bind_data.item_info.item_type == "number" || this.bind_data.item_info.item_type == "telephone" || 
          this.bind_data.item_info.item_type == "date" || this.bind_data.item_info.item_type == "time" ){
        this.bind_data.item_data = "";
      }
      if( this.bind_data.item_info.item_type == "radio" || 
          ( this.bind_data.item_info.item_type == "pulldown" && this.bind_data.item_info.item_method == "static" )){
        if( this.bind_data.item_info.item_options === undefined ){
          this.bind_data.item_data = "";
        } else {
          if( this.bind_data.item_info.item_options.length ){
            for( let i=0;i<this.bind_data.item_info.item_options.length;i++ ){
              if( this.bind_data.item_info.item_options[i].init ){
                this.bind_data.item_data = this.bind_data.item_info.item_options[i].value;
              }
            }
          } else {
            this.bind_data.item_data = "";
          }
        }
      }
      if( this.bind_data.item_info.item_type == "checkbox" ){
        this.bind_data.item_data = [];
      }
      if( this.bind_data.item_info.item_type == "toggle" ){
        this.bind_data.item_data = this.bind_data.item_info.item_unchecked_value;
      }
      if( this.bind_data.item_info.item_type == "name" || this.bind_data.item_info.item_type == "datetime" ){
        this.bind_data.item_data = ["",""];
      }
      // if( this.bind_data.item_info.item_type == "telephone" ){
      //   this.bind_data.item_data = ["","",""];
      // }
    }
  }
};
</script>
<style scoped>
.item-inputter {
  text-align: left;
  padding: 4px;
}
.item-curosr {
  cursor:pointer;
}
.long_text {
  white-space: break-spaces;
}
</style>