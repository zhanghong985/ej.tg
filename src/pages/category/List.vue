<template>
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
        <!--/按钮-->

        <!--表格-->
        <el-table :data="categorys">
            <el-table-column  label="编号" prop="id"></el-table-column>
            <el-table-column  label="栏目名称" prop="name"></el-table-column>
            <el-table-column  label="序号" prop="num"></el-table-column>
            <el-table-column  label="父栏目" prop="parentId"></el-table-column>
              <el-table-column fixed="right" label="操作">
                  <template v-slot="slot">
                      <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                      <a href="" @click.prevent="toUpdateHandler">修改</a>
                  </template>
                  </el-table-column>           
        </el-table>
        <!--/表格结束-->

        <!--分页开始-->
        <el-pagination layout="prev,pager,next" :total="50"></el-pagination>
        <!--/分页结束-->

        <!--模态框-->
      <el-dialog
  title="录入产品信息"
  :visible.sync="visible"
  width="60%">
  {{form}}
  <el-form :model="form" label-width="80px">
             <el-form-item label="编号">
                     <el-input type="id" v-model="form.id"/>
                 </el-form-item>
                 <el-form-item label="栏目名称">
                     <el-input type="name" v-model="form.name"/>
                 </el-form-item>
                  <el-form-item label="序号">
                     <el-input v-model="form.num"/>
                 </el-form-item>
                  <el-form-item label="父栏目">
                     <el-input v-model="form.parentId"/>
                 </el-form-item>
                 
  </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="closeModalHandler">取 消</el-button>
            <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
             </span>
        </el-dialog>
        <!--/模态框-->

    </div>
</template>
<script>
import request from'@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
       loadData(){
      let url = "http://localhost:6677/category/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.categorys = response.data;
      })
    },
      submitHandler(){
  let url = "http://localhost:6677/category/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
    data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
   toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //调用后台接口，完成删除操作
        let url="http://localhost:6677/category/deleteById?id="+id;
        request.get(url).then((response)=>{
        //1.刷新数据
        this.loadData();
        //2.提示结果
        this.$message({
              type: 'success',
              message: response.message
            });
      })
      })
   },
   toUpdateHandler(row){
     //模态框表单中显示当前行的信息
     this.form=row;
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      //将form变为初始值
      this.form={
        type:"category"
      }
      this.visible = true;
    }
  },
    //用于存放在网页中显示的数据
    data(){
        return{
            visible:false,
            categorys:[],
            form:{
              type:"category"

            }
        }
    },
    created(){
    //vue实例创建完毕
    let url="http://localhost:6677/category/findAll"
    request.get(url).then((response)=>{
      //将查询结果设置到customers中,this指向外部函数的this
      this.categorys=response.data;
    })
    }
    }
</script>
<style scoped>
</style>
