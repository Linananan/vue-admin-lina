<template>
    <div>
        <!--按钮-->
        <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger" >批量删除</el-button>
        <!--按钮结束-->
        <!--表格-->
        <el-table :data="categories">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="数量"></el-table-column>
            <el-table-column prop="parentId" label="id"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!--表格结束-->
        <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination>
        <!--模态框-->
        <el-dialog 
        :title="title"
        :visible.sync="visible"
        width="%60" >
           <el-form lable-width="80px">
               <el-form-item label="编号">
                   <el-input v-model="form.id"/>
                </el-form-item>
               <el-form-item label="栏目名称">
                   <el-input v-model="form.name"/>
                </el-form-item>
               <el-form-item label="数量">
                   <el-input v-model="form.price"/>
               </el-form-item>
               <el-form-item label="id">
                   <el-input v-model="form.description"/>
               </el-form-item>
           </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button  @click="closeModule">取消</el-button>
                <el-button type="primary" @click="submitHandler">确定</el-button>
            </span>
        </el-dialog>
        <!--模态框结束-->
    </div>
</template>
<script>
import request from '@/utils/request'//@表示src第三方库
import querystring from 'querystring'//系统库不需要加路径
export default {
    created(){
        this.loadData();
    },
    data(){
        return{
            title:"录入信息",
            visible:false,
            products:[],
            form:{
                type:"category"
            }
        }
    },
    menthods:{
        loadData(){
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                //将查询结果设置到customer中
                this.categories=response.data;
            })
        },
        toDeleteHandler(){
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
            this.title="修改信息";
            this.visible=true;
        },
        submitHandler(){
            let url="http://localhost:6677/category/findAll"
            request({
                url,
                method:"POST",
                henders:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束,关闭模态框,提示消息
                //this.visible=false;
                this.closeModule();
                // 刷新
                this.loadData();
                // 提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        closeModule(){
            this.visible=false;
        },
        toAddHandler(){
            this.title="录入信息";
            this.visible=true;
        }
    }
}
</script>
<style scoped>

</style>