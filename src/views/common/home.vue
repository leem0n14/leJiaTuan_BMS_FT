<template>
  <div class="mod-home">
    <h1>乐驾团后台管理系统</h1>
    <img src="../../assets/img/ljtBg.jpg" alt="" class="bg" style="width:100%">

  </div>
</template>

<script>
import EditorBar from "@/components/editoritem/editoritem"
export default {
  data() {
    return {


    }
  },
  components: {
    EditorBar
  },
  methods: {

  }
}
</script>

<style>
.mod-home {
  position: relative;
  line-height: 1.5;
  /* background-color: #55aa33; */
}

.logo {
  position: absolute;
  top: 0;
  right: 0;
  width: 220px;
  height: 75px;
  border-radius: 10px;
  background-color: #17b3a3;
}
</style>
