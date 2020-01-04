<template>
    <div>
    <h3>员工管理</h3>
        <el-button size="small" type="primary" @click="AddHandler">添加</el-button>
        <el-button size="small" type="danger">删除</el-button>
        <el-table :data="employees">
            <el-table-column label="编号" prop="id" fixed="left"></el-table-column>
            <el-table-column label="姓名" prop="realname" fixed="left"></el-table-column>
            <el-table-column label="手机号" prop="telephone" width="120"></el-table-column>
            <el-table-column label="身份证号" prop="idCard" width="170"></el-table-column>
            <el-table-column label="银行卡号" prop="bankCard" width="170"></el-table-column>
            <el-table-column label="注册时间" prop="registerTime" width="130"></el-table-column>
            <el-table-column label="状态" prop="status"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                  <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                  <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
              </template>
            </el-table-column>
        </el-table>
        <!-- 模态框 -->
       <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            {{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="姓名">
                    <el-input v-model="form.realname"/>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"/>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"/>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"/>
                </el-form-item>
                <el-form-item label="状态">
                    <el-input v-model="form.status"/>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="closerModalHandler">取 消</el-button>
            <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from "querystring"
export default {
    data(){
        return{
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
       }
    },
    created(){
        this.loadData();
    },
    methods:{
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url="http://localhost:6677/waiter/deleteById?id="+id;
            request.get(url).then((response)=>{
              //1.刷新数据
              this.loadData();
              //2.提示结果
              this.$message({
              type: 'success',
              message:response.message
            });
            })
        })
        },
        toUpdateHandler(row){
            this.title="修改员工信息"
            this.visible=true;
            this.form=row;
        },
        loadData(){
            let url="http://localhost:6677/waiter/findAll";
            request.get(url).then((response)=>{
                this.employees=response.data;
            })
        },
        submitHandler(){
            let url="http://localhost:6677/waiter/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"//提示后台传入查询字符串
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
             //模态框关闭
             this.closerModalHandler();
             //刷新 
             this.loadDate();
             //提示消息
             this.$message({
                type:"success",
                message:response.message 
             })
            })
        },
        AddHandler(){
            this.form={
                type:"customer"
            };
            this.title="录入员工信息";
            this.visible=true;
        },
        closerModalHandler(){
            this.visible=false;
        }
    }
}
</script>

<style scoped>
</style>