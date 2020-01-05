<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="products">
            <el-table-column label="编号" prop="id"  fixed="left"> </el-table-column>
            <el-table-column label="清洁件名称" prop="name"  fixed="left"> </el-table-column>
            <el-table-column label="描述" prop="description" width="200"> </el-table-column>
            <el-table-column label="价格" prop="price" fixed="left"> </el-table-column>
            <el-table-column label="状态" prop="status" > </el-table-column>
            <el-table-column label="图片" prop="photo"> </el-table-column>
            <el-table-column label="栏目" prop="categoryId"> </el-table-column>
            <el-table-column label="操作" fixed="right" > 
             
                <template v-slot="slot">
                    <a href="" @click.prevent="todeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toupdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--/表格-->
        <!--分页-->
         <el-pagination
         layout="prev, pager, next"
        :total="50">
       </el-pagination>
       <!--/分页-->
       <!--模式框-->
       <el-dialog
         :title="title"
         :visible.sync="visible"
         width="60%">
         <el-form :model="form" label-width="80px">
             <el-form-item label="编号">
                 <el-input v-model="form.id"></el-input>
             </el-form-item>
             <el-form-item label="清洁件名称">
                 <el-input v-model="form.name" ></el-input>
             </el-form-item>
             <el-form-item label="描述">
                 <el-input v-model="form.description"></el-input>
             </el-form-item>
             <el-form-item label="价格">
                 <el-input v-model="form.price"></el-input>
             </el-form-item>
             <el-form-item label="状态">
                 <el-input v-model="form.status"></el-input>
             </el-form-item>
             <el-form-item label="图片">
                 <el-input v-model="form.photo"></el-input>
             </el-form-item>
             <el-form-item label="栏目">
                 <el-select v-model="form.categoryId">
                     <el-option v-for="item in options" :key="item.id" :label="item.name" :value="item.id"></el-option>
                 </el-select>
             </el-form-item>
            
         </el-form>
           ---{{form}}
         <span slot="footer" class="dialog-footer">
         <el-button @click="closeModalHander" size="small">取 消</el-button>
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
        </span>
        </el-dialog>
        <!--/模式框-->
        </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
//import { type } from 'os'
export default {
    //用于存放网页中用于调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
            this.products=response.data;
        })
        },
        submitHandler(){
    
            //通过request与后台进行交互，并且携带参数
             let url="http://localhost:6677/product/saveOrUpdate"
             request({
                 url,
                 method:"POST",
                 headers:{
                     "Content-Type":"application/x-www-form-urlencoded"
                 },
                 data:querystring.stringify(this.form)
             }).then((response)=>{
                 //请求结束
                 this.closeModalHander();
                 this.$message({
            type: 'success',
            message:response.message
          });
            this.loadData();
             })
    
        },
        todeleteHandler(id){
            //确认
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/product/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新sju 
                  this.loadData();
                 this.$message({
                type: 'success',
                message: '删除成功!'+id
            });
          
            });
           
            })
            },
        toupdateHandler(row){
           this.title="修改产品信息"
            this.form=row;
            this.visible=true;
        },
        toAddHandler(){
          this.title="添加产品信息"
            this.form={
                type:"product"
            };
            this.visible=true;
        },
        loadCategory(){
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
            this.options=response.data;
        })

        },
        
        closeModalHander(){
            this.visible=false;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            products:[],
            form:{},
            options:{}

        }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕
        this.loadData();
        this.loadCategory();
        
    }
}
</script>

<style scoped>

</style>