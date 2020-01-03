<template>
    <div>
        <el-button type="warning" @click="toAddHandler">添加</el-button>
        <el-button type="danger">批量删除</el-button>

        <el-table :data="category">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="项目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>

                </template>
            </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-pagination
        layout="prev, pager, next"
            :total="50">
        </el-pagination>
        <!-- 模态框 -->
        <el-dialog
                :title="title"
                :visible.sync="visible"
                width="50%">
                <el-form :model="form" label-width="80px"> 
                    <el-form-item label="栏目名称">
                        <el-input type="name" v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="序号">
                        <el-input v-model="form.num"></el-input>
                    </el-form-item>
                </el-form>
                <span slot="footer" class="dialog-footer">
                <el-button @click="closeModalHandler" size="smell">取 消</el-button> <!---@是事件的绑定   --->
                <el-button type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
            let url= "http://localhost:6677/category/findAll"
                request.get(url).then((response)=>{
        //将查询结果设置到customers
        this.category=response.data;
    })
        },
        toDeleteHandler(){
            //确认
            
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
        toUpdateHandler(){
            this.title="修改栏目信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.title="添加栏目信息"
            this.visible=true;
        },
        submitHandler(){
            //通过request交互
            let url="http://localhost:6677/category/saveOrUpdate"
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
            title:'项目管理',
            visible:false,
            category:[ ],
            form:{
                type:"category"
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