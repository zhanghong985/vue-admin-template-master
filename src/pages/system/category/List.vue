<template>
<div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler"> 添加</el-button>
    <el-button type="danger" size="small"> 批量删除 </el-button>
    <!-- 按钮结束 -->
    <!-- 表格 -->
<el-table :data="product">
    
    <el-table-column prop="id" label="编号"></el-table-column>
    <el-table-column prop="name" label="栏目名称"></el-table-column>
    <el-table-column prop="num" label="序号"></el-table-column>
    <el-table-column prop="parentId" label="父栏目"></el-table-column>
    <el-table-column  fixed="right" label="操作">
        <template v-slot="slot">
           <a href=""  @click.prevent="toDeleteHandler(slot.row.id)"> 删除 </a>
           <a href=""  @click.prevent="toUpdateHandler"> 修改</a>
        </template>
    </el-table-column>
</el-table>
<!-- 表格结束 -->
<!-- 模态框 -->
<el-dialog 
:title.sync="title"  
:visible.sync="visible" 
width="60%">

    <el-form :model="form" label-width="80px">

             <el-form-item label="编号">
                 <el-input v-model="form.id"/>                
             </el-form-item>

             <el-form-item label="栏目名称">               
                 <el-input  v-model="form.name"/>
             </el-form-item>
             
             <el-form-item label="序号" >
                 <el-input v-model="form.num"/>                
             </el-form-item>
               <el-form-item label="所属栏目">
                    <el-select v-model="form.status" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" :label="item.name"
                            :value="item.parentId">
                        </el-option>
                    </el-select>
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
     data(){
        return{
            visible:false,
              product:[],
            form:{
                
            }
        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },

    methods:{
        submitHandler(){
          let url = "http://localhost:6677/category/saveOrUpdate";
          //前端向后台发送请求，完成数据保存操作
      request({
        url,
        method:"POST",
        //告诉给后台我的请求体中放的是查询字符串
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        //请求体中的数据，将this.form转换为查询字符串发送给后台
          data:querystring.stringify(this.form)
      }).then((response)=>{
          this.closeModalHandler();
          this.loadData();
          this.$message({type:"success",message:response.message});
          
      })
      
        },
        
        //重载员工数据
        loadData(){
          //this->vue实例,通过vue实例访问该实例中的数据
          //this.title/this.toAddHandler
                let url="http://localhost:6677/category/findAll"
                request.get(url).then((response)=>{
                    this.product=response.data;
                })           
        },
        toAddHandler(){
              let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
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
        toUpdateHandler(row){
            this.title="修改栏目信息";
            this.visible=true;

        },
         closeModalHandler(){
            this.visible=false;
        }
         },
   
   
}

</script>
<style  scoped>

</style>