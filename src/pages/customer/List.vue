<template>
    <div>
        <!-- 按钮 --->
        <el-button type="success" @click="toAddHandler" size="small">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- 按钮结束-->
        <!-- 表格-->
        <el-table :data="customers">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="username" label="姓名"></el-table-column>
            <el-table-column label="年龄"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                    <!--{{}}用来打印vue中的变量-->
                </template>
            </el-table-column>
        </el-table>
        <!--表格结束-->
        <!--分页开始 -->
        <!-- <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!-- 分页结束>
        <! -- 对话 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            <!-- ---{{form}}   --->
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closeModule">取 消</el-button>
                <el-button type="primary" @click="submitHandler" >确 定</el-button>
            </span>
        </el-dialog>
        <!--对话结束-->
    </div>
</template>
<script>
import request from '@/utils/request'//@表示src
import querystring from 'querystring'
//暴露接口
export default {
    //this为当前vue实例
    //用于存放要向网页中存放的数据
    created(){
        //文档加载完毕要执行的操作,vue实例创建完毕
        this.loadData();
    },
    data(){
        return{
            title:"录入用户信息",
            visible:false,
            customers:[],
            form:{
                type:"customer"
            }
        }
    },
    
    //用于存放网络中需要调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
                //将查询结果设置到customer中
                this.customers=response.data;
            })
        },
        submitHandler(){
            //this.form 对象 ---字符串----> 后台'{"type":"customer","age":12}'
            //json字符串'{"type":"customer","age":12}'
            // 查询字符串 type=customer&age=12
            //通过request与后台进行交互，并且要携带参数
            let url="http://localhost:6677/customer/saveOrUpdate"
            //reques.post(url,this.form)
            request({
                url,
                method:"POST",
                henders:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //调用后台接口完成删除操作
               
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
        toAddHandler(){
            this.form={
                type:"customer"  //原始的customer界面
            }
            this.title="录入用户信息"
            this.visible=true;
        },
        closeModule(){
            this.visible=false;
        },
        toUpdateHandler(row){
            //模态框的表单中显示出当前行的信息
            this.form=row;
            this.title="修改用户信息";
            this.visible=true;
        },
        toDeleteHandler(id){
            //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => { 
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
        }
    }
}
</script>
<style scoped>

</style>
