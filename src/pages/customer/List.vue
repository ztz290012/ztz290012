<template>
    <div>
        
        <el-button type="warning" @click="toAddHandler">添加</el-button>
        <el-button type="danger">批量删除</el-button>

        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

                </template>
            </el-table-column>
        </el-table>
        <!---分页   --->
        <!-- <el-pagination
        layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!---/分页  --->
        <!---模态框   --->
        <el-dialog
            :title="title"
                :visible.sync="visible"
                width="50%">
                <el-form :model="form" label-width="80px"> 
                    <el-form-item label="用户名">
                        <el-input type="name" v-model="form.username"></el-input>
                    </el-form-item>
                    <el-form-item label="密码">
                        <el-input type="password" v-model="form.password"></el-input>
                    </el-form-item>
                    <el-form-item label="真实姓名">
                        <el-input  v-model="form.realname"></el-input>
                    </el-form-item>
                    <el-form-item label="手机号">
                        <el-input  v-model="form.telephone"></el-input>
                    </el-form-item>
                </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closeModalHandler" size="smell">取 消</el-button> <!---@是事件的绑定   --->
                <el-button type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!--- /模态框   --->
    </div>

</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //存放要在页面中要调用的方法
    
    methods:{
        loadData(){
            let url= "http://localhost:6677/customer/findAll"
                request.get(url).then((response)=>{
        //将查询结果设置到customers
        this.customers=response.data;
    })
        },
        toDeleteHandler(){
            //确认
            
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        }).then(() => {
            //调用后台接口完成删除操作
            let url="http://localhost:6677/customer/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                this.$message({
                type: 'success',
                message: response.message
            });    
            })
        })
        },
        toUpdateHandler(row){
            //显示当前行的信息
            this.title="修改员工信息";
            this.visible=true;
            this.form=row;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.title="添加信息"
            this.visible=true;
            this.form={
                type:"customer"
            }
        },
        submitHandler(){
            //通过request交互
            let url="http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Contet-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)//转换为查询字符串

           }).then(()=>{
               //关闭模态框
               this.closeModalHandler();
               this.$message({
                   type:"success",
                   message:response.message
                })
               this.loadDate();
           })
        }
    },
        
    
    //存放要在网页中显示的数据
    data(){
        return{
            title:'录入员工信息',
            visible:false,
            customers:[ ],
            form:{
                type:"customer"
            }
        }
    },
    created(){
      //vue文档加载完毕后
      this.loadData();
    }
}
</script>
<style scoped>
    
</style>