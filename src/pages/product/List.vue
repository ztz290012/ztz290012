<template>
    <div>
        <div>
        <el-button type=info @click="toAddHandler">添加</el-button>
        <el-button type=warning>批量删除</el-button>
        </div>
        <el-table :data="product">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="categoryId" label="所属产品"></el-table-column>
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
                    <el-form-item label="名称">
                        <el-input type="name" v-model="form.name"></el-input>
                    </el-form-item>
                    <el-form-item label="价格">
                        <el-input v-model="form.price"></el-input>
                    </el-form-item>
                    <el-form-item label="所属栏目">
                    <el-select v-model="form.status" placeholder="请选择">
                        <el-option v-for="item in options"
                            :key="item.value" 
                            :label="item.name"
                            :value="item.parentId">
                        </el-option>
                    </el-select>
                </el-form-item>
                    <el-form-item label="介绍">
                        <el-input v-model="form.description"></el-input>
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
          toAddHandler(){
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.options = response.data;
            })
            this.title="添加产品信息",
            this.visible=true;
        },
        loadData(){
            let url= "http://localhost:6677/product/findAll"
                request.get(url).then((response)=>{
        //将查询结果设置到
        this.product=response.data;
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
            this.title="修改产品信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        submitHandler(){
            //通过request交互
            let url="http://localhost:6677/product/saveOrUpdate"
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
            title:'产品管理',
            visible:false,
            product:[ ],
            form:{
                type:"product"
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