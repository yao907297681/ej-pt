<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="addresss">
            <el-table-column label="编号" prop="id"  fixed="left"> </el-table-column>
            <el-table-column label="省" prop="province"  fixed="left"> </el-table-column>
            <el-table-column label="市" prop="city" fixed="left"> </el-table-column>
            <el-table-column label="县" prop="area" fixed="left"> </el-table-column>
            <el-table-column label="详细地址" prop="address" fixed="left" width="200" > </el-table-column>
            <el-table-column label="联系方式" prop="telephone"> </el-table-column>
            <el-table-column label="员工号" prop="customerId"> </el-table-column>
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
             <el-form-item label="省">
                 <el-input v-model="form.province" ></el-input>
             </el-form-item>
             <el-form-item label="市">
                 <el-input v-model="form.city"></el-input>
             </el-form-item>
             <el-form-item label="县">
                 <el-input v-model="form.area"></el-input>
             </el-form-item>
             <el-form-item label="详细地址">
                 <el-input v-model="form.address"></el-input>
             </el-form-item>
             <el-form-item label="联系方式">
                 <el-input v-model="form.telephone"></el-input>
             </el-form-item>
             <el-form-item label="员工号">
                 <el-input v-model="form.customerId"></el-input>
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
            let url="http://localhost:6677/address/findAll"
            request.get(url).then((response)=>{
            this.addresss=response.data;
        })
        },
        submitHandler(){
    
            //通过request与后台进行交互，并且携带参数
             let url="http://localhost:6677/address/saveOrUpdate"
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
            let url="http://localhost:6677/address/deleteById?id="+id;
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
           this.title="修改地址信息"
            this.form=row;
            this.visible=true;
        },
        toAddHandler(){
          this.title="添加地址信息"
            this.form={
                type:"address"
            };
            this.visible=true;
        },
        
        closeModalHander(){
            this.visible=false;
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            addresss:[],
            form:{}

        }
    },
    created(){
        //this为当前vue实例对象
        //vue实例创建完毕
        this.loadData();
        
    }
}
</script>

<style scoped>

</style>