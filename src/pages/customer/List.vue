<template>
    <div>
        <!--     按钮     -->
        <el-button type="primary" size="small" icon="el-icon-edit-outline" @click="toAddHandler" round>添加</el-button>
        <el-button type="danger" size="small" icon="el-icon-delete" round>批量删除</el-button>
        <!--     表格     -->
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="username" label="用户名"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                 <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)"><i class="el-icon-delete"></i></a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)"><i class="el-icon-edit"></i></a>
                </template>
            </el-table-column>
        </el-table>
        <!--     分页     -->
        <el-pagination layout="prev, pager, next" :total="50">
         </el-pagination>
         <!--     模态框     -->
         <el-dialog title="录入顾客信息" :visible.sync="visible"
          width="60%" >
             <el-form :v-model="form" label-width="80px">
                 <el-form-item label="用户名">
                     <el-input v-model="form.username"></el-input>
                 </el-form-item>
                  <el-form-item label="密码">
                     <el-input type="password" v-model="form.password"></el-input>
                 </el-form-item>
                 <el-form-item label="真实姓名">
                     <el-input v-model="form.realname"></el-input>
                 </el-form-item>
                 <el-form-item label="手机号">
                     <el-input v-model="form.telephone"></el-input>
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
import request from '@/utils/request' //@代表src;
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法；
    methods:{
        loadData(){
             let url = "http://localhost:6677/customer/findAll"
        request.get(url).then((response) =>{
            //将查询结果设置到customers中，this指向外部函数的this
            this.customers = response.data;
        })
        },
        submitHandler(){
            //this.form 对象 ---字符串--->后台；
            //通过request与后台进行交互,携带参数;
            let url="http://localhost:6677/customer/saveOrUpdate"
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
        toDeleteHandler(id){
         //确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/customer/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                this.$message({
                    type: 'success',
                    message: '删除成功!'
                });
            });
        })
        },
        toUpdateHandler(row){
            //模态框显示当前信息；
            this.form=row;
            this.visible= true;
        },
        closeModalHandler(){
            this.visible= false;
        },
        toAddHandler(){
            this.form={
                type:"customers"
            }
            this.visible= true;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false, 
            customers:[],
            form:{
                type: 'customers'
            }
        }
    },
    created(){
        //vue实例创建完毕
        this.loadData();
    }
}
</script>

<style scoped>

</style>

