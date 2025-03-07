<template>
  <div
    style="max-height: 50vh;overflow-y: auto;"
    :style="commonStyle"
  >
    <Editor
      v-model="content"
      style="width: 100%;height: 100%"
      :init="init"
    />
  </div>
</template>

<script>
import tinymce from 'tinymce/tinymce' // tinymce默认hidden，不引入不显示
import Editor from '@tinymce/tinymce-vue'
import 'tinymce/themes/silver/theme' // 编辑器主题
import 'tinymce/icons/default' // 引入编辑器图标icon，不引入则不显示对应图标
// 引入编辑器插件（基本免费插件都在这儿了）
import 'tinymce/plugins/advlist' // 高级列表
import 'tinymce/plugins/autolink' // 自动链接
import 'tinymce/plugins/link' // 超链接
import 'tinymce/plugins/image' // 插入编辑图片
import 'tinymce/plugins/lists' // 列表插件
import 'tinymce/plugins/charmap' // 特殊字符
import 'tinymce/plugins/media' // 插入编辑媒体
import 'tinymce/plugins/wordcount' // 字数统计
import 'tinymce/plugins/table' // 表格
import 'tinymce/plugins/contextmenu' // contextmenu
import 'tinymce/plugins/directionality'
import 'tinymce/plugins/nonbreaking'
import 'tinymce/plugins/pagebreak'
import { imgUrlTrans } from '@/components/canvas/utils/utils'
import { mapState } from 'vuex'
// 编辑器引入
export default {
  name: 'RemarkEditor',
  components: {
    Editor
  },
  props: {
    remark: {
      type: String,
      required: true
    },
    background: {
      type: String,
      required: false
    },
    showTable: {
      type: Boolean,
      default: true
    },
    showMedia: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      content: '',
      // 初始化配置
      init: {
        auto_focus: true,
        toolbar_items_size: 'small',
        language_url: '/tinymce/langs/zh_CN.js', // 汉化路径是自定义的，一般放在public或static里面
        language: 'zh_CN',
        skin_url: '/tinymce/skins/ui/oxide', // 皮肤
        content_css: '/tinymce/skins/content/default/content.css',
        plugins: 'advlist autolink link image lists charmap  media wordcount table contextmenu directionality pagebreak', // 插件
        // 工具栏
        toolbar: 'undo redo |fontselect fontsizeselect |forecolor backcolor bold italic |underline strikethrough link| formatselect |' +
          'alignleft aligncenter alignright | bullist numlist |' +
          ' blockquote subscript superscript removeformat | table image media | fullscreen ' +
          '| bdmap indent2em lineheight formatpainter axupimgs',
        toolbar_location: '/',
        toolbar_mode: 'wrap',
        font_formats: '微软雅黑=Microsoft YaHei;宋体=SimSun;黑体=SimHei;仿宋=FangSong;华文黑体=STHeiti;华文楷体=STKaiti;华文宋体=STSong;华文仿宋=STFangsong;Andale Mono=andale mono,times;Arial=arial,helvetica,sans-serif;Arial Black=arial black,avant garde;Book Antiqua=book antiqua,palatino;Comic Sans MS=comic sans ms,sans-serif;Courier New=courier new,courier;Georgia=georgia,palatino;Helvetica=helvetica;Impact=impact,chicago;Symbol=symbol;Tahoma=tahoma,arial,helvetica,sans-serif;Terminal=terminal,monaco;Times New Roman=times new roman,times;Trebuchet MS=trebuchet ms,geneva;Verdana=verdana,geneva;Webdings=webdings;Wingdings=wingdings',
        fontsize_formats: '12px 14px 16px 18px 20px 22px 24px 28px 32px 36px 48px 56px 72px', // 字体大小
        menubar: false,
        branding: false,
        height: 300,
        min_height: 200,
        max_height: 500,
        elementpath: false,
        statusbar: false,
        convert_urls: false
      }
    }
  },
  computed: {
    customStyle() {
      let style = {}
      if (this.canvasStyleData.openCommonStyle) {
        if (this.canvasStyleData.panel.backgroundType === 'image' && this.canvasStyleData.panel.imageUrl) {
          style = {
            background: `url(${imgUrlTrans(this.canvasStyleData.panel.imageUrl)}) no-repeat`,
            ...style
          }
        } else if (this.canvasStyleData.panel.backgroundType === 'color') {
          style = {
            background: this.canvasStyleData.panel.color,
            ...style
          }
        }
      }
      if (!style.background) {
        style.background = '#FFFFFF'
      }
      return style
    },
    commonStyle() {
      return {
        background: this.background
      }
    },
    ...
    mapState([
      'curComponent',
      'canvasStyleData'
    ])
  },
  watch: {
    content: {
      handler(newValue) {
        this.$emit('onRemarkChange', newValue)
      }
    }
  },
  created() {
    if (!this.showTable) {
      this.init.plugins = this.init.plugins.replace(' table', '')
    }
    if (!this.showMedia) {
      this.init.plugins = this.init.plugins.replace(' media', '')
    }
  },
  mounted() {
    this.content = this.remark
    tinymce.init({})
  }
}
</script>

<style scoped>

::v-deep .tox-edit-area__iframe {
  background: rgba(255, 255, 255, 0) !important;
}

</style>
