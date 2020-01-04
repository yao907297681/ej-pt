<template>
    <div>
        <h5>评论管理</h5>
        <div>{{todayDate}}</div>
        <el-button type="primary" @click="toAddHandler">点击评论</el-button>
        <el-button type="warning">批量删除</el-button>
        <el-table :data="comment">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="评论内容" prop="content"></el-table-column>
            <!-- <el-table-column label="评论时间" prop="commentTime"></el-table-column> -->
            <el-table-column label="所评订单" prop="orderId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
               <a href="" @click.prevent="deleteHandler(slot.row.id)">删除</a>
               <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="66%" >
        <el-form :model="form" label-width="80px">
        <el-form-item label="评论内容">
          <el-input v-model="form.content" />
        </el-form-item>
        <!-- <el-form-item label="时间">
          <el-input v-model="form.commentTime" />
        </el-form-item> -->
        <el-form-item label="订单编号">
          <el-input v-model="form.orderId" />
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
         <el-button type="warning" @click="visible=false" size="small">取 消</el-button>   
         </span>
    </el-dialog>

    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            visible:false,
            title:'评论管理',
            comment:[],
            form:[],
            todayDate: "",

        }
    },
   
        methods:{
             loadData(){
        let url= 'http://localhost:6677/comment/findAll'
        request.get(url).then((response)=>{
            this.comment=response.data

        })
        },
        toAddHandler(){
            this.form = {
       
      }
            this.title='记录评论信息',
            this.visible=true;

        },
        submitHandler(){
            let url = 'http://localhost:6677/comment/saveOrUpdate'
            request({
             url,
               method:"POST",
               headers:{
              "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
              // 模态框关闭
             this.closerModalHandler();
           
             // 刷新
               this.loadData();
                // 提示消息
                this.$message({
                  type:"success",
                  message:response.message+'评论测试代码'
                })
            })

        },
        closerModalHandler(){
            this.visible=false;
        },
        deleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => { 
              let url="http://localhost:6677/comment/deleteById?id="+id;
              request.get(url).then((response)=>{
                this.loadData();
                  this.$message({
                type: 'success',
                message: response.message
              });
              })
            
            });
              
        },
        toUpdataHandler(row){
          this.title='修改信息',
          this.visible=true,
          this.form=row;
        },
        nowTime() {
            let myDate = new Date();
            let year = myDate.getFullYear();
            let month = myDate.getMonth() + 1;
            let date = myDate.getDate();
            let hours = myDate.getHours();
            let minutes = myDate.getMinutes();
            let seconds = myDate.getSeconds();
            var week = "星期" + "日一二三四五六".charAt(myDate.getDay());
            hours = this.check(hours);
            minutes = this.check(minutes);
            seconds = this.check(seconds);
            this.todayDate =
                hours + ":" + minutes+'  '+ week +'  '+ month+'/'+date+'/'+year;
            },
        //  检验时间补零的方法
        check(i) {
            let num;
            i < 10 ? (num = "0" + i) : (num = i);
            return num;
            },

    //方法结束
        },
        created(){
            setInterval(this.nowTime, 1000);
            this.loadData()

        }
    
    
}
</script>