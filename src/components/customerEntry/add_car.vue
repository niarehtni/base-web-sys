<template>
  <el-form :model="ruleForm2" status-icon ref="ruleForm2" label-width="140px" class="demo-ruleForm">
    <el-form-item label="车牌号" prop="carNo"  :rules='$rules.requiredInputValue'>
      <el-input  v-model="ruleForm2.carNo" ></el-input>
    </el-form-item>
    <el-form-item label="车辆登记编号" prop="carNum"  :rules='$rules.requiredInputValue'>
      <el-input  v-model="ruleForm2.carNum" ></el-input>
    </el-form-item>
    <el-form-item label="行驶证编号" prop="certificateNum"  :rules='$rules.requiredInputValue'>
      <el-input  v-model="ruleForm2.certificateNum" ></el-input>
    </el-form-item>
    <el-form-item label="车辆状态" prop="state"  :rules='$rules.requiredSelectValue'>
      <el-select v-model='ruleForm2.state' placeholder='请选择'>
        <el-option
          v-for="item in $dict.findDictData('PropertyStatus')"
          :key='item.id'
          :label='item.value'
          :value='item.id'>
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="车辆所有人" prop="ownerName"  :rules='$rules.requiredInputValue'>
      <el-input  v-model="ruleForm2.ownerName" ></el-input>
    </el-form-item>
    <el-form-item label="与申请人关系" prop="relationships"  :rules='$rules.requiredSelectValue'>
      <el-select v-model='ruleForm2.relationships' placeholder='请选择'>
        <el-option
          v-for="item in $dict.findDictData('ApplicantRelationship')"
          :key='item.id'
          :label='item.value'
          :value='item.id'>
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="车辆品牌" prop="carCategory"  :rules='$rules.requiredInputValue'>
      <el-input  v-model="ruleForm2.carCategory" ></el-input>
    </el-form-item>
    <el-form-item label="评估价值(万元)" prop="evaluationAmount"   :rules='$rules.requiredInputNumber'>
      <el-input  v-model="ruleForm2.evaluationAmount" type="number"></el-input>
    </el-form-item>
    <el-form-item label="贷款余额(万元)" prop="loanBalanceAmount"   :rules='$rules.requiredInputNumber'>
      <el-input  v-model="ruleForm2.loanBalanceAmount" type="number"></el-input>
    </el-form-item>
    <!--<el-form-item label="产权详细地址" prop="address"  >-->
      <!--<el-input  v-model="ruleForm2.address" ></el-input>-->
    <!--</el-form-item>-->
    <el-form-item label="备注" prop="remark"  >
      <el-input  v-model="ruleForm2.remark" type="textarea"
                 :autosize="{ minRows: 2, maxRows: 4}"></el-input>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submitForm('ruleForm2')">提交</el-button>
      <el-button @click="resetForm('ruleForm2')">取消</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
    import {customerInfoController} from './../../controller/common/customerInfo_controller'
    import {guaranteeInfoController} from './../../controller/common/guaranteeInfo_controller'
    export default {
      data () {
        return {
          customerInfoController: this.isCustomer ? customerInfoController : guaranteeInfoController,
          ruleForm2: {
            orderId: this.orderId,
            customerId: this.$store.state.customerId,
            certificateNum: '',
            exist: 1,
            carNo: '',
            carNum: '',
            ownerName: '',
            state: '',
            area: '',
            carCategory: '',
            relationships: '',
            addressList: [],
            addressProvince: '',
            addressCity: '',
            addressCounty: '',
            evaluationAmount: null,
            loanBalanceAmount: null,
            address: '',
            remark: ''
          }
        }
      },
      props: {
        isCustomer: {
          type: Boolean,
          default: true
        },
        orderId: {}
      },
      methods: {
        editInit (obj) {
          console.log(obj)
          this.ruleForm2.addressList = [obj.addressProvince, obj.addressCity, obj.addressCounty]
          this.ruleForm2 = {...this.ruleForm2, ...obj}
          this.$refs['ruleForm2'].resetFields()
        },
        addInit () {
          this.ruleForm2 = {
            orderId: this.orderId,
            customerId: this.$store.state.customerId,
            certificateNum: '',
            exist: 1,
            carNo: '',
            carNum: '',
            ownerName: '',
            state: '',
            area: '',
            carCategory: '',
            relationships: '',
            addressList: [],
            addressProvince: '',
            addressCity: '',
            addressCounty: '',
            evaluationAmount: null,
            loanBalanceAmount: null,
            address: '',
            remark: ''
          }
          this.$refs['ruleForm2'].resetFields()
        },
        submitForm (formName) {
          this.$refs[formName].validate((valid) => {
            if (valid) {
              this.addCar()
            } else {
              return false
            }
          })
        },
        dataFormat () {
          [this.ruleForm2.addressProvince, this.ruleForm2.addressCity, this.ruleForm2.addressCounty] = this.ruleForm2.addressList
          this.ruleForm2.category = 'car'
          this.ruleForm2.type = this.isCustomer ? 1 : 2
        },
        addCar () {
          this.dataFormat()
          this.customerInfoController.saveCustomerAssets(this.ruleForm2).then(res => {
            if (res.status === 200) {
              if (res.data) {
                this.$store.commit('SET_CUSTOMER_ID', res.data)
              }
              this.$message.success('新增/编辑成功')
              this.$nextTick(() => { this.$emit('close') })
            }
          })
        },
        resetForm (formName) {
          this.$refs[formName].resetFields()
          this.$emit('cancel')
        }
      }
    }
</script>

<style scoped>

</style>
