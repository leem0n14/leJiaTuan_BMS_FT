<template>
  <el-dialog :title="!dataForm.goodId ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible" width="90%"
    top="1vh">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" label-width="150px">
      <el-form-item label="显示顺序" prop="numbers">
        <!-- <el-input v-model="dataForm.numbers" placeholder="显示顺序"></el-input> -->
        <el-input-number v-model="dataForm.numbers" :min="1" label="显示顺序"></el-input-number>
      </el-form-item>
      <el-form-item label="商品班型名" prop="goodName">
        <el-input v-model="dataForm.goodName" placeholder="商品班型名" style="width: 300px;"></el-input>
      </el-form-item>
      <el-form-item label="商品首页照片(702×365)" prop="photo">
        <!-- <el-input v-model="dataForm.photo" placeholder="商品首页照片"></el-input> -->
        <el-upload class="avatar-uploader" :action="cdn + '/app/uploadFiles/photoUpload'" :on-success="upLoadSuccess"
          :before-upload="beforeUpLoad" :data="{ where: 'goods' }" :show-file-list="false">
          <img v-if="dataForm.photo" :src="cdn + '/super/goods/' + dataForm.photo" class="upLoad">
          <i v-else class="el-icon-plus boforeUpload"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="商品轮播图(750×394)" prop="photos">
        <el-upload ref="lbt" :action="cdn + '/app/uploadFiles/photoUpload'" list-type="picture-card" :on-preview="preview"
          :on-remove="remove" :on-success="lbtSuccess" :data="{ where: 'goods' }"
          :file-list="photosList.length != 0 ? photosList : upLoaded">
          <i class="el-icon-plus"></i>
        </el-upload>
        <el-dialog :visible.sync="dialogVisible">
          <img width="100%" :src="dialogImageUrl">
        </el-dialog>
      </el-form-item>
      <el-form-item label="标题" prop="goodTitle" style="width: 400px;">
        <el-input v-model="dataForm.goodTitle" placeholder="标题"></el-input>
      </el-form-item>
      <el-form-item label="商品详情描述" prop="goodDescribe">
        <!-- <el-input v-model="dataForm.goodDescribe" placeholder="商品详情描述"></el-input> -->
        <editor-bar :catchData="catchData" :content="editorContent" :describe="dataForm.goodDescribe"></editor-bar>
      </el-form-item>
      <el-form-item label="班型标签{课程车型}" prop="goodTags">
        <!-- <el-input v-model="dataForm.goodTags" placeholder="班型标签{课程车型}"></el-input> -->
        <el-select v-model="dataForm.goodTags" placeholder="请选择车型">
          <el-option v-for="item in carType" :key="item.id" :label="item.label" :value="item.label">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="商品班型价格" prop="goodPrice">
        <!-- <el-input v-model="dataForm.goodPrice" placeholder="商品班型价格"></el-input> -->
        <el-input-number v-model="dataForm.goodPrice" label="商品班型价格"></el-input-number>
      </el-form-item>
      <el-form-item label="商品数量" prop="goodNum">
        <!-- <el-input v-model="dataForm.goodNum" placeholder="商品数量"></el-input> -->
        <el-input-number v-model="dataForm.goodNum" label="商品数量"></el-input-number>
      </el-form-item>
      <el-form-item label="商品类型" prop="goodType">
        <!-- <el-input v-model="dataForm.goodType" placeholder="商品类型"></el-input> -->
        <el-radio-group v-model="dataForm.goodType">
          <el-radio label="普通">普通</el-radio>
          <el-radio label="秒杀">秒杀</el-radio>
          <el-radio label="拼团">拼团</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="上架状态" prop="goodStatu">
        <!-- <el-input v-model="dataForm.goodStatu" placeholder="上架状态{0 下架  1 上架 }"></el-input> -->
        <el-radio-group v-model="dataForm.goodStatu">
          <el-radio :label="0">下架</el-radio>
          <el-radio :label="1">上架</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="赠送积分" prop="goodPoint">
        <!-- <el-input v-model="dataForm.goodPoint" placeholder="赠送积分"></el-input> -->
        <el-input-number v-model="dataForm.goodPoint" label="赠送积分"></el-input-number>
      </el-form-item>
      <el-form-item label="是否是教练商品" prop="ifCoach">
        <!-- <el-input v-model="dataForm.ifCoach" placeholder="是否是教练商品"></el-input> -->
        <el-radio-group v-model="dataForm.ifCoach">
          <el-radio :label="0">否</el-radio>
          <el-radio :label="1">是</el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item label="所属教练" :prop="dataForm.ifCoach == 1 ? 'coachId' : ''" v-if="dataForm.ifCoach == 1">
        <!-- <el-input v-model="dataForm.coachId" placeholder="教练id"></el-input> -->
        <el-select v-model="dataForm.coachId" placeholder="请选择所属教练" @focus="coachFocus">
          <el-option v-for="item in coachList" :key="item.coachId" :label="item.coachName" :value="item.coachId">
          </el-option>
        </el-select>
      </el-form-item>
      <!-- <el-form-item label="所属驾校ID" prop="schoolId">
        <el-input v-model="dataForm.schoolId" placeholder="所属驾校ID"></el-input>
      </el-form-item> -->
      <el-form-item label="所属驾校名称" prop="schoolName">
        <!-- <el-input v-model="dataForm.schoolName" placeholder="所属驾校名称"></el-input> -->
        <el-select v-model="dataForm.schoolName" placeholder="请选择所属驾校" @change="chooseSchool(dataForm.schoolName)">
          <el-option v-for="item in schoolList" :key="item.schoolId" :label="item.schoolName" :value="item.schoolName">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="秒杀开始时间" :prop="dataForm.goodType == '秒杀' ? 'startTime' : ''" v-if="dataForm.goodType == '秒杀'">
        <!-- <el-input v-model="dataForm.endTime" placeholder="秒杀结束时间"></el-input> -->
        <el-date-picker v-model="dataForm.startTime" value-format="timestamp" type="datetime" placeholder="秒杀开始时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="秒杀结束时间" :prop="dataForm.goodType == '秒杀' ? 'endTime' : ''" v-if="dataForm.goodType == '秒杀'">
        <!-- <el-input v-model="dataForm.endTime" placeholder="秒杀结束时间"></el-input> -->
        <el-date-picker v-model="dataForm.endTime" value-format="timestamp" type="datetime" placeholder="秒杀结束时间">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="拼团所需人数" :prop="dataForm.goodType == '拼团' ? 'groupNum' : ''" v-if="dataForm.goodType == '拼团'">
        <!-- <el-input v-model="dataForm.groupNum" placeholder="拼团所需人数"></el-input> -->
        <el-input-number v-model="dataForm.groupNum" label="拼团所需人数"></el-input-number>
      </el-form-item>
      <el-form-item label="是否在首页展示" prop="display">
        <!-- <el-input v-model="dataForm.display" placeholder="是否在首页展示{0 不展示 1展示}"></el-input> -->
        <el-radio-group v-model="dataForm.display">
          <el-radio :label="0">不展示</el-radio>
          <el-radio :label="1">展示</el-radio>
        </el-radio-group>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
import EditorBar from "@/components/editoritem/editoritem"
export default {
  data() {
    return {
      visible: false,
      dialogImageUrl: '',
      dialogVisible: false,
      photosList: [],
      cdn: window.SITE_CONFIG.baseUrl,
      carType: [{
        id: 1,
        label: 'c1'
      }, {
        id: 2,
        label: 'c2'
      }, {
        id: 3,
        label: 'c3'
      }],
      schoolList: [],
      coachList: [],
      dataForm: {
        numbers: '',
        goodId: 0,
        goodName: '',
        photo: '',
        photos: [],
        goodTitle: '',
        goodDescribe: '',
        goodTags: '',
        goodPrice: '',
        goodNum: '',
        goodType: '',
        goodStatu: '',
        goodPoint: '',
        ifCoach: '',
        coachId: '',
        schoolId: '',
        schoolName: '',
        startTime: '',
        endTime: '',
        groupNum: '',
        display: '',
      },
      dataRule: {
        goodName: [{
          required: true,
          message: '商品班型名不能为空',
          trigger: 'blur'
        }],
        photo: [{
          required: true,
          message: '商品首页照片不能为空',
          trigger: 'blur'
        }],
        photos: [{
          required: true,
          message: '商品轮播图不能为空',
          trigger: 'blur'
        }],
        goodTitle: [{
          required: true,
          message: '标题不能为空',
          trigger: 'blur'
        }],
        goodDescribe: [{
          // required: true,
          message: '商品详情描述不能为空',
          trigger: 'blur'
        }],
        goodTags: [{
          required: true,
          message: '班型标签{课程车型}不能为空',
          trigger: 'blur'
        }],
        goodPrice: [{
          required: true,
          message: '商品班型价格不能为空',
          trigger: 'blur'
        }],
        goodNum: [{
          required: true,
          message: '商品数量不能为空',
          trigger: 'blur'
        }],
        goodType: [{
          required: true,
          message: '商品类型不能为空',
          trigger: 'blur'
        }],
        goodStatu: [{
          required: true,
          message: '上架状态{0 下架  1 上架 }不能为空',
          trigger: 'blur'
        }],
        goodPoint: [{
          required: true,
          message: '赠送积分不能为空',
          trigger: 'blur'
        }],
        ifCoach: [{
          required: true,
          message: '是否是教练商品不能为空',
          trigger: 'blur'
        }],
        coachId: [{
          required: true,
          message: '教练id不能为空',
          trigger: 'blur'
        }],
        // schoolId: [{
        //   required: true,
        //   message: '所属驾校ID不能为空',
        //   trigger: 'blur'
        // }],
        schoolName: [{
          required: true,
          message: '所属驾校名称不能为空',
          trigger: 'blur'
        }],
        startTime: [{
          required: true,
          message: '秒杀开始时间不能为空',
          trigger: 'blur'
        }],
        endTime: [{
          required: true,
          message: '秒杀结束时间不能为空',
          trigger: 'blur'
        }],
        groupNum: [{
          required: true,
          message: '拼团所需人数不能为空',
          trigger: 'blur'
        }],
        numbers: [{
          required: true,
          message: '显示顺序不能为空',
          trigger: 'blur'
        }],
        display: [{
          required: true,
          message: '是否在首页展示{0 不展示 1展示}不能为空',
          trigger: 'blur'
        }],
      }
    }
  },
  components: {
    EditorBar
  },
  mounted() {
    this.getSchoolList()
  },
  computed: {
    upLoaded() {
      let arr = []
      for (let i = 0; i < this.dataForm.photos.length; i++) {
        arr.push({ name: this.dataForm.photos[i], url: this.cdn + '/super/goods/' + this.dataForm.photos[i] })
      }
      return arr
    }
  },
  methods: {
    coachFocus() {
      if (this.dataForm.schoolId == '') {
        this.$message('请先选择驾校')
      }
    },
    //拿教练列表
    getCoachList(schoolId) {
      this.$http({
        url: this.$http.adornUrl('/sys/coach/getlist'),
        method: 'get',
        params: this.$http.adornParams({ schoolId })
      }).then((data) => {
        console.log(data);
        if (data.data.code == 0) {
          this.coachList = data.data.wxCoachEntities
        } else {
          // this.$message.error(data.msg)
        }
      })
    },
    getSchoolList() {
      this.$http({
        url: this.$http.adornUrl('/sys/school/getList'),
        method: 'get',
        params: this.$http.adornParams()
      }).then((data) => {
        // console.log(data);
        this.schoolList = data.data.schoolEntities
      })
    },
    lbtSuccess(res, file, fileList) {
      console.log('response, file, fileList', res, file, fileList);
      if (res && res.code == 0) {
        this.dataForm.photos.push(res.fileName)
        this.$message.success('商品轮播图添加成功')
      } else {
        this.$message.error(res.code + res.msg)
      }
    },
    preview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    remove(file, fileList) {
      console.log(file, fileList);
      // let arr=[]

      this.dataForm.photos = this.dataForm.photos.filter(k => k != file.name)

      // this.dataForm.photos=arr
      this.$message.success('商品轮播图删除成功')
    },
    clearUploadList() {
      console.log('666’');
      this.$refs.lbt.clearFiles()
    },
    //选中学校
    chooseSchool(e) {
      for (let i = 0; i < this.schoolList.length; i++) {
        if (e == this.schoolList[i].schoolName) {
          // console.log(this.schoolName[i].id);
          this.dataForm.schoolId = this.schoolList[i].schoolId
          // return
        }
      }
      this.getCoachList(this.dataForm.schoolId)
    },
    //上传成功钩子
    upLoadSuccess(res, e) {
      console.log(res, e, 'upLoadSuccess');
      this.$message.success('上传成功')
      this.dataForm.photo = res.fileName
    },
    //上传前钩子
    beforeUpLoad(e) {
      console.log(e, 'beforeUpLoad');
    },
    // 监听富文本的输入
    catchData(e) {
      // console.log("111", e);
      this.dataForm.goodDescribe = e
    },
    //富文本中的内容
    editorContent(e) {
      console.log("222", e);

    },


    init(id) {
      this.dataForm.goodId = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.goodId) {
          this.$http({
            url: this.$http.adornUrl(`/sys/goods/info/${this.dataForm.goodId}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.dataForm.numbers = data.goods.numbers
              this.dataForm.goodName = data.goods.goodName
              this.dataForm.photo = data.goods.photo
              this.dataForm.photos = data.goods.photos
              this.dataForm.goodTitle = data.goods.goodTitle
              this.dataForm.goodDescribe = data.goods.goodDescribe
              this.dataForm.goodTags = data.goods.goodTags
              this.dataForm.goodPrice = data.goods.goodPrice
              this.dataForm.goodNum = data.goods.goodNum
              this.dataForm.goodType = data.goods.goodType
              this.dataForm.goodStatu = data.goods.goodStatu
              this.dataForm.goodPoint = data.goods.goodPoint
              this.dataForm.ifCoach = data.goods.ifCoach
              this.dataForm.coachId = data.goods.coachId
              this.dataForm.schoolId = data.goods.schoolId
              this.dataForm.schoolName = data.goods.schoolName
              this.dataForm.startTime = data.goods.stime
              this.dataForm.endTime = data.goods.etime
              this.dataForm.groupNum = data.goods.groupNum
              this.dataForm.saleNum = data.goods.saleNum
              this.dataForm.display = data.goods.display
              this.dataForm.version = data.goods.version
            }
          })
        }
      })
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(`/sys/goods/${!this.dataForm.goodId ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'numbers': parseInt(this.dataForm.numbers),
              'goodId': this.dataForm.goodId || undefined,
              'goodName': this.dataForm.goodName,
              'photo': this.dataForm.photo,
              'photos': this.dataForm.photos,
              'goodTitle': this.dataForm.goodTitle,
              'goodDescribe': this.dataForm.goodDescribe,
              'goodTags': this.dataForm.goodTags,
              'goodPrice': this.dataForm.goodPrice,
              'goodNum': this.dataForm.goodNum,
              'goodType': this.dataForm.goodType,
              'goodStatu': this.dataForm.goodStatu,
              'goodPoint': this.dataForm.goodPoint,
              'ifCoach': this.dataForm.ifCoach,
              'coachId': this.dataForm.coachId,
              'schoolId': this.dataForm.schoolId,
              'schoolName': this.dataForm.schoolName,
              'startTime': this.dataForm.startTime,
              'endTime': this.dataForm.endTime,
              'groupNum': this.dataForm.groupNum,
              'saleNum': this.dataForm.saleNum,
              'display': this.dataForm.display,
              'version': this.dataForm.version,
            })
          }).then(({
            data
          }) => {
            if (data && data.code === 0) {
              this.clearUploadList()
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {

                  this.visible = false
                  this.$emit('refreshDataList')
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        }
      })
    }
  },
  beforeDestroy() {
    console.log('6’');
    this.clearUploadList()
  },
  deactivated() {
    console.log('6’');
    this.clearUploadList()
  }
}
</script>

<style lang="scss">
.boforeUpload {
  width: 200px;
  height: 100px;
  line-height: 100px;
  border-radius: 15px;
  border: 1px solid #ccc;
  color: #ccc;
  font-size: 30px;
}

.upLoad {
  max-height: 200px;
  max-width: 400px;
  border: 1px solid #333333;
  border-radius: 15px;
}
</style>
