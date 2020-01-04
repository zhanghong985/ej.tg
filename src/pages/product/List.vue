<template>
    <div>   
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="products">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="description" type="textarea" label="描述"></el-table-column>
            <el-table-column prop="categoryId" label="所属栏目"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页结束 -->
        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="50%">
          
            <el-form :model="form" label-width="80px">


                <el-form-item label="产品名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>

                <el-form-item label="价格">
                    <el-input v-model="form.price"></el-input>
                </el-form-item>
               
               <el-form-item label="描述">
                    <el-input v-model="form.description"></el-input>
                </el-form-item>

                <el-form-item label="所属栏目">
                    <el-select v-model="form.categoryId">
                        <el-option v-for="item in options"
                        :key="item.id"
                        :label="item.name"
                        :value="item.id"
                        ></el-option>
                    </el-select>
                </el-form-item>
               
               
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModelHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- /模态框 -->
    </div>
</template>


<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
       loadCategory(){
            let url ="http://localhost:6677/category/findAll";
            request.get(url).then((response)=>{
                this.options = response.data;
            })
        },
        //加载页面
        loadData(){
            let url ="http://localhost:6677/product/findAll";
            request.get(url).then((response)=>{
                this.products = response.data;
            })
        },
        submitHandler(){
            let url = "http://localhost:6677/product/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModelHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
            
        },
        toAddHandler(){
            this.form= {}  
            this.visible=true;
        },
        closeModelHandler(){
            this.visible=false;
        },
        toUpdataHandler(){
            this.form=row;
            this.visible=true;
        },
         toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //调用后台接口，完成删除操作
        let url="http://localhost:6677/product/deleteById?id="+id;
        request.get(url).then((response)=>{
          //刷新数据
          this.loadData();
          //提示结果
              this.$message({
              type: 'success',
              message:response.message
        });
        })

        
      })
      
    }
    },
    data(){
        return{
            visible:false,
            products:[],
            options:[],
            form:{}
        }
    },
    created(){
        this.loadData();
        this.loadCategory();
    }
}
</script>

<style scoped>

</style>


 