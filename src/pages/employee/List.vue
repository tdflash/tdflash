<template>
    <div>
        <el-button type="primary" size="small" icon="el-icon-edit-outline" @click="toAddHandler" round>添加</el-button>
        <el-button type="danger" size="small" icon="el-icon-delete" @click="toDeleteSomeHandler" round>批量删除</el-button>
    <!-- 表格 -->
    <el-table :data="employees">
        <el-table-column label="编号" prop="id" width=50> </el-table-column>
        <el-table-column label="用户名" prop="username"> </el-table-column>
        <el-table-column label="真实姓名" prop="realname"> </el-table-column>
        <el-table-column label="性别" prop="gender"> </el-table-column>
        <el-table-column label="手机号" prop="telephone"> </el-table-column>
        <el-table-column label="QQ号" prop="qq"> </el-table-column>
        <el-table-column label="身份证" prop="idCard" width=200> </el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
    </el-table>
    <!-- 分页 -->
    <el-pagination layout="prev, pager, next" :total="50">
    </el-pagination>
    <!-- 模态框 -->
        <el-dialog
         :title="title"
         :visible.sync="visible"
          width="60%" >
             <el-form :v-model="form" label-width="80px">
                <el-form-item label="用户名">
                     <el-input v-model="form.username">
                     </el-input>
                 </el-form-item>
                 <el-form-item label="真实姓名">
                     <el-input v-model="form.realname">
                     </el-input>
                 </el-form-item>
                  <el-form-item label="性别">
                     <el-radio-group v-model="form.gender">
                     <el-radio label="男">男</el-radio>
                     <el-radio label="女">女</el-radio>
                     </el-radio-group>
                 </el-form-item>
                 <el-form-item label="身份证">
                     <el-input v-model="form.id_card">
                     </el-input>
                 </el-form-item>
                 <el-form-item label="手机号">
                     <el-input v-model="form.telephone">
                     </el-input>
                 </el-form-item>
                 <el-form-item label="QQ号">
                     <el-input v-model="form.qq">
                     </el-input>
                 </el-form-item>
             </el-form>    
             <span slot="footer" class="dialog-footer">
             <el-button @click="closeModalHandler">取消</el-button>
             <el-button type="primary" @click="submitHandler">确定</el-button>
             </span>
        </el-dialog>
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring' //querystring 是系统库不需加路径，第三方库需要加路径
export default {
  data() {
    return {
      title: '录入员工信息',
      visible : false,
          employees:[],
          form:{
                type: 'waiter'
            }
        }
    },
    methods:{
        submitHandler(){
            //this.form 对象 ---字符串--->后台；
            //通过request与后台进行交互,携带参数;
            let url="http://localhost:6677/waiter/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form) 
            }).then((response) =>{
                //模态框关闭
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        loadData(){
             let url = "http://localhost:6677/waiter/findAll"
        request.get(url).then((response) =>{
            //将查询结果设置到customers中，this指向外部函数的this
            this.employees = response.data;
        })
        },
        toDeleteSomeHandler(){
            this.title="批量删除员工信息";
            this.visible=true;
        },
        toUpdateHandler(row){
            this.title="修改员工信息";
            this.visible=true;
        },
        toDeleteHandler(id){
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
        this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.title ="添加员工信息";
            this.visible = true;

    }
  },
  created(){
        //页面加载出来时加载数据
        this.loadData();
    }
}
</script>

<style scoped>

</style>