<template>
    <div>
        <h3>订单管理</h3>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="success" size="small">批量删除</el-button>
         <!-- 表格 -->
        <el-table :data="orders">
            <el-table-column prop="id" label="订单编号"></el-table-column>
            <el-table-column prop="orderTime" label="订单时间"></el-table-column>
            <el-table-column prop="total" label="订单数量"></el-table-column>
            <el-table-column prop="status" label="订单状态"></el-table-column>
            <el-table-column prop="customerId" label="顾客编号"></el-table-column>
            <el-table-column prop="waiterId" label="员工编号"></el-table-column>
            <el-table-column prop="addressId" label="地址编号"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>  
        </el-table>
        <!-- 表格结束 -->
        <!-- 模态框 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            ---{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="订单时间">
                    <el-input v-model="form.orderTime"></el-input>
                </el-form-item>
                <el-form-item label="订单数量">
                    <el-input v-model="form.tatal"></el-input>
                </el-form-item>
                <el-form-item label="订单状态">
                    <el-input v-model="form.status"></el-input>
                </el-form-item>
                <el-form-item label="顾客编号">
                    <el-input v-model="form.customerId"></el-input>
                </el-form-item>
                <el-form-item label="员工编号">
                    <el-input v-model="form.waiterId"></el-input>
                </el-form-item>
                <el-form-item label="地址编号">
                    <el-input v-model="form.addressId"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="closeModalHandler">取 消</el-button>
            <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 模态框结束 -->
    </div>
</template>     
      
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url = "http://localhost:6677/order/findAll";
            request.get(url).then((response)=>{
            //将查询结果设置到orders中，箭头函数中的this指向外部函数实例
            this.orders = response.data;
        })
        },
        submitHandler(){
            //this.form 对象---字符串--> 后台
            //json'{"type":"customer","age":12}'
            //request.post(url,this.form)
            //查询字符串 type=customer&age=12
            //通过request与后台进行交互，并且要携带参数
            let url = "http://localhost:6677/order/save";
            request({    //()表示方法调用
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束，模态框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        toAddHandler(){
            //将form变为初始值
            this.form={
                type:"orders"
            }
            this.title="录入订单信息";
            this.visible=true;
            
        },
        toDeleteHandler(id){
            //确认
          alert(id);
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
            let url = "http://localhost:6677/order/deleteById?id="+id;
            request.get(url).then((response)=>{
                //1.刷新数据
                this.loadData();
                //2.显示结果
                this.$message({
                type: 'success',
                message:response.message
          });
            })
          
        })

        },
        toUpdateHandler(row){
            this.title="修改订单信息";
            
            //模态框的表单中显示当前行的数据
            this.visible=true;
            this.form=row;
            
        },
        closeModalHandler(){
            this.visible=false;
        },
        
    },
    //用于存放要向网页中显示的内容
    data(){
        return{
            //title:"录入订单信息",
            visible:false,
            orders:[],
            form:{
                type:"order"
            }
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