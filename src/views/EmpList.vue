<template>
    <div>
        <!--查询Layout布局-->
        <el-row>
            <el-col :span="10">
                <el-input v-model="queryInfo.ename" placeholder="请输入内容"></el-input>
            </el-col>
            <el-col :span="3">
                <el-button type="primary" icon="el-icon-search" @click="query">查询</el-button>
            </el-col>
            <el-col :span="3">
                <el-button type="primary" icon="el-icon-plus"  @click="openAdd">新增</el-button>
            </el-col>
            <el-col :span="3">
                <el-button type="primary" icon="el-icon-delete" @click="delBatch">删除</el-button>
            </el-col>
        </el-row>

        <el-table
                :data="emps"
                stripe
                style="width: 100%"
                @selection-change="handleSelectionChange">
            <el-table-column
                    prop="empno"
                    type="selection"
                    width="55">
            </el-table-column>
            <el-table-column
                    label="序号"
                    type="index"
                    width="50">
            </el-table-column>
            <el-table-column
                    prop="ename"
                    label="姓名"
                    width="180">
            </el-table-column>
            <el-table-column
                    prop="sex"
                    label="性别"
                    width="180">
            </el-table-column>
            <el-table-column
                    prop="hiredate"
                    label="入职日期">
            </el-table-column>
            <el-table-column label="操作">
                <template slot-scope="scope">
                    <el-button
                            size="mini"
                            @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
                    <el-button
                            size="mini"
                            type="danger"
                            @click="handleDelete(scope.$index, scope.row)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!--分页-->
        <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="queryInfo.pageNum"
                :page-sizes="[5, 10, 20, 30]"
                :page-size="queryInfo.pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total">
        </el-pagination>
        <!--新增弹框-->
        <!--visible.sync：  控制对话框的显示和隐藏，控制值为true显示 false隐藏-->
        <el-dialog title="员工新增" :visible.sync="addFormVisible">
        <el-form :model="addForm" :rules="addRules" ref="addForm">
            <el-form-item label="姓名" label-width="120px" prop="ename">
                <el-input v-model="addForm.ename" autocomplete="off"></el-input>
            </el-form-item>
            <el-form-item label="性别" label-width="120px" prop="sex">
                <el-radio-group v-model="addForm.sex">
                    <el-radio label="男">男</el-radio>
                    <el-radio label="女">女</el-radio>
                </el-radio-group>
            </el-form-item>
            <el-form-item label="入职日期" label-width="120px" prop="hiredate">
                <!--format: format指定输入框的格式-->
                <!--value-format指定绑定值的格式。-->
                <el-date-picker
                        v-model="addForm.hiredate"
                        type="date"
                        format="yyyy 年 MM 月 dd 日"
                        value-format="yyyy-MM-dd"
                        size="80px">
                </el-date-picker>
            </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
            <el-button @click="addFormVisible = false">关闭</el-button>
            <el-button type="primary" @click="saveAdd">确 定</el-button>
        </div>
    </el-dialog>
        <!--修改弹框-->
        <el-dialog title="员工修改" :visible.sync="editFormVisible">
            <el-form :model="editForm" :rules="addRules" ref="editForm">
                <el-form-item label="员工编号" label-width="120px">
                    <el-input v-model="editForm.empno" :disabled="true" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="姓名" label-width="120px" prop="ename">
                    <el-input v-model="editForm.ename" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="性别" label-width="120px" prop="sex">
                    <el-radio-group v-model="editForm.sex">
                        <el-radio label="男">男</el-radio>
                        <el-radio label="女">女</el-radio>
                    </el-radio-group>
                </el-form-item>
                <el-form-item label="入职日期" label-width="120px" prop="hiredate">
                    <!--format: format指定输入框的格式-->
                    <!--value-format指定绑定值的格式。-->
                    <el-date-picker
                            v-model="editForm.hiredate"
                            type="date"
                            format="yyyy 年 MM 月 dd 日"
                            value-format="yyyy-MM-dd"
                            size="80px">
                    </el-date-picker>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="editFormVisible = false">关闭</el-button>
                <el-button type="primary" @click="saveEdit">确 定</el-button>
            </div>
        </el-dialog>
    </div>
</template>

<script>
    export default {
        name: "EmpList",
        data() {
            return {
                //表格数据
                emps: [],
                //查询条件
                queryInfo: {
                    ename: "",//根据姓名模糊查询
                    pageNum: 1,
                    pageSize: 10,
                },
                //总条数
                total: 0,
                addFormVisible: false,//控制新增对话框显隐
                //新增表单数据
                addForm: {
                    ename: "",
                    sex: "",
                    hiredate: ""
                },
                addRules: {
                    //prop相同
                    ename: [
                        //非空验证失去焦点触发提示语言
                        {required: true, message: '请输入姓名', trigger: 'blur'},
                    ],
                    sex: [
                        {required: true, message: '请选择性别', trigger: 'change'}
                    ],
                    hiredate: [
                        {required: true, message: '请选择入职日期', trigger: 'change'}
                    ],
                },
                //存放选中行
                selectData: [],
                editFormVisible: false,
                //编辑数据
                editForm: {
                    empno: "",
                    ename: "",
                    sex: "",
                    hiredate: "",
                },

            }
        },
        methods: {
            //获取信息
            getEmp() {
                this.$http.post("getAllEmp", this.queryInfo).then(res => {
                    this.emps = res.data.emps;
                    this.total = res.data.counts;
                })
            },
            query() {
                //将页码设置成第一页，
                this.queryInfo.pageNum = 1;
                this.getEmp();
            },
            //每页条数改变时
            handleSizeChange(val) {
                this.queryInfo.pageNum = 1;
                this.queryInfo.pageSize = val;
                this.getEmp();
            },
            //页码改变时触发
            handleCurrentChange(val) {
                this.queryInfo.pageNum = val;
                this.getEmp();

            },
            //点击新增按钮，打开新增对话框
            openAdd() {
                this.addFormVisible = true;
                this.$refs["addForm"].resetFields();
            },
            //点击新增框保存按钮，完成数据保存操作
            saveAdd() {
                //判断是否符合验证规则
                this.$refs["addForm"].validate((valid) => {
                    if (valid) {
                        this.$http.post("addEmp", this.addForm).then(res => {
                            if (res.data == "success") {
                                this.$message({
                                    message: '新增成功',
                                    type: 'success'
                                });
                                this.addFormVisible = false;
                                this.getEmp();
                            }

                        })
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            handleEdit(index, row) {
                this.$http.get("getEmpByEmpno?empno=" + row.empno).then(res => {
                    this.editForm = res.data;
                    this.editFormVisible = true;
                    this.$refs["editForm"].resetFields();
                })
            },
            //点击修改框保存按钮，完成数据保存操作
            saveEdit() {
                //判断是否符合验证规则
                this.$refs["editForm"].validate((valid) => {
                    if (valid) {
                        this.$http.post("editEmp", this.editForm).then(res => {
                            if (res.data == "success") {
                                this.$message({
                                    message: '修改成功',
                                    type: 'success'
                                });
                                this.editFormVisible = false;
                                this.getEmp();
                            }

                        })
                    } else {
                        console.log('error submit!!');
                        return false;
                    }
                });
            },
            handleDelete(index, row) {
                this.$confirm('确定删除?', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$http.get("delEmp?empno=" + row.empno).then(res => {
                        if(res.data == "success") {
                            this.$message({
                                type: 'success',
                                message: '删除成功!'
                            });
                            this.queryInfo.pageNum = 1;
                            this.getEmp();
                        } else {
                            this.$message.error("删除失败")
                        }
                    })
                }).catch(() => {
                    this.$message({
                        type: 'info',
                        message: '已取消删除'
                    });
                });
            },
            //复选框触发，返回勾选数据
            handleSelectionChange(val) {
                this.selectData = val;
            },
            //批量删除
            delBatch() {
                if(this.selectData[0] != null) {
                    let empnos = [];
                    for(let i = 0; i < this.selectData.length; i++) {
                        empnos[i] = this.selectData[i].empno;
                    }
                    this.$confirm('确定删除?', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then(() => {
                        this.$http.get("delBatchEmp?empnos=" + empnos).then(res => {
                            if (res.data == "success") {
                                this.$message({
                                    type: 'success',
                                    message: '删除成功!'
                                });
                                this.queryInfo.pageNum = 1;
                                this.getEmp();
                            } else {
                                this.$message.error("删除失败")
                            }
                        })
                    }).catch(() => {
                        this.$message({
                            type: 'info',
                            message: '已取消删除'
                        });
                    });
                } else {
                    this.$message({
                        type: 'info',
                        message: '请先选择删除信息'
                    });
                }
            },
        },
        created() {
            this.getEmp();
        }
    }
</script>

<style scoped>

</style>