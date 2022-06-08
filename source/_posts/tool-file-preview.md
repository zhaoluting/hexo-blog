---
title: vue文件预览
date: 2022-06-07 16:19:34
tags: tool
categories: 工具
---

- 一般外网可访问且内容不敏感的话，可以直接使用微软的在线预览，体验也比较好；如果是本地、内网文件或者内容比较敏感的，还是比较建议使用插件来转换显示；
- PDF可以直接新窗口打开，用浏览器自带的预览功能；如果需要自定义样式，则可以使用[vue-pdf](https://www.npmjs.com/package/vue-pdf)；
- excel推荐使用[xlsx](https://www.npmjs.com/package/xlsx)，目前使用起来没什么大问题；
- word使用最多的是[mammoth.js](https://www.npmjs.com/package/mammoth)，原理是将源文档转换为HTML，但是只支持.docx，样式也没法完全还原，注重还原度的话可以先转成PDF再预览；

<!--more-->

> 下面是示例代码，挖个坑，以后填

## 在线office
```javascript
filePreview(fileUrl) {
  if (/\.(ppt|pptx|doc|docx|xls|xlsx)$/.test(fileUrl)) {
    const officePath = 'https://view.officeapps.live.com/op/view.aspx?src=';
    window.open(officePath + fileUrl, '_blank')
  }
}
```

## pdf
文档：[vue-pdf](https://www.npmjs.com/package/vue-pdf)
遗留问题：多页模式下，页数过多渲染会很慢，所以设置了分页；目录的锚点，在小屏下会偏移，issues上也有人碰到了同样的问题，等作者解决；
```javascript
<template>
  <div :ref="refname" class="pdf-preview" v-loading="loading" element-loading-text="加载中">
    <!-- pdf预览 -->
    <el-radio-group :disabled="pdfData.pageCount <= 1" size="small" v-model="pdfData.type" class="pagination-radio"
      @change="changePdfType">
      <el-radio-button :label="0">
        <SvgIcon className="radio-icon" iconClass="icon-fenye"></SvgIcon>
      </el-radio-button>
      <el-radio-button :label="1">
        <SvgIcon className="radio-icon" iconClass="icon-chaifenyemian"></SvgIcon>
      </el-radio-button>
    </el-radio-group>
    <!-- 可滚动 -->
    <template v-if="pdfData.type == 1">
      <el-pagination class="pdf-pagination" @current-change="changeRollingPdfPage"
        :current-page.sync="rollingPdf.currentPage" :total="pdfData.pageCount" :page-size="rollingPdf.size"
        :pager-count="5" hide-on-single-page background layout="jumper, prev, pager, next"></el-pagination>
      <div class="preview-content preview-pdf-rolling" v-loading="rollingPdf.loading" element-loading-text="加载中">
        <pdf class="pdf-rolling-item" :class="'pdf-rolling-item-' + item.page" @page-loaded="rollingPdfLoad"
          @link-clicked="clickPdfLink" v-for="item in rollingPdf.nowList" :key="item.page" :src="pdfData.src"
          :page="item.page"></pdf>
      </div>
    </template>
    <!-- 分页 -->
    <template v-if="pdfData.type != 1">
      <el-pagination class="pdf-pagination" @current-change="changePdfPage" :current-page.sync="pdfData.currentPage"
        :total="pdfData.pageCount" :page-size="1" layout="jumper, prev, pager, next" :pager-count="5"
        hide-on-single-page background></el-pagination>
      <div class="preview-content preview-pdf-paged">
        <pdf :src="pdfData.src" :page="pdfData.currentPage" @num-pages="pdfData.pageCount=$event"
          @link-clicked="clickPdfLink"></pdf>
      </div>
    </template>
  </div>
</template>

<script>
  import SvgIcon from "@_c/SvgIcon";
  import pdf from "vue-pdf"
  import { handelTool } from "@/js/utils";

  export default {
    name: "officePdfPreview",
    components: {
      pdf,
      SvgIcon,
    },
    props: {
      refname: {
        type: String,
        default: 'officePreview'
      },
      file: {
        type: Object,
        default: () => {
          return {}
        }
      },
      // 基础链接
      baseURL: {
        type: String,
        default: ''
      },
    },
    data() {
      return {
        loading: false,
        // pdf数据
        pdfData: {
          // 当前页
          currentPage: 1,
          // pdf文件总页数
          pageCount: 0,
          //
          src: "",
          type: 1,
        },
        rollingPdf: {
          src: '',
          size: 5,
          currentPage: 1,
          fullList: [],
          nowList: [],
          loadingItem: 0,
          loading: false,
        },
      }
    },
    watch: {
      file: {
        handler(newValue) {
          this.$nextTick(() => {
            this.initPdf()
          })
        }
      }
    },
    mounted() {
      this.initPdf()
    },
    methods: {
      setPdfCount(value) {
        this.pdfData.pageCount = value;
        this.changePdfType(1)
      },
      // 切换PDF查看类型为分页、滚动
      changePdfType(type) {
        this.pdfData.type = type;
        if (this.pdfData.type == 1) {
          this.rollingPdf = this.$options.data().rollingPdf;
          for (let index = 1; index <= this.pdfData.pageCount; index++) {
            this.rollingPdf.fullList.push({ page: index });
          }
          this.changeRollingPdfPage(1);
        } else {
          this.changePdfPage(1);
        }
      },
      // 切换pdf显示页
      changeRollingPdfPage(page) {
        this.rollingPdf.loadingItem = 0;
        this.rollingPdf.loading = true;
        if (typeof page == 'number') {
          this.rollingPdf.currentPage = page;
          if (page >= 1 && page <= this.pdfData.pageCount) {
            this.$refs[this.refname].querySelector(".preview-pdf-rolling").scrollTop = 0;
            this.rollingPdf.nowList = this.rollingPdf.fullList.slice((page - 1) * this.rollingPdf.size, page * this.rollingPdf.size)
          }
        }
      },
      // 点击PDF内部的目录
      clickPdfLink(value) {
        if (typeof value == 'number') {
          if (this.pdfData.type == 1) {
            this.rollingPdf.scrollTo = value;
            const toPage = Math.ceil(value / this.rollingPdf.size);
            if (toPage == this.rollingPdf.currentPage) {
              const rollingItem = this.$refs[this.refname].querySelector('.pdf-rolling-item-' + value);
              rollingItem && handelTool.scrollInto(rollingItem);
            } else {
              this.changeRollingPdfPage(toPage);
            }
          } else {
            this.changePdfPage(value)
          }
        }
      },
      // 滚动类型pdf加载
      rollingPdfLoad() {
        this.rollingPdf.loadingItem++;
        this.rollingPdf.loading = this.rollingPdf.loadingItem < this.rollingPdf.nowList.length;
        if (this.rollingPdf.scrollTo && !this.rollingPdf.loading) {
          setTimeout(() => {
            const rollingItem = this.$refs[this.refname].querySelector('.pdf-rolling-item-' + this.rollingPdf.scrollTo);
            if (rollingItem) {
              this.rollingPdf.scrollTo = null;
              handelTool.scrollInto(rollingItem);
            }
          }, 800);
        }
      },
      /**
       * 切换pdf显示页
       * @param command
       */
      changePdfPage(value) {
        if (typeof value == 'number') {
          this.pdfData.currentPage = value;
          this.$nextTick(() => {
            let pdfWarpDom = this.$refs[this.refname].querySelector(".preview-pdf-paged")
            pdfWarpDom && (pdfWarpDom.scrollTop = 0);
          })
        }
      },
      /**
       * pdf预览
       */
      initPdf() {
        if (this.loading || !this.file.url) return;
        this.loading = true;
        this.pdfData.type = 0;
        // this.getNumPages();
        try {
          // 加载
          let _this = this
          const xhr = new XMLHttpRequest();
          xhr.open('get', this.baseURL + this.file.url, true);
          xhr.responseType = 'blob';
          xhr.onload = function () {
            if (xhr.status === 200) {
              _this.pdfData.src = _this.getObjectURL(xhr.response);
            } else if (xhr.status === 404) {
              _this.$message.error("文件不存在")
            } else {
              _this.$message.error("读取服务器文档异常")
            }
            _this.loading = false;
          }
          xhr.onerror = function () {
            _this.$message.error("访问服务器异常，请检查访问链接是否正常")
            _this.loading = false;
          }
          xhr.send();
        } catch (e) {
          console.log(e)
        }
      },
      /**
       * 将返回的流数据转换为url
       * @param file 文件流
       */
      getObjectURL(file) {
        let url = null;
        if (window.createObjectURL !== undefined) { // basic
          url = window.createObjectURL(file);
        } else if (window.webkitURL !== undefined) { // webkit or chrome
          try {
            url = window.webkitURL.createObjectURL(file);
          } catch (error) {
          }
        } else if (window.URL !== undefined) { // mozilla(firefox)
          try {
            url = window.URL.createObjectURL(file);
          } catch (error) {
            console.log(error)
          }
        }
        return url;
      },
    },
  }
</script>

<style lang="scss">
  .pdf-preview {
    .preview-content {
      margin-top: 5px;
      border: 1px solid #eee;
      overflow: auto;

      .pdf-rolling-item {
        border-bottom: 7px solid #eee;
      }
    }

    .preview-pdf-paged>span,
    .preview-pdf-rolling>span {
      .annotationLayer {
        display: none !important;
      }
    }

    .pagination-radio {
      vertical-align: baseline;

      .el-radio-button__inner {
        padding: 6px 15px;
      }

      .radio-icon {
        font-size: 16px;
      }
    }

    .pdf-pagination {
      display: inline-block;
    }
  }
</style>
```

## excel
文档：[xlsx](https://www.npmjs.com/package/xlsx)
```javascript
<template>
  <div class="excel-preview" v-loading="loading" element-loading-text="加载中">
    <div class="tab">
      <el-radio-group size="small" v-model="excel.sheetNameActive" @change="getSheetNameTable">
        <el-radio-button v-for="(item,index) in excel.sheetNames" :key="index" :label="item"></el-radio-button>
      </el-radio-group>
    </div>
    <div class="preview-content">
      <div v-html="excel.SheetActiveTable" style="padding: 10px 15px"></div>
    </div>
  </div>
</template>

<script>
  import * as XLSX from 'xlsx';

  export default {
    name: "officeExcelPreview",
    components: {
    },
    props: {
      file: {
        type: Object,
        default: () => {
          return {}
        }
      },
      baseURL: {
        type: String,
        default: ''
      },
    },
    data() {
      return {
        loading: false,
        // Excel文档数据
        excel: {
          // 数据
          workbook: {},
          // 表名称集合
          sheetNames: [],
          // 激活项
          sheetNameActive: "",
          // 当前激活表格
          SheetActiveTable: ""
        }
      }
    },
    watch: {
      file: {
        handler(newValue) {
          this.$nextTick(() => {
            this.initExcelPreview()
          })
        }
      }
    },
    mounted() {
      this.initExcelPreview()
    },
    methods: {
      /**
       * 根据工作表名称获取数据
       * @param sheetName
       */
       getSheetNameTable(sheetName) {
        try {
          // 获取当前工作表的数据
          const worksheet = this.excel.workbook.Sheets[sheetName]
          // 转换为数据  1.json数据有些问题，2.如果是html那么样式需修改
          let htmlData = XLSX.utils.sheet_to_html(worksheet, { header: '', footer: '' })
          htmlData = htmlData === '' ? htmlData : htmlData.replace(/<table/, '<table class="default-table" border="1px solid #ccc" cellpadding="0" cellspacing="0"')
          // 第一行进行改颜色
          htmlData = htmlData === '' ? htmlData : htmlData.replace(/<tr/, '<tr style="background:#b4c9e8"')
          this.excel.SheetActiveTable = htmlData
        } catch (e) {
          // 如果工作表没有数据则到这里来处理
          this.excel.SheetActiveTable = '<h4 style="text-align: center">' + this.emptyTips + '</h4>'
        }
      },
      /**
       * 表格数据预览
       */
       initExcelPreview() {
        if (this.loading || !this.file.url) return;
        this.loading = true;
        try {
          let xhr = new XMLHttpRequest()
          xhr.open('get', this.baseURL + this.file.url, true)
          xhr.responseType = 'arraybuffer'
          const _this = this
          xhr.onload = function (e) {
            if (xhr.status === 200) {
              const data = new Uint8Array(xhr.response)
              const workbook = XLSX.read(data, { type: 'array' })
              const sheetNames = workbook.SheetNames // 工作表名称集合
              _this.excel.workbook = workbook
              _this.excel.sheetNames = sheetNames
              _this.excel.sheetNameActive = sheetNames[0]
              _this.getSheetNameTable(sheetNames[0])
            } else if (xhr.status === 404) {
              _this.$message.error("文件不存在")
            } else {
              _this.$message.error("读取服务器文档异常")
            }
            _this.loading = false;
          }
          xhr.onerror = function () {
            _this.$message.error("访问服务器异常，请检查访问链接是否正常")
            _this.loading = false;
          }
          xhr.send()
        } catch (e) {
          console.log(e)
        }

      },
    },
  }
</script>
```


## word
文档：[mammoth.js](https://www.npmjs.com/package/mammoth)
遗留问题：只支持.docx格式；样式没法一模一样还原；
```javascript
<template>
  <div v-loading="loading" element-loading-text="加载中">
    <div v-html="docHtml"></div>
  </div>
</template>

<script>
  import mammoth from 'mammoth'
  export default {
    name: "officeDocPreview",
    components: {
    },
    props: {
      file: {
        type: Object,
        default: () => {
          return {
            name: '',
            url: ''
          }
        }
      },
      baseURL: {
        type: String,
        default: ''
      },
    },
    data() {
      return {
        loading: false,
        docHtml: "",
        emptyTips: '暂无内容',
      }
    },
    watch: {
      file: {
        handler(newValue) {
          this.$nextTick(() => {
            this.initDocPreview()
          })
        }
      }
    },
    mounted() {
      this.initDocPreview()
    },
    methods: {
      /**
       * docx文件预览
       */
       initDocPreview() {
        // 请求文件流
        try {
          let _this = this
          if (_this.loading || !_this.file.url) return;
          _this.loading = true;
          const xhr = new XMLHttpRequest();
          xhr.open('get', this.baseURL + this.file.url, true);
          xhr.responseType = 'arraybuffer';
          xhr.onload = function () {
            if (xhr.status === 200) {
              mammoth.convertToHtml({
                arrayBuffer: new Uint8Array(xhr.response)
              }).then(function (resultObject) {
                _this.docHtml = resultObject.value
              })
            } else if (xhr.status === 404) {
              _this.$message.error("文件不存在")
            } else {
              _this.$message.error("读取服务器文档异常")
            }
            _this.loading = false;
          }
          xhr.onerror = function () {
            _this.$message.error("访问服务器异常，请检查访问链接是否正常")
            _this.loading = false;
          }
          xhr.send();
        } catch (e) {
          this.docHtml = '<h1 style="text-align: center">' + this.emptyTips + '</h1>'
        }
      },
    },
  }
</script>
```

## 集成弹窗
```javascript
<template>
  <el-dialog :visible="visible" :close-on-click-modal="false" append-to-body
    @close="handleClose" width="80%" :fullscreen="fullscreen" custom-class="office-preview-dialog">
    <template slot="title">
      <div class="single-ellipsis dialog-title">
        {{ file.name }}
        <div class="icon-btn">
          <el-tooltip effect="dark" content="刷新" placement="bottom">
            <i class="el-icon-refresh-right btn-item" @click="initOfficePreview"></i>
          </el-tooltip>
          <el-tooltip effect="dark" content="浏览器模式" placement="bottom" v-if="fileType==1">
            <i class="el-icon-data-board btn-item" @click="openPdfWindow"></i>
          </el-tooltip>
          <el-tooltip effect="dark" :content="fullscreen ? '退出全屏' : '全屏'" placement="bottom">
            <SvgIcon class="btn-item btn-screen" :iconClass="fullscreen ? 'icon-quxiaoquanping' : 'icon-quanping'" @click.native="fullscreen = !fullscreen"></SvgIcon>
          </el-tooltip>
        </div>
      </div>
    </template>
    <div class="office-preview">
      <!-- Pdf文档预览 -->
      <PdfPreview v-if="fileType == 1" :refname="refname + '_pdf'" :file="file" :baseURL="baseURL"></PdfPreview>
      <!-- Excel文档预览 -->
      <ExcelPreview v-if="fileType == 3" :file="file" :baseURL="baseURL"></ExcelPreview>
      <!-- word文档预览 -->
      <DocPreview v-if="fileType == 2" :file="file" :baseURL="baseURL"></DocPreview>
    </div>
    <Empty v-if="!fileType" description="仅支持pdf、docx、xlsx、xls文件预览"></Empty>
    <div class="bottom-btns">
      <el-button type="primary" plain size="small" @click="downloadFile">下载</el-button>
      <el-button size="small" @click="handleClose">关闭</el-button>
    </div>
  </el-dialog>
</template>

<script>
  import SvgIcon from "@_c/SvgIcon";
  import { systemApi } from '@/js/api/index';
  import PdfPreview from './pdfPreview.vue';
  import ExcelPreview from './excelPreview.vue';
  import DocPreview from './docPreview.vue';
  export default {
    name: "officePreview",
    components: {
      SvgIcon,
      PdfPreview,
      ExcelPreview,
      DocPreview
    },
    props: {
      refname: {
        type: String,
        default: 'officePreview'
      },
      visible: {
        type: Boolean,
        default: false
      },
      // file:{name:'',url:''}
      file: {
        type: Object,
        default: () => {
          return {}
        }
      },
      // 基础链接
      baseURL: {
        type: String,
        default: ''
      },
      downfile: {
        type: Object,
        default: () => {
          return {}
        }
      }
    },
    data() {
      return {
        // 全屏
        fullscreen: false,
        // 文件类型
        fileType: 0,
      }
    },
    watch: {
      file: {
        handler(newValue) { 
          this.initOfficePreview()
        }
      }
    },
    methods: {
      // 下载文件
      downloadFile() {
        if (this.downfile.filePath) {
          systemApi.downloadFile(this.downfile);
        } else if (this.file.filePath) {
          systemApi.downloadFile(this.file);
        }
      },
      handleClose() {
        this.fullscreen = false;
        this.$emit("update:visible", false)
      },
      /**
       * 获取文件类型
       * @return {null|number}
       */
      getFileType() {
        if (!this.file) {
          return null
        }
        let fileName = this.file.name.lastIndexOf(".");//取到文件名开始到最后一个点的长度
        let fileNameLength = this.file.name.length;//取到文件名长度
        let fileFormat = this.file.name.substring(fileName + 1, fileNameLength);
        if (fileFormat.toLowerCase() === "pdf") {
          return 1
        } else if (fileFormat.toLowerCase() === "docx" || fileFormat.toLowerCase() === "doc") {
          return 1
        } else if (fileFormat.toLowerCase() === "xlsx" || fileFormat.toLowerCase() === "xls") {
          return 3
        }
        return null
      },
      initOfficePreview() {
        this.fileType = null;
        this.$nextTick(() => {
          this.fileType = this.getFileType();
        })
      },
      openPdfWindow() {
        window.open(this.baseURL + this.file.url, '_blank')
      }
    },
  }
</script>

<style lang="scss">
.office-preview-dialog {
  &.is-fullscreen {
    .office-preview .preview-content {
      height: calc(100vh - 160px);
    }
  }
  .el-dialog__body {
    padding-top: 10px;
    padding-bottom: 15px;
  }
  .dialog-title {
    text-align: center;
    position: relative;
    padding: 0 55px;

    .icon-btn {
      position: absolute;
      top: -10px;
      font-size: 20px;
      right: 20px;
      cursor: pointer;
      .btn-screen{
        font-size: 22px;
      }
      .btn-item {
        cursor: pointer;
        margin: 0 4px;
        vertical-align: bottom;
        &:hover {
          color: #3C7BFF;
        }
      }
    }
  }
  .bottom-btns {
    text-align: right;
    margin-top: 10px;
  }
  .office-preview {
    .preview-content {
      margin-top: 5px;
      border: 1px solid #eee;
      overflow: auto;
      height: 65vh;
    }
  }

}
</style>
```