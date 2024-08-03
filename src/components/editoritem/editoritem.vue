<template> 
  <div id="wangeditor">
    <div ref="editorElem" style="text-align: left;"></div>
  </div>
</template> 
 <script>

//引入编辑器
import E from "wangeditor";
export default {
  name: "editorElem",
  data() {
    return {
      baseurl: window.SITE_CONFIG.baseUrl,
      editor: null,
      editorContent: '',
      imgs: [],
      info: '',
    };
  },
  // 接收父组件的方法
  props: ["catchData", "content", 'describe'],
  watch: {
    //和父组件的值绑定
    editorContent(v) {
      console.log(v, '?');
      if (v !== this.editor.txt.html()) {
        this.editor.txt.html(v)
      }
      this.editor.selection.moveCursor(this.editor.$textElem.elems[0], false);
    },
    content(v) {
      console.log(v, 'v');
      if (v !== this.editor.txt.html())
        this.editor.txt.html(v)
      this.editor.selection.moveCursor(this.editor.$textElem.elems[0], false);
    },
    /*
    父组件传回的html字符串，vue应用创建顺序先mount(挂载)，之后再prop传值，
    所以需要用到watch监视describe，才能拿到describe的值，不然describe为空或者默认值
    */
    describe(newV) {
      this.editor.txt.html(this.describe)//父组件传入的html字符串describe写入富文本
      this.editorContent = this.describe//赋值给editorContent,防止不修改时保存时给后端发送的是空字符？
    }
  },
  mounted() {
    this.editor = new E(this.$refs.editorElem);//创建editorElem对象
    //监视改变的钩子
    this.editor.config.onchange = (html) => {
      this.editorContent = html;//将改变后的字符复制给data里面的editorContent
      this.catchData(this.editorContent);// 然后把这个html通过catchData的方法传入父组件
    };

    this.editor.config.uploadImgServer = this.baseurl + "/app/uploadFiles/fuUpload";//你上传富文本图片是的接口和域名(IP地址)
    this.editor.config.uploadFileName = "file";//和后端约定好的上传文件名
    //配置富文本菜单
    this.editor.config.menus = [
      // 菜单配置
      "head", // 标题
      "bold", // 粗体
      "fontSize", // 字号
      "fontName", // 字体
      "italic", // 斜体
      "underline", // 下划线
      "strikeThrough", // 删除线
      "foreColor", // 文字颜色
      "backColor", // 背景颜色
      "link", // 插入链接
      "list", // 列表
      "justify", // 对齐方式
      "quote", // 引用
      "emoticon", // 表情
      "image", // 插入图片
      "table", // 表格
      "code", // 插入代码
      "undo", // 撤销
      "redo", // 重复
    ];
    //上传图片的一大堆钩子
    this.editor.config.uploadImgHooks = {
      before: function (xhr, editor, files) {
        // console.log('上传图片前before',  xhr, editor, files);
        // 图片上传之前触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，files 是选择的图片文件
        // 如果返回的结果是 {prevent: true, msg: 'xxxx'} 则表示用户放弃上传
        // return {
        //   prevent: true,
        //   msg: '放弃上传'
        // }
      },
      success: function (xhr, editor, result) {
        // console.log('上传图片成功success', xhr, editor, result);
        // 图片上传并返回结果，图片插入成功之后触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
        this.imgUrl = Object.values(result.data).toString();
      },
      fail: function (xhr, editor, result) {
        console.log('上传图片失败fail', xhr, editor, result);
        // 图片上传并返回结果，但图片插入错误时触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
      },
      error: function (xhr, editor) {
        console.log('上传图片出错error', xhr, editor);
        // 图片上传出错时触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象
      },
      timeout: function (xhr, editor) {
        console.log('上传图片超市timeout', xhr, editor);
        // 图片上传超时时触发
        // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象
      },

      // 如果服务器端返回的不是 {errno:0, data: [...]} 这种格式，可使用该配置
      // （但是，服务器端返回的必须是一个 JSON 格式字符串！！！否则会报错）
      customInsert: (insertImg, res, editor) => {
        // console.log('customInsert', res);
        if (res && res.code == 0) {
          this.$message.success('上传成功')
          this.imgs.push(res.fileName)
        }
        // 图片上传并返回结果，自定义插入图片的事件（而不是编辑器自动插入图片！！！）
        // insertImg 是插入图片的函数，editor 是编辑器对象，result 是服务器端返回的结果
        // 举例：假如上传图片成功后，服务器端返回的是 {url:'....'} 这种格式，即可这样插入图片：
        // let url = Object.values(result.data); // result.data就是服务器返回的图片名字和链接
        // JSON.stringify(url); // 在这里转成JSON格式
        insertImg(this.baseurl + '/super/goods/' + res.fileName);
        // result 必须是一个 JSON 格式字符串！！！否则报错
      },
    };

    this.editor.create(); // 创建富文本实例
    //父组件传出内容为空时写的默认内容，其实没什么用
    if (!this.content) {
      this.editor.txt.html("请编辑内容1");
    }
  },
};
</script>
