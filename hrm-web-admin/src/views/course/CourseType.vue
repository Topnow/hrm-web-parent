<template id="courseType">
    <section>
        <el-row style="height: 100%;border: 1px solid #DCDFE6;margin-top: 10px">
            <el-col :span="6" style="border-right: 1px solid #DCDFE6; min-height:520px;">
                <div class="grid-content bg-purple">
                    <el-tree :data="courseTypes" style="border: none" :props="defaultProps"  @node-click="handleNodeClick">
                    </el-tree>
                </div>
            </el-col>
            <el-col :span="17" style="margin-left: 10px;padding-top: 10px">
                <!--工具条-->
                <el-col :span="24" class="toolbar" style="padding-bottom: 0px;">
                    <el-form :inline="true">
                        <el-form-item>
                            <el-button type="primary" @click="handleAdd" icon="fa fa-plus">新增</el-button>
                        </el-form-item>
                    </el-form>
                </el-col>
                <!--列表-->
                <el-table :data="courseTypes1" highlight-current-row v-loading="listLoading" @selection-change="selsChange" style="width: 100%;">
                    <el-table-column type="selection" width="40">
                    </el-table-column>
                    <el-table-column type="index" width="55" label="#">
                    </el-table-column>
                    <el-table-column property="createTime"  label="创建时间" width="120" sortable>
                        <template scope="scope">
                            {{ dateTimeFormat(scope.row.createTime) }}
                        </template>
                    </el-table-column>
                    <el-table-column prop="name" label="课程名称" width="120" sortable>
                    </el-table-column>
                    <el-table-column prop="path" label="路径" width="120" sortable>
                    </el-table-column>

                    <el-table-column prop="description" label="描述" width="150" sortable>
                    </el-table-column>

                    <el-table-column label="操作" width="200">
                        <template scope="scope">
                            <el-button size="small" @click="handleEdit(scope.$index, scope.row)" icon="fa fa-pencil-square-o">编辑</el-button>
                            <el-button type="danger" size="small" @click="courseDel(scope.$index, scope.row)" icon="fa fa-times">删除</el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </el-col>
        </el-row>

        <!--新增界面-->
        <el-dialog title="新增" :visible.sync="addFormVisible" width="30%">
            <el-form :model="addForm" label-width="80px" :rules="addFormRules" ref="addForm" size="small">
                <el-form-item label="课程名称" >
                    <el-input v-model="addForm.name" auto-complete="off" placeholder="请输入课程名称"></el-input>
                </el-form-item>
                <el-form-item label="父类ID">
                    <el-select v-model="addForm.pid" clearable placeholder="请选择父类ID">
                        <el-option
                                v-for="item in pids"
                                :key="item.id"
                                :label="item.name"
                                :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="图标" >
                    <el-input v-model="addForm.logo" auto-complete="off" placeholder="请上传图标"></el-input>
                </el-form-item>
                <el-form-item label="创建时间">
                    <el-date-picker
                            v-model="addForm.createTime"
                            type="datetime"
                            placeholder="选择日期时间">
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="排序" >
                    <el-input type="number" max="500" min="1" v-model="addForm.sortIndex" auto-complete="off" placeholder="请选择排序号"></el-input>
                </el-form-item>
                <el-form-item label="路径">
                    <el-cascader
                            :options="courseTypes2"
                            separator="."
                            :props="{
                                checkStrictly : true,
                                value: 'id',
                                label: 'name',
                                children: 'children'
                            }"
                            :change-on-select="true"
                            v-model="addForm.path"
                            clearable
                            placeholder="请选择路径">
                    </el-cascader>
                </el-form-item>
                <el-form-item label="描述">
                    <textarea v-model="addForm.description" auto-complete="off"  rows="3" cols="20" ></textarea>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click.native="addFormVisible = false" icon="el-icon-error">取消</el-button>
                <el-button type="primary" @click.native="addSubmit" :loading="addLoading" icon="fa fa-paper-plane">保存</el-button>
            </div>
        </el-dialog>

        <!--编辑界面-->
        <el-dialog title="编辑" :visible.sync="editFormVisible" width="30%">
            <el-form :model="editForm" label-width="80px" :rules="addFormRules" ref="editForm" size="small">
                <el-form-item label="课程名称" >
                    <el-input v-model="editForm.name" auto-complete="off" placeholder="请输入课程名称"></el-input>
                </el-form-item>
                <el-form-item label="图标" >
                    <el-input v-model="editForm.logo" auto-complete="off" placeholder="请上传图标"></el-input>
                </el-form-item>
                <el-form-item label="创建时间">
                    <el-date-picker
                            v-model="editForm.updateTime"
                            type="datetime"
                            placeholder="选择日期时间">
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="排序" >
                    <el-input type="number" max="500" min="1" v-model="editForm.sortIndex" auto-complete="off" placeholder="请选择排序号"></el-input>
                </el-form-item>
                <el-form-item label="描述">
                    <textarea v-model="editForm.description" auto-complete="off"  rows="3" cols="20" ></textarea>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click.native="editFormVisible = false" icon="el-icon-error">取消</el-button>
                <el-button type="primary" @click.native="editSubmit" :loading="editLoading" icon="fa fa-paper-plane">保存</el-button>
            </div>
        </el-dialog>
    </section>
</template>
<style>
    .el-row {
        margin-bottom: 20px;
        height: 100%;
    }
    :last-child {
        margin-bottom: 0;
    }
    #courseType el-col {
        border: 1px solid red;
        border-radius: 4px;
    }
    .grid-content {
        border-radius: 4px;
        min-height: 36px;
    }
</style>

<script>
    export default {
        data() {
            return {
                courseTypes:[],
                courseTypes1:[],
                courseTypes2:[],
                defaultProps: {
                    children: 'children',
                    label: 'name'
                },
                listLoading:false,
                //验证
                addFormRules: {
                    name: [
                        { required: true, message: '请输入姓名', trigger: 'blur' }
                    ],
                    email:[
                        {type:"email",message:"请输入正确的邮箱地址",trigger:"blur"}
                    ],
                    age:[
                        {type:"integer",max:60,min:18,message:"请输入正确的年龄",trigger:"blur"}
                    ]
                },
                addLoading:false,
                editLoading:false,

                //新增数据
                addForm:{
                    createTime: '', //创建时间
                    name: '',       //课程名称
                    pid: '',        //父类型 ID
                    logo: '',       //图标
                    description: '',//描述
                    sortIndex: '',  //排序
                    path: '',       //路径
                    //totalCount: '', //课程数量

                },
                //新增页面是否显示
                addFormVisible:false,
                //编辑数据
                editForm:{
                    updateTime: '', //修改时间
                    name: '',       //课程名
                    logo: '',       //图标
                    description: '',//描述
                    sortIndex: '',  //排序

                },
                //新增页面是否显示
                editFormVisible:false,
                //选择父ID下拉列表
                pids:[],
            }
        },
        methods:{
            changSel(value){
                console.log(value)
            },
            getTreeData(){
                // 发送一个异步请求: get请求 /product/courseType/treeData
                this.$http.get("/course/courseType/treeData").then(res=>{
                    //console.log(this);
                    this.courseTypes = res.data;
                    this.courseTypes1 = res.data;
                    this.courseTypes2 = res.data;
                });
            },
            handleNodeClick(value){
                //console.debug(value.children);
                this.courseTypes1=value.children;
            },
            //选择父类ID下拉列表
            getPids(){
                this.$http.get("/course/courseType/getAllParent")
                    .then(res=>{
                        this.pids=res.data;
                    })
            },
            //显示新增页面
            handleAdd: function () {
                this.addFormVisible = true;
                //重置表单
                this.addForm = Object.assign({},null);
            },

            //新增
            addSubmit: function () {
                this.$refs.addForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.addLoading = true;
                            let para = Object.assign({}, this.addForm);
                            para.path=para.path+"";
                            //截取字符串
                            let disLength=para.path.length;
                            para.pid=parseInt(para.path.substring(disLength-4,disLength));
                            //替换 ,为.
                            para.path="."+para.path+".";
                            para.path=para.path.replace(",",".");
                            //日期
                            //para.createTime=this.dateConversion(para.createTime);
                            para.createTime=new Date().getTime();
                            para.createTime=parseInt(para.createTime);
                            //alert(para.pid)
                            //alert(para.path)
                            //console.log(para)
                            this.$http.post("/course/courseType/save",para)
                            //this.$http.post("/#",para)
                                .then(res=>{
                                    this.listLoading=false;
                                    this.addLoading=false;
                                    let {success,message}=res.data;
                                    if (success){
                                        this.$message({
                                            message: message,
                                            type: 'success'
                                        });
                                        this.addFormVisible=false;
                                        this.getTreeData();
                                    }else {
                                        this.$message({
                                            message: message,
                                            type: 'error'
                                        });
                                    }
                                })
                        });
                    }
                });
            },

            //显示编辑界面 数据回显
            handleEdit: function (index, row) {
                this.editFormVisible = true;
                //this.addLoading=true;
                this.editForm = Object.assign({}, row);
                //this.listLoading=false;
            },

            //编辑
            editSubmit: function () {
                this.$refs.editForm.validate((valid) => {
                    if (valid) {
                        this.$confirm('确认提交吗？', '提示', {}).then(() => {
                            this.editLoading = true;
                            let para = Object.assign({}, this.editForm);
                            para.updateTime=new Date().getTime();
                            para.updateTime=parseInt(para.updateTime);
                            this.$http.post("/course/courseType/save",para)
                                .then(res=>{
                                    this.listLoading=false;
                                    this.editLoading=false;
                                    let {success,message}=res.data;
                                    if (success){
                                        this.$message({
                                            message: message,
                                            type: 'success'
                                        });
                                        this.editFormVisible=false;
                                        this.getTreeData();
                                    }else {
                                        this.$message({
                                            message: message,
                                            type: 'error'
                                        });
                                    }
                                })
                        });
                    }
                });
            },

            //删除
            courseDel: function (index, row) {
                this.$confirm('确认删除该记录吗?', '提示', {
                    type: 'warning'
                }).then(() => {
                    this.listLoading = true;
                    this.$http.delete("/course/courseType/"+row.id)
                        .then(res=>{
                            //alert(row.id)
                            this.listLoading = false;
                            let{success,message}=res.data;
                            if (success){
                                this.$message({
                                    message: message,
                                    type: 'success'
                                });
                                this.getTreeData();
                            }else {
                                this.$message({
                                    message: message,
                                    type: 'error'
                                });
                            }
                        })
                }).catch(() => {

                });
            },
            selsChange: function (sels) {
                this.sels = sels;
            },

            //格式化时间戳
            dateTimeFormat(value) {
                let time = new Date(+value);
                let rightTwo = (v) => {
                    v = '0' + v
                    return v.substring(v.length - 2, v.length)
                }
                if (time == null) return;
                let year = time.getFullYear();
                let month = time.getMonth() + 1;
                let date = time.getDate();
                let hours = time.getHours();
                let minutes = time.getMinutes();
                let seconds = time.getSeconds();
                return year + '-' + rightTwo(month) + '-' + rightTwo(date)+ ' ' + rightTwo(hours) + ':' + rightTwo(minutes) + ':' + rightTwo(seconds);
            },
            //保存

        },

        mounted(){
            //对courseTypes数据赋值
           this.getTreeData();
           //父ID下拉列表
           this.getPids();
        }
    };
</script>