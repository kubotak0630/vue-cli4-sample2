<template>
  <!-- ファイル選択のDropボックスのコアコンポーネント -->
  <!-- ファイルセレクト及びファイルのドロップに対応 -->
  <!-- ファイルの読み込みが完了したら"emit-freaddone-event"を親に通知する(ファイルの中身も通知) -->
  <div>
    <div v-bind:id="id_str">
      <div
        class="btn-defclass"
        v-bind:class="{ dropover: isActive, 'fileselect-enable': isEnable }"
        v-on:click="on_clickFileSelectBtn"
        v-on:dragover="on_dragover"
        v-on:dragleave="on_dragleave"
        v-on:drop="on_drop"
      >{{ text_label }}</div>
      <input type="file" class="fdummy-input" v-on:change="on_changeFileName" />
      <input
        type="text"
        class="filename-box"
        v-bind:value="fname"
        placeholder="ファイルが選択されていません"
        readonly
      />
    </div>
  </div>
</template>

<script>
export default {
  name: "FileDropBox",
  props: {
    text_label: String, ////BOXに表示する文字列
    //Instanceが一つなら設定する必要がない
    id_str: {
      type: String, //CSSのIDを外から設定する。methods on_clickFileSelectBtnでIDセレクタでqueryしていためIDが必要
      default: "fdrop-box"
    },
    isEnable: {
      type: Boolean,
      default: true
    }
  },
  data: function() {
    return {
      fname: "",
      isActive: false //クラスの表示切替
    };
  },
  methods: {
    on_clickFileSelectBtn: function() {
      if (this.isEnable) {
        document.querySelector(`#${this.id_str} .fdummy-input`).click();
      }
    },
    on_changeFileName: function(evt) {
      let file = evt.target.files;

      //inputテキストボックスへのファイル名表示
      this.fname = file[0].name;

      //FileReaderを作成してファイルの読み込み
      this.readFile_withFileReader(file[0]);
    },
    on_dragover: function(evt) {
      if (this.isEnable) {
        evt.preventDefault();
        evt.dataTransfer.dropEffect = "copy";
        this.isActive = true;
        console.log("on_dragover");
      }
    },
    on_dragleave: function(evt) {
      console.log("on_dragleave");
      this.isActive = false;
    },
    on_drop: function(evt) {
      evt.preventDefault();
      this.isActive = false;

      // evt.dataTransfer.filesはFileListオブジェクト
      const fileList = evt.dataTransfer.files;

      // Array.prototypeメソッドを使えるようにするため、FileListをArrayに変換する
      const fileArray = Array.from(fileList);

      //inputテキストボックスへのファイル名表示
      this.fname = fileArray[0].name;

      //FileReaderを作成してファイルの読み込み
      this.readFile_withFileReader(fileArray[0]);
    },
    //FileReaderの作成とファイルの読み込み
    readFile_withFileReader: function(fileObj) {
      let reader = new FileReader();
      reader.readAsText(fileObj);

      // readAsText() が終了後に呼び出される
      reader.onload = evt => {
        console.log("call FileReader onload");
        // this.fileContent = evt.target.result;

        //ファイル読み込み完了を親に通知
        // evt.target.result: String
        this.$emit("emit-freaddone-event", evt.target.result);
      };
    }
  }
};
</script>

<style lang="scss" scoped>
.btn-defclass {
  background-color: #00336d;
  color: gray;
  // cursor: default;
  padding: 15px 30px;
  /* margin: 0 20px 40px 0; */
  margin-right: 20px;
  height: 40px;
  width: 350px;
  line-height: 40px;
  vertical-align: middle;
  // box-shadow: 3px 3px 4px gray;

  display: inline-block;
}

.fileselect-enable {
  cursor: pointer;
  color: #fff;
  box-shadow: 3px 3px 4px gray;
}

/* 非表示設定 */
.fdummy-input {
  margin: 0 0 40px 0;
  display: none;
}

.filename-box {
  width: 200px;
  /* position: relative; */
  /* top: -20px; */
}

/* ファイルドロップの時にマウスカーソルがボックスに入った時に色を変える */
.dropover {
  background-color: #4d9be8;
  color: yellow;
}
</style>
