<template>
  <div class="list-main">
    <main-title>
      <i class="el-icon-document-copy"></i> 管理 Manage
      <div class="feature-bar">
        <el-button type="success" class="feature-btn" @click="addVisible = true">
          <i class="el-icon-plus"></i>新增
        </el-button>
      </div>
    </main-title>
    <div class="list-col">
      <el-table :data="tableData" style="width: 100%" :default-sort = "{prop: 'date', order: 'descending'}">
        <el-table-column prop="ehr_id" label="ID" width="100"></el-table-column>
        <el-table-column prop="user.name" label="姓名" width="110"></el-table-column>
        <el-table-column prop="ehr_position" label="职位" width="180"></el-table-column>
        <el-table-column prop="user.phone" label="电话" width="180"></el-table-column>
        <el-table-column prop="ehr_applyaccount" label="可发布岗位" sortable width="130"></el-table-column>
        <el-table-column prop="ehr_currentaccount" label="已发布岗位" sortable width="130"></el-table-column>
        <el-table-column prop="user.created_time" label="创建日期" sortable width="180"></el-table-column>
        <el-table-column fixed="right" label="操作" width="100">
          <template slot-scope="scope">
            <el-button @click="detailClick(scope.row)" type="text" size="small">详情</el-button>
            <el-popconfirm title="确定删除？"
               @confirm="deleteClick(scope.row)">
                <el-button slot="reference"  type="text" size="small">删除</el-button>
              </el-popconfirm>
          </template>
        </el-table-column>
      </el-table>
      <div class="page-peddle">
        <el-pagination :page-size="8" :pager-count="7" layout="prev, pager, next" :total="200"></el-pagination>
      </div>
    </div>
    <el-dialog title="新增HR" :visible.sync="addVisible">
      <el-form :model="form">
        <el-row>
          <el-col :span="12" class="left-form">
            <div class="input-li">
              <el-row>
                <el-col :span="4" class="input-name">姓名：</el-col>
                <el-col :span="20" class="input-col">
                  <el-input v-model="form.name" placeholder="请输入内容"></el-input>
                </el-col>
              </el-row>
            </div>
            <div class="input-li">
              <el-row>
                <el-col :span="4" class="input-name">电话：</el-col>
                <el-col :span="20" class="input-col">
                  <el-input v-model="form.phone" placeholder="请输入内容"></el-input>
                </el-col>
              </el-row>
            </div>
            <div class="input-li">
              <el-row>
                <el-col :span="4" class="input-name">密码：</el-col>
                <el-col :span="20" class="input-col">
                  <el-input v-model="form.password" placeholder="请输入内容" show-password></el-input>
                </el-col>
              </el-row>
            </div>
          </el-col>
          <el-col :span="12" class="right-form">
            <div class="input-li">
              <el-row>
                <el-col :span="5" class="input-name">性别：</el-col>
                <el-col :span="19" class="input-col">
                  <el-select v-model="form.sex" placeholder="请选择" class="select-input">
                    <el-option label="男" value="0"></el-option>
                    <el-option label="女" value="1"></el-option>
                  </el-select>
                </el-col>
              </el-row>
            </div>
            <div class="input-li">
              <el-row>
                <el-col :span="5" class="input-name">岗位：</el-col>
                <el-col :span="19" class="input-col">
                  <el-input v-model="form.ehr_position" placeholder="请输入内容" ></el-input>
                </el-col>
              </el-row>
            </div>
            <div class="input-li">
              <el-row>
                <el-col :span="5" class="input-name">工号：</el-col>
                <el-col :span="19" class="input-col">
                  <el-input v-model="form.ehr_jobnum" placeholder="H20201111" ></el-input>
                </el-col>
              </el-row>
            </div>
          </el-col>
        </el-row>
        <div class="sub-col">
          <el-button type="primary" @click="submit"><i class="el-icon-plus"></i>新建</el-button>
        </div>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import MainTitle from "../../components/company/body/MainTitle";
export default {
  name: "list",
  components: {MainTitle},
  data() {
    return {
      addVisible: false,
      form: {
        id:'',
        name: '',
        phone: '',
        password: '',
        sex: '',
        ehr_position: '',
        ehr_jobnum: ''

      },
      ea_id:'',
      //  name: '',
      //  phone: '',
      //  password: '',
      //  sex: '',
       enterprise_id:'',
      tableData: [
        
      ]
    }
  },
   created(){
    const _this = this
    this.ea_id=11
    this.$http.get('http://localhost:8085/company/list/'+localStorage.getItem("ea_id"))
      .then(function (response) {
        for(var i=0;i<response.data.length;i++){
          _this.tableData.push(response.data[i])
          _this.tableData[i].user.created_time=_this.tableData[i].user.created_time.substring(0,10);
        }

      })
      .catch(function (error) {
        console.log(error)
      });
      
  },
  methods: {
    detailClick(row) {
      this.$router.push({
        path: '/company/hr_detail',
        query: {
          id: row.ehr_id,
        }
      })
    },
    deleteClick(row) {
      console.log(row.ehr_id);
      this.$http.get('http://localhost:8085/company/list/delete/'+row.ehr_id)
      .then(function(response) {
            
      })
      .catch(function (error) {
        console.log(error)
      });
      location.reload();
      
    },
    submit() {
      const that=this;
      console.log(this.form)
       this.$http.post('http://localhost:8085/company/list/insertUser',{
         name:this.form.name,
         phone:this.form.phone,
         password:this.form.password,
         sex:this.form.sex,

       })
      .then(function(response) {
         that.form.id=response.data.id;
         console.log(that.form.id);
         that.$http.post('http://localhost:8085/company/profile/'+that.ea_id)
        .then(function(response) {
            console.log(response.data);
            that.enterprise_id=response.data.e_id
            console.log(that.enterprise_id);
      });
         that.$http.post('http://localhost:8085/company/list/insertCompanyHr',{
           enterprise_id:that.enterprise_id,
           ehr_id:that.form.id,
           ehr_position:that.form.ehr_position,
           ehr_jobnum:that.form.ehr_jobnum

       })
        .then(function(response) {
            console.log(response.data);
      })
      .catch(function (error) {
        console.log(error)
      });
      })
      .catch(function (error) {
        console.log(error)
      });
      

      console.log(this.form)
    }
  }
}
</script>

<style scoped>
@import "../../assets/css/company/list.css";
</style>
