<template>
  <div class="app-container">

    <el-button class="add" type="primary" @click="dialogShow('add')">添加成员</el-button>

    <el-table :data="list" v-loading.body="listLoading" element-loading-text="Loading" border fit highlight-current-row>
      <el-table-column align="center" label='ID' width="150">
        <template slot-scope="scope">
          {{scope.row.id}}
        </template>
      </el-table-column>
      <el-table-column label="头像" width="150" align="center">
        <template slot-scope="scope">
          {{scope.row.userIcon}}
        </template>
      </el-table-column>
      <el-table-column label="姓名" width="150" align="center">
        <template slot-scope="scope">
          {{scope.row.name}}
        </template>
      </el-table-column>
      <el-table-column label="手机号" width="150" align="center">
        <template slot-scope="scope">
          <span>{{scope.row.phone}}</span>
        </template>
      </el-table-column>
      <el-table-column label="密码" align="center">
        <template slot-scope="scope">
          {{scope.row.password}}
        </template>
      </el-table-column>
      <el-table-column label="日程" align="center">
        <template slot-scope="scope">
          <el-button type="success" size="mini" @click="checkSchedule">查看</el-button>
        </template>
      </el-table-column>
      <el-table-column label="好友" align="center">
        <template slot-scope="scope">
          <el-button type="success" size="mini" @click="checkFriend">查看</el-button>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center">
        <template slot-scope="scope">
          <el-button type="success" size="mini" @click="dialogShow('edit',scope.row)">编辑</el-button>
          <el-button type="danger" size="mini" @click="deleteOne(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>

    <el-dialog :title="title" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="姓名" label-width="60px">
          <el-input v-model.trim="form.name" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="手机号" label-width="60px">
          <el-input v-model.trim="form.phone" auto-complete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" label-width="60px">
          <el-input v-model.trim="form.password" auto-complete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogConfirm">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
  import {ERR_OK} from 'api/config';
  import {getList, deleteOne,editOne,addOne} from 'api/mock'

  export default {
    data() {
      return {
        form: {},
        title:'',
        dialogFormVisible: false,
        list: null,
        listLoading: true
      }
    },
    created() {
      this.fetchData()
    },
    methods: {
      checkSchedule(){
        this.$router.push('/example/schedule')
      },
      checkFriend(){
        this.$router.push('/example/friend')
      },
      callback(text){
        this.fetchData();
        this.dialogFormVisible=false;
        this.$message.success(text);
      },

      dialogShow(type,row){
        this.dialogFormVisible = true;
        this.title=type==='add'?'成员添加':'信息修改';
        this.form =type==='add'?{}:Object.assign({},row);
        this.type=type;
      },

      addOne(){
        let form=this.form;
        if(form.name&&form.age&&form.occupation){
          let list=this.list;
          let addId={id:parseInt(list[list.length-1].id)+1};
          addOne(Object.assign({},addId,this.form)).then(()=>{
            this.callback('添加成功');
          })
        }
        else{
          this.$message.info('请填写完整的信息后进行添加');
        }
      },
      editOne(){
        editOne(this.form).then(()=>{
          this.callback('修改成功');
        })
      },

      dialogConfirm(){
          this.type==='add'?this.addOne():this.editOne();
      },
      deleteOne(id){
        deleteOne(id).then(() => {
          this.callback('删除成功');
        })
      },
      fetchData() {
        this.listLoading = true;
        getList().then((ops) => {
          this.list = ops;
          this.listLoading = false;
        })
      }
    }
  }
</script>

<style scoped>
  .add{
    margin-bottom: 20px;
  }
</style>
