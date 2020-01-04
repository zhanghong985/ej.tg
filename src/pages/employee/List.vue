<template>
<!-- 员工管理 -->
    <div>
        <!--按钮-->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
        <!--/按钮-->
        
        <!--表格-->
        <el-table :data="employee">
            <el-table-column fixed="left" label="编号" prop="id"></el-table-column>
            <el-table-column fixed="left" label="用户名" prop="realname"></el-table-column>
            <el-table-column label="性别" prop="gender"></el-table-column>
            <el-table-column width="120" label="手机号" prop="telephone"></el-table-column>
            <el-table-column width="200" label="身份证号" prop="idCard"></el-table-column>
            <el-table-column width="200" label="银行卡号" prop="bankCard"></el-table-column>
            <el-table-column fixed="right" label="操作">
                  <template v-slot="slot">
                      <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                      <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

                  </template>
                  </el-table-column>           
        </el-table>
        <!--/表格结束-->

        <!--分页开始-->
        <!-- <el-pagination layout="prev,pager,next" :total="50"></el-pagination> -->
        <!--/分页结束-->

        <!--模态框-->
        <el-dialog :title.sync="title"  :visible.sync="visible" width="60%">

           <el-form :model="form" label-width="80px">

             <el-form-item label="用户名">
                 <el-input v-model="form.realname"/>                
             </el-form-item>

             <el-form-item label="密码">               
                 <el-input type="password" v-model="form.password"/>
             </el-form-item>
             
             <el-form-item label="姓名" >
                 <el-input v-model="form.username"/>                
             </el-form-item>

              <el-form-item label="性别">
                  <el-radio-group v-model="form.gender">
                      <el-radio label="男"></el-radio>
                      <el-radio label="女"></el-radio>
                  </el-radio-group>                         
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

         </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="closeModalHandler">取 消</el-button>
            <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
             </span>
        </el-dialog>
        <!--/模态框-->

    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    data(){
        return{
            visible:false,
            employee:[],
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
          let url = "http://localhost:6677/waiter/saveOrUpdate";
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
                let url="http://localhost:6677/waiter/findAll"
                request.get(url).then((response)=>{
                    this.employee=response.data;
                })           
        },

        toAddHandler(){
            this.title="添加员工信息";
            this.visible=true;
        },

        toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
        //调用后台接口，完成删除操作
        let url="http://localhost:6677/customer/deleteById?id="+id;
        request.get(url).then((response)=>{
          //刷新数据
          this.loadData();
          //提示结果
              this.$message({
              type: 'success',
              message:response.message
        })
        })

        
      })
      
    },

        toUpdateHandler(row){
           this.form=row;
            this.visible=true;

        },

        closeModalHandler(){
            this.visible=false;
        }
    }
}
</script>
<style scoped>

</style>
