<template>
    <div>
        <!--按钮-->
        <div>
            <el-button size="small" type="primary" @click="toAddHandler">添加</el-button>
            <el-button size="small" type="danger">批量删除</el-button>
        </div>
        <!--/按钮-->
        <!--表格-->
        <el-table :data="employees">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column fixed="right" label="操作">
              <template v-slot="slot"><!--获取操作数据-->
              <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
              <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
              </template>
            </el-table-column>
        </el-table>
        <!--/表格-->
        <!--分页-->
            <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!--/分页-->
        <!--模态框-->
            <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">
      {{form}}
      <span>
         <el-form :model="form" label-width="80px">
          <el-form-item label="用户名">
            <el-input v-model="form.username"><!--v-model双向数据绑定-->
            </el-input>
          </el-form-item>
          <el-form-item label="密码">
            <el-input type="password" v-model="form.password">
            </el-input>
          </el-form-item>
            <el-form-item label="姓名">
            <el-input v-model="form.realname">
            </el-input>
          </el-form-item>
          <el-form-item label="性别">
            <el-radio-group v-model="form.gender">
            <el-radio label="6">男</el-radio>
            <el-radio label="9">女</el-radio>
            </el-radio-group>
          </el-form-item>
             <el-form-item label="电话">
            <el-input v-model="form.telephone">
            </el-input>
          </el-form-item>
          <el-form-item label="身份证号">
            <el-input v-model="form.idCard">
            </el-input>
          </el-form-item>
          <el-form-item label="银行卡号">
            <el-input v-model="form.bankCard">
            </el-input>
          </el-form-item>
        </el-form>
      </span>
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
        <!--/模态框-->
    </div>
</template>

<script>
import request from "@/utils/request"//@=src目录,第三方库需要加路径
import querystring from "querystring"//系统库，不用加路径
export default {
    methods:{
        submitHandler(){
    //页面数据的更新
    let url="http://localhost:6677/employee/saveOrUpdate"
    request({
      url,
      method:"post",
      headers:{
        "Content-Type":"application/x-www-form-urlencoded"
      },
      data:querystring.stringify(this.form)
    }).then((response)=>{
      //关闭模态框
      this.closeModalHandler();
      //刷新
      this.loadData();
      //提示消息
      this.$message({
        type:"success",
        message:request.message
      })
    })
    },
        //重载数据
        loadData(){
//vue实例创建完毕执行操作
let url="http://localhost:6677/waiter/findAll"
request.get(url).then((response)=>{
  //过滤后的结果
  this.employees = response.data  //把查询结果放置到employee中
})
    },
        toAddHandler(){//添加方法
    this.form={
      type:"employee"
    }
      this.visible = true;
    },
        closeModalHandler(){//取消方法
            this.visible = false;
        },
        toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //调用后台，完成删除 
        let url="http://localhost:6677/employee/deleteById?id="+id;//传参地址方式
        request.get(url).then((response)=>{
          //刷新
          this.loadData();
          //提示结果
            this.$message({
            type: 'success',
            message: response.message
          });
        })
        
      })     
    },
        toUpdateHandler(row){//修改方法
    //在模态框显示当前行信息
      this.form=row;
      this.visible = true;
    }
    },
    data(){
        return{
        title:"录入员工信息",
        visible:false,
        employees:[],
        form:{
        type:"employee"
      }
        }
    },
    created(){
        //在页面加载出来的时候显示数据
    this.loadData();
  }
}
</script>
<style scoped>

</style>