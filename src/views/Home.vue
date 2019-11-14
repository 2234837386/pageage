<template>
  <div class="home">
    <el-container>
      <el-header>Header</el-header>
      <el-container>
        <el-aside width="200px">Aside</el-aside>
        <el-main>
           <el-button type="primary" @click="dialogFormVisible=true">添加</el-button>
          <el-table :data="tableData" style="width: 100%">
            <el-table-column label="姓名" prop="username"></el-table-column>
            <el-table-column label="密码" prop="password"></el-table-column>
            <el-table-column label="性别" prop="sex"></el-table-column>
            <el-table-column align="right">
              <template slot="header" slot-scope="scope">操作</template>
              <template slot-scope="scope">
                <el-button size="mini" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                <el-button
                  size="mini"
                  type="danger"
                  @click="handleDelete(scope.$index, scope.row)"
                >Delete</el-button>
              </template>
            </el-table-column>
          </el-table>

          <el-pagination
            background
            layout="prev, pager, next"
            @current-change="changePage"
            :page-size="limit"
            :total="total"
          ></el-pagination>
        </el-main>
      </el-container>
    </el-container>
    <el-dialog title="添加成员" :visible.sync="dialogFormVisible">
      <el-form :model="form">
        <el-form-item label="姓名" :label-width="formLabelWidth">
          <el-input v-model="form.username" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="密码" :label-width="formLabelWidth">
          <el-input v-model="form.password" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="性别" :label-width="formLabelWidth">
          <el-input v-model="form.phone" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addFun">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "home",
  components: {},
  created() {
    this.getData();
  },
  methods: {
    getData() {
      this.$http
        .get("/api/list", {
          params: { limit: this.limit, pagenum: this.pagenum }
        })
        .then(res => {
          console.log(res.data);
          this.tableData = res.data.data;
          this.total = res.data.total;
        });
    },
    changePage(cur) {
      this.pagenum = cur;
      this.getData();
    },
    handleEdit(index, row) {
            this.dialogFormVisible = true;
            // row.id  修改的目标
            this.id = row.id;
            this.form = {
                username:row.username,
                password:row.password,
                sex:row.sex
            }
            console.log(index, row);
        },
  handleDelete(index, row) {
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
        }).then(() => {
            this.$http.get('/api/del',{params:{id:row.id}}).then(res => {
                this.getData();
                this.$message({
                    type: 'info',
                    message: '删除成功'
                }); 
            })
            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消删除'
                }); 
            })
        },
        reset(){
            this.form={
                username:'',
                password:'',
                sex:''
            }
        },
      addFun(){
            //区分  添加和修改

            let url = '';

            if(this.id){
                //修改
                url = '/api/gai'
            }else{
                //添加
                url = '/api/add'
            }
            this.$http.post(url,{...this.form,id:this.id}).then(res => {
                if(res.data.code === 1){
                    this.getData();
                }
                this.$message({
                    type: 'info',
                    message: res.data.msg
                });
                this.dialogFormVisible = false;
                this.reset();
            })
        },
  },
  
     
 
  data() {
    return {
      tableData: [],
      total: 0,
      limit: 3,
      pagenum: 1,
      dialogFormVisible: false,
      formLabelWidth: "120px",
      form: {
        username: "",
        password: "",
        sex: ""
      },
      id: null
    };
  }
};
</script>
<style>
.home,
.el-container {
  width: 100%;
  height: 100%;
  overflow: hidden;
}
.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-aside {
  background-color: #d3dce6;
}

.el-main {
  background-color: #e9eef3;
}
</style>