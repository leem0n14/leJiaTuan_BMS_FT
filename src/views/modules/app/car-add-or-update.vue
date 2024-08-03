<template>
  <el-dialog :title="!dataForm.id ? '新增' : '修改'" :close-on-click-modal="false" :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
      <el-form-item label="车牌号" prop="carId">
        <el-input v-model="dataForm.carId" placeholder="车牌号" maxlength="7"></el-input>
      </el-form-item>
      <el-form-item label="车型" prop="carType">
        <el-input v-model="dataForm.carType" placeholder="车型"></el-input>
      </el-form-item>
      <el-form-item label="驾照类型" prop="carForm">
        <el-select v-model="dataForm.carForm" clearable placeholder="请选择驾照类型">
          <el-option v-for="item in typeName" :key="item" :label="item" :value="item">
          </el-option>
        </el-select>
      </el-form-item>
      <!-- <el-form-item label="驾校ID" prop="schoolId">
        <el-input v-model="dataForm.schoolId" placeholder="驾校ID"></el-input>
      </el-form-item> -->
      <el-form-item label="所属驾校" prop="schoolName">
        <!-- <el-input v-model="dataForm.schoolName" placeholder="所属驾校"></el-input> -->
        <el-select v-model="dataForm.schoolName" placeholder="请选择学校" @change="schoolHandle(dataForm.schoolName)">
          <el-option v-for="item in schoolList" :key="item.schoolId" :label="item.schoolName" :value="item.schoolName">
          </el-option>
        </el-select>
      </el-form-item>
      <!-- <el-form-item label="保险生效" prop="bxTime">
        <el-date-picker value-format="timestamp" v-model="dataForm.bxTime" type="date" placeholder="选择保险生效日期">
        </el-date-picker>
      </el-form-item>
      <el-form-item label="保险到期" prop="byTime">
        
        <el-date-picker value-format="timestamp" v-model="dataForm.byTime" type="date" placeholder="选择保险到期日期">
        </el-date-picker>
      </el-form-item> -->
      <!-- <el-form-item label="教练ID" prop="coachId">
        <el-input v-model="dataForm.coachId" placeholder="教练ID"></el-input>
      </el-form-item> -->
      <el-form-item label="教练名" prop="coachName">
        <!-- <el-input v-model="dataForm.coachName" placeholder="教练名"></el-input> -->
        <el-select v-model="dataForm.coachName" placeholder="请选择教练" @focus="coachFocus()"
          @change="coachHandle(dataForm.coachName)">
          <el-option v-for="item in coachList" :key="item.coachId" :label="item.coachName" :value="item.coachName">
          </el-option>
        </el-select>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        carId: '',
        carType: '',
        schoolId: '',
        schoolName: '',
        bxTime: '',
        byTime: '',
        coachId: '',
        coachName: '',
        carForm: ''
      },
      typeName: ['c1(手动档)', 'c2(自动挡)', 'c3(小车)', 'A2(货车)', 'B2(货车)', 'A1(客车)', 'A3(客车)', 'B1(客车)', 'D(摩托车)',
        'E(摩托车)', 'F(摩托车)'
      ],
      schoolId: null,
      schoolList: [],
      coachList: [],
      dataRule: {
        carId: [
          { required: true, message: '车牌号不能为空', trigger: 'blur' }
        ],
        carType: [
          { required: true, message: '车型不能为空', trigger: 'blur' }
        ],
        carForm: [
          { required: true, message: '驾照类型不能为空', trigger: 'blur' }
        ],
        schoolId: [
          { required: true, message: '驾校ID不能为空', trigger: 'blur' }
        ],
        schoolName: [
          { required: true, message: '所属驾校不能为空', trigger: 'blur' }
        ],
        bxTime: [
          { required: true, message: '保险日期不能为空', trigger: 'blur' }
        ],
        byTime: [
          { required: true, message: '保养日期不能为空', trigger: 'blur' }
        ],
        coachId: [
          { required: true, message: '教练ID不能为空', trigger: 'blur' }
        ],
        coachName: [
          { required: true, message: '教练名不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  mounted() {
    // this.getCoachList()
    this.getSchoolList()
  },

  methods: {
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
        if (data.data.code == 0) {
          this.schoolList = data.data.schoolEntities
        } else {
          // this.$message.error(data.msg)
        }
      })
    },
    coachFocus() {
      if (this.schoolId != null) {
        this.getCoachList(this.schoolId)
      } else {
        this.$message('请先选择驾校')
      }
    },
    coachHandle(e) {
      console.log(e);
      for (let i = 0; i < this.coachList.length; i++) {
        if (e == this.coachList[i].coachName) {
          this.dataForm.coachId = this.coachList[i].coachId
        }
      }
    },
    schoolHandle(e) {
      this.dataForm.coachName = ''
      this.dataForm.coachId = ''
      // console.log('??', e);
      for (let i = 0; i < this.schoolList.length; i++) {
        if (e == this.schoolList[i].schoolName) {
          this.schoolId = this.schoolList[i].schoolId
          this.dataForm.schoolId = this.schoolList[i].schoolId
          console.log(this.schoolId, this.schoolList[i].schoolId);
          this.getCoachList(this.schoolList[i].schoolId)
        }
      }

    },
    init(id) {
      this.dataForm.id = id || 0
      this.visible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].resetFields()
        if (this.dataForm.id) {
          this.$http({
            url: this.$http.adornUrl(`/sys/car/info/${this.dataForm.id}`),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carId = data.car.carId
              this.dataForm.carType = data.car.carType
              this.dataForm.schoolId = data.car.schoolId
              this.dataForm.schoolName = data.car.schoolName
              this.dataForm.bxTime = data.car.bxTime
              this.dataForm.byTime = data.car.byTime
              this.dataForm.coachId = data.car.coachId
              this.dataForm.coachName = data.car.coachName
              this.dataForm.carForm = data.car.carForm
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
            url: this.$http.adornUrl(`/sys/car/${!this.dataForm.id ? 'save' : 'update'}`),
            method: 'post',
            data: this.$http.adornData({
              'id': this.dataForm.id || undefined,
              'carId': this.dataForm.carId,
              'carType': this.dataForm.carType,
              'schoolId': this.dataForm.schoolId,
              'schoolName': this.dataForm.schoolName,
              'bxTime': this.dataForm.bxTime,
              'byTime': this.dataForm.byTime,
              'coachId': this.dataForm.coachId,
              'coachName': this.dataForm.coachName,
              carForm: this.dataForm.carForm
            })
          }).then(({ data }) => {
            if (data && data.code === 0) {
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
  }
}
</script>
