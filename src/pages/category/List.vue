<template>
    <div>
        <h5>栏目管理</h5>
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="warning" size="small">批量删除</el-button>
        <el-table :data="category">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="栏目名称" prop="name"></el-table-column>
            <el-table-column label="序号" prop="num"></el-table-column>
            <el-table-column label="父栏目" prop="parentId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
               <a href="" @click.prevent="deleteHandler(slot.row.id)">删除</a>
               <a href="" @click.prevent="updataHandler(slot.row)">修改</a>
                </template>
            </el-table-column>

        </el-table>
        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="66%" >
        <el-form :model="form" label-width="80px">
        <el-form-item label="栏目名称">
          <el-input v-model="form.name" />
        </el-form-item>
        <el-form-item label="序号">
          <el-input v-model="form.num" />
        </el-form-item>
        <el-form-item label="父栏目">
                 <el-select v-model="form.categoryId">
                     <el-option v-for="item in options" :key="item.id" :label="item.name" :value="item.id"></el-option>
                 </el-select>
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
            title:'栏目管理',
            category:[],
            form:[],
            options:{}

        }
    },
   
        methods:{
             loadData(){
        let url= 'http://localhost:6677/category/findAll'
        request.get(url).then((response)=>{
            this.category=response.data

        })
        },
        loadFu(){
           let url= 'http://localhost:6677/product/findAll'
        request.get(url).then((response)=>{
            this.options=response.data

        })

        },
        updataHandler(row){
            this.visible=true;
            this.form=row;
            this.title='修改信息',
            this.loadData();
        },
        toAddHandler(){
            this.form = {
        
      }  
            this.title='录入产品信息',
            this.visible=true;

        },
        submitHandler(){
            let url = 'http://localhost:6677/category/saveOrUpdate'
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
                  message:response.message+'栏目测试代码'
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
        let url="http://localhost:6677/category/deleteById?id="+id;
        request.get(url).then((response)=>{
          this.loadData();
            this.$message({
          type: 'success',
          message: response.message
        });
        })
      
      })
        }
      },

     created(){
            this.loadData(),
            this.loadFu()

        }
    
    
}
</script>