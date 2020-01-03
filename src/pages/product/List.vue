<template>
    <div>
        <h2>产品管理</h2>
        <el-button type="primary" size="small" icon="el-icon-edit-outline" @click="toAddHandler" round>添加</el-button>
        <el-button type="danger" size="small" icon="el-icon-delete" round>批量删除</el-button>
        <el-table :data="products">
            <el-table-column width=30><el-checkbox v-model="checked"></el-checkbox></el-table-column>
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="categoryId" label="所属产品"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--     分页     -->
        <el-pagination layout="prev, pager, next" :total="50">
         </el-pagination>
         <!--     模态框     -->
         <el-dialog title="录入产品信息" :visible.sync="visible"
          width="60%" >
             <el-form :v-model="form" label-width="80px">
                 <el-form-item label="产品名称">
                     <el-input v-model="form.name"></el-input>
                 </el-form-item>
                  <el-form-item label="价格">
                     <el-input v-model="form.price"></el-input>
                 </el-form-item>
                 <el-form-item label="描述">
                     <el-input v-model="form.description"></el-input>
                 </el-form-item>
                 <el-form-item label="所属产品">
                     <el-input v-model="form.categoryId"></el-input>
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
             let url = "http://localhost:6677/product/findAll"
        request.get(url).then((response) =>{
            //将查询结果设置到customers中，this指向外部函数的this
            this.products = response.data;
        })
        },
        submitHandler(){
            //this.form 对象 ---字符串--->后台；
            //通过request与后台进行交互,携带参数;
            let url="http://localhost:6677/product/saveOrUpdate"
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
          this.$message({
            type: 'success',
            message: '删除成功!'
          });
        })
        },
        toUpdateHandler(){
            this.visible= true;
        },
        closeModalHandler(){
            this.visible= false;
        },
        toAddHandler(){
            this.visible= true;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false, 
            products:[],
            form:{
                type: 'products'
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

