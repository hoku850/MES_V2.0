<!--订单配置页面的统一编辑页面-->
<template>
  <div class="edit-panel-container ">
    <div class="edit-panel">
      <div class="form-row">
        <div class="form-group col-2 mb-0">
          <h2>{{isUpdate === true ? '更新表单:' : ''}} {{isCreate === true ? '创建表单:' : ''}}</h2>
        </div>
      </div>
      <div class="form-row mb-3">
        <div class="form-group col-3">
          <label for="edit-ZhiDan" class="col-form-label">制单号:{{formData[4].notNull === true ? '*' : ''}}</label>
          <input type="text" id="edit-ZhiDan"
                 class="form-control"
                 v-model="formData[4].value"
                 :disabled="!isCreate">
        </div>
        <div class="form-group col-3" v-if="isUpdate">
          <label for="edit-Status" class="col-form-label">状态:{{formData[3].notNull === true ? '*' : ''}}</label>
          <select id="edit-Status" class="custom-select"
                  v-model="formData[3].value" :disabled="!isCreate">
            <option value="" disabled>请选择</option>
            <option value="0">未开始</option>
            <option value="1">进行中</option>
            <option value="2">已完成</option>
            <option value="3">已作废</option>
          </select>
        </div>

      </div>
      <div class="dropdown-divider"></div>
      <div class="form-row">
        <div class="form-group col-2 mb-0" v-for="(item, index) in formData" v-if="index >= 5">
          <div v-if="index !== 25">
            <label :for="'edit-' + item.field" class="col-form-label">{{item.title}}: {{item.notNull === true ? '*' :
              ''}}</label>
            <input type="text" :id="'edit-' + item.field" class="form-control" v-model="item.value"
                   :disabled="(!isCreate) && (formData[3].value !== 0)">
          </div>
          <div v-if="index === 25">
            <label :for="'edit-' + item.field" class="col-form-label">{{item.title}}: {{item.notNull === true ? '*' :
              ''}}</label>
            <select type="text" :id="'edit-' + item.field" class="form-control" v-model="item.value"
                    :disabled="(!isCreate) && (formData[3].value !== 0)">
              <option value="" disabled>请选择</option>
              <option value="无绑定">无绑定</option>
              <option value="与SMI卡绑定">与SMI卡绑定</option>
              <option value="与SIM&BAT绑定">与SIM&BAT绑定</option>
              <option value="与SIM&VIP绑定">与SIM&VIP绑定</option>
              <option value="与BAT绑定">与BAT绑定</option>
            </select>
          </div>
        </div>
        <p class="form-control-sm">* 为非空项目</p>

      </div>
      <div class="dropdown-divider"></div>
      <div class="form-row justify-content-around">
        <button type="button" class="btn btn-secondary col-2 text-white" @click="closePanel">取消</button>
        <button type="button" class="btn btn-primary col-2 text-white" @click="btnHandler"
                :disabled="(!isCreate) && (formData[3].value !== 0)">提交
        </button>
      </div>
    </div>
  </div>
</template>

<script>
  import {mapGetters, mapActions} from 'vuex'
  import {orderOperUrl} from "../../../../config/orderApiConfig";
  import {axiosFetch} from "../../../../utils/fetchData";
  import {errHandler} from "../../../../utils/errorHandler";
  import _ from 'lodash'

  export default {
    name: "EditPanel",
    //props: ['editData'],
    data() {
      return {
        isCreate: false,
        isUpdate: false,
        formData: [],
        isPending: false
      }
    },
    computed: {
      ...mapGetters(['editData', 'copyData'])
    },
    created: function () {
      if (this.editData.length !== 0) {
        this.isCreate = false;
        this.isUpdate = true;
        this.formData = JSON.parse(JSON.stringify(this.editData))
      } else if (this.copyData.length !== 0) {
        this.isCreate = true;
        this.isUpdate = false;
        this.formData = JSON.parse(JSON.stringify(this.copyData));
      } else {
        this.isCreate = true;
        this.isUpdate = false;
        this.formData = [
          {
            "title": "序号",
            "field": "Id",
            "value": ''
          }, {
            "title": "序号",
            "field": "showId",
            "value": ''
          }, {
            "title": "状态",
            "field": "ShowStatus",
            "value": ''
          }, {
            "title": "状态",
            "field": "Status",
            "value": ''
          }, {
            "title": "制单号",
            "field": "ZhiDan",
            "value": '',
            "notNull": true
          }, {
            "title": "型号",
            "field": "SoftModel",
            "value": '',
            "notNull": true
          }, {
            "title": "SN1",
            "field": "SN1",
            "value": '',
            "notNull": false
          }, {
            "title": "SN2",
            "field": "SN2",
            "value": '',
            "notNull": false
          }, {
            "title": "SN3",
            "field": "SN3",
            "value": '',
            "notNull": false
          }, {
            "title": "箱号1",
            "field": "BoxNo1",
            "value": '',
            "notNull": true
          }, {
            "title": "箱号2",
            "field": "BoxNo2",
            "value": '',
            "notNull": true
          }, {
            "title": "生产日期",
            "field": "ProductDate",
            "value": '',
            "notNull": true
          }, {
            "title": "颜色",
            "field": "Color",
            "value": '',
            "notNull": true
          }, {
            "title": "重量",
            "field": "Weight",
            "value": '',
            "notNull": true
          }, {
            "title": "数量",
            "field": "Qty",
            "value": '',
            "notNull": true
          }, {
            "title": "产品编号",
            "field": "ProductNo",
            "value": '',
            "notNull": true
          }, {
            "title": "版本",
            "field": "Version",
            "value": '',
            "notNull": true
          }, {
            "title": "起始IMEI号",
            "field": "IMEIStart",
            "value": '',
            "notNull": true
          }, {
            "title": "终止IMEI号",
            "field": "IMEIEnd",
            "value": '',
            "notNull": true
          }, {
            "title": "起始SIM卡号",
            "field": "SIMStart",
            "value": '',
            "notNull": false
          }, {
            "title": "终止SIM卡号",
            "field": "SIMEnd",
            "value": '',
            "notNull": false
          }, {
            "title": "起始BAT号",
            "field": "BATStart",
            "value": '',
            "notNull": false
          }, {
            "title": "终止BAT号",
            "field": "BATEnd",
            "value": '',
            "notNull": false
          }, {
            "title": "起始VIP号",
            "field": "VIPStart",
            "value": '',
            "notNull": false
          }, {
            "title": "终止VIP号",
            "field": "VIPEnd",
            "value": '',
            "notNull": false
          }, {
            "title": "IMEI关联",
            "field": "IMEIRel",
            "value": '',
            "notNull": true
          }, {
            "title": "TAC信息",
            "field": "TACInfo",
            "value": '',
            "notNull": true
          }, {
            "title": "公司名",
            "field": "CompanyName",
            "value": '',
            "notNull": false
          }, {
            "title": "备注1",
            "field": "Remark1",
            "value": '',
            "notNull": false
          }, {
            "title": "备注2",
            "field": "Remark2",
            "value": '',
            "notNull": false
          }, {
            "title": "备注3",
            "field": "Remark3",
            "value": '',
            "notNull": false
          }, {
            "title": "备注4",
            "field": "Remark4",
            "value": '',
            "notNull": false
          }, {
            "title": "备注5",
            "field": "Remark5",
            "value": '',
            "notNull": false
          }, {"field": 'JST_template', "title": 'JST模板', "value": '', "notNull": false},
          {"field": 'CHT_template1', "title": 'CHT模板1', "value": '', "notNull": false},
          {"field": 'CHT_template2', "title": 'CHT模板2', "value": '', "notNull": false},
          {"field": 'BAT_prefix', "title": 'BAT前缀', "value": '', "notNull": false},
          {"field": 'BAT_digits', "title": 'BAT位数', "value": '', "notNull": false},
          {"field": 'SIM_prefix', "title": 'SIM前缀', "value": '', "notNull": false},
          {"field": 'SIM_digits', "title": 'SIM位数', "value": '', "notNull": false},
          {"field": 'VIP_prefix', "title": 'VIP前缀', "value": '', "notNull": false},
          {"field": 'VIP_digits', "title": 'VIP位数', "value": '', "notNull": false},
          {"field": 'ICCID_prefix', "title": 'ICCID前缀', "value": '', "notNull": false},
          {"field": 'ICCID_digits', "title": 'ICCID位数', "value": '', "notNull": false},
          {"field": 'IMEIPrints', "title": 'IMEI打印', "value": '', "notNull": false},
          {"field": 'MAC_prefix', "title": 'MAC前缀', "value": '', "notNull": false},
          {"field": 'MAC_digits', "title": 'MAC位数', "value": '', "notNull": false},
          {"field": 'Equipment_prefix', "title": 'Equipment前缀', "value": '', "notNull": false},
          {"field": 'Equipment_digits', "title": 'Equipment位数', "value": '', "notNull": false},]
      }
    },
    mounted: function () {


    },
    methods: {
      ...mapActions(['setEditing', 'setEditData', 'setCopyData']),
      closePanel: function () {
        this.setEditData([]);
        this.setCopyData([]);
        this.setEditing(false)
      },
      updateData: function () {
        this.isPending = true;
        let tempData = {
          id: this.formData[0].value

        };
        for (let i = 4; i < this.formData.length; i++) {
          if (this.formData[i].notNull === true) {
            if (_.trim(this.formData[i].value) !== "") {
              tempData[this.formatCase(this.formData[i].field)] = _.trim(this.formData[i].value);
            } else {
              this.$alertWarning("存在不能为空数据");
              return
            }
          }
          else {
            tempData[this.formatCase(this.formData[i].field)] = _.trim(this.formData[i].value);
          }
        }
        switch (tempData['iMEIRel']) {
          case '无绑定':
            tempData['iMEIRel'] = 0;
            break;
          case '与SIM卡绑定':
            tempData['iMEIRel'] = 1;
            break;
          case '与SIM&BAT绑定':
            tempData['iMEIRel'] = 2;
            break;
          case '与SIM&VIP绑定':
            tempData['iMEIRel'] = 3;
            break;
          case '与BAT绑定':
            tempData['iMEIRel'] = 4;
            break;
        }

        let options = {
          url: orderOperUrl + '/update',
          data: tempData
        };
        axiosFetch(options).then(res => {
          if (res.data.result === 200) {
            this.$alertSuccess('更新成功');
            this.setEditing(false);
            this.setEditData([]);
            let tempUrl = this.$route.path;
            //console.log(this.$route.url)
            this.$router.replace('/_empty')
            this.$router.replace(tempUrl)
          } else if (res.data.result === 400) {
            this.$alertInfo('请检查格式并重试')
          } else {
            errHandler(res.data.result)
          }
        }).catch(err => {
          this.$alertDanger('请求超时，请刷新重试')
        })
      },
      createData: function () {
        this.isPending = true;
        let tempData = {};
        for (let i = 4; i < this.formData.length; i++) {
          if (this.formData[i].notNull === true) {
            if (_.trim(this.formData[i].value) !== "") {
              tempData[this.formatCase(this.formData[i].field)] = _.trim(this.formData[i].value);
            } else {
              this.$alertWarning("存在不能为空数据");
              return
            }
          }
          else if (_.trim(this.formData[i].value) !== "") {
            tempData[this.formatCase(this.formData[i].field)] = _.trim(this.formData[i].value);
          }
        }
        switch (tempData['iMEIRel']) {
          case '无绑定':
            tempData['iMEIRel'] = 0;
            break;
          case '与SMI卡绑定':
            tempData['iMEIRel'] = 1;
            break;
          case '与SIM&BAT绑定':
            tempData['iMEIRel'] = 2;
            break;
          case '与SIM&VIP绑定':
            tempData['iMEIRel'] = 3;
            break;
          case '与BAT绑定':
            tempData['iMEIRel'] = 4;
            break;
        }
        let options = {
          url: orderOperUrl + '/create',
          data: tempData
        };
        axiosFetch(options).then(res => {
          if (res.data.result === 200) {
            this.$alertSuccess('添加成功');
            this.setEditing(false);
            this.setCopyData([]);
            let tempUrl = this.$route.path;
            this.$router.replace('/_empty');
            this.$router.replace(tempUrl)
          } else if (res.data.result === 412) {
            this.$alertWarning('请检查格式并重试');
            this.setEditing(false);
          } else if (res.data.result === 400) {
            this.$alertWarning('该制单号已存在');
            this.setEditing(false);
          } else {
            errHandler(res.data.result)
          }
        }).catch(err => {
          console.log(JSON.stringify(err))
          this.$alertDanger('请求超时，请刷新重试');
        })
      },
      btnHandler: function () {
        if (!this.isPending) {
          if (this.isCreate) {
            this.createData();
            this.isPending = false;
          } else if (this.isUpdate) {
            this.updateData();
            this.isPending = false;
          }
        }
      },
      formatCase: function (str) {
        if (str.indexOf('_') > -1) {
          let string = str.toLowerCase();
          let strArray = string.split('_');
          for (let i = 1; i < strArray.length; i++) {
            strArray[i] = strArray[i].replace(/( |^)[a-z]/g, (L) => L.toUpperCase());
          }
          return strArray.join('');
        } else {
          return str.replace(/( |^)[A-Z]/g, (L) => L.toLowerCase());
        }
      }
    }
  }
</script>

<style scoped>
  .edit-panel-container {
    position: absolute;
    /*display: flex;*/
    /*align-items: center;*/
    /*justify-content: center;*/
    /*height: 100%;*/
    margin-bottom: 20px;
    width: 100%;
    left: 0;
    top: 0;
    background: rgba(0, 0, 0, 0.1);
    z-index: 101;
  }

  .edit-panel {
    background: #ffffff;
    height: 100%;
    width: 100%;
    z-index: 102;
    border-radius: 10px;
    box-shadow: 3px 3px 20px 1px #bbb;
    padding: 30px 60px 10px 60px;
  }
</style>
