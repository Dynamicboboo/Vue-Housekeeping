<template>
  <div class="customer">
    <el-button type="primary" size="small" @click="toAdd">添加</el-button>
    <!-- 表格 -->
    <el-table size="small" :data="customers">
      <el-table-column type="index" :index="1" label="编号" width="100"/>
      <el-table-column prop="realname" label="用户名" />
      <el-table-column prop="telephone" label="手机号" />
      <el-table-column prop="status" label="状态" />
      <el-table-column fixed="right" label="操作">
        <template slot-scope="scope">
          <el-button type="danger" size="small" @click="del(scope.row.id)">删除</el-button>
          <el-button type="primary" size="small" @click="edit(scope.row)">修改</el-button>
          <el-button type="primary" size="small" @click="detailsHandler(scope.row.id)">详情</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="用户" :visible.sync="visable">
      <el-form ref="ruleForm" :model="form" :rules="rules" label-width="80px">
        <el-form-item label="用户名" prop="realname">
          <el-input v-model="form.realname" clearable placeholder="请输入用户名" />
        </el-form-item>
        <el-form-item label="手机号" prop="telephone">
          <el-input
            v-model="form.telephone"
            clearable
            placeholder="请输入手机号"
            maxlength="11"
            minlength="11"
            show-word-limit
          />
        </el-form-item>
        <el-form-item label="密码" prop="password">
          <el-input v-model="form.password" clearable placeholder="请输入密码" show-password />
        </el-form-item>
        <el-form-item label="状态" prop="status">
          <el-select v-model="form.status" placeholder="请输入状态">
            <el-option label="禁用" value="禁用" />
            <el-option label="启用" value="启用" />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="close('ruleForm')">取消</el-button>
        <el-button type="primary" @click="submit('ruleForm')">确定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>
</template>
<script>
import { get, post } from "@/utils/request";
export default {
  data() {
    return {
        text: '',
      visable: false,
      form: {},
      customers: [],
      rules: {
        realname: [
          { required: true, message: "请输入用户名", trigger: "blur" }
        ],
        telephone: [
          { required: true, message: "请输入手机号", trigger: "blur" }
        ],
        password: [{ required: true, message: "请输入密码", trigger: "blur" }],
        status: [{ required: true, message: "请输入状态", trigger: "change" }]
      }
    };
  },
  created() {
    this.loadCustomers();
  },
  methods: {
    // 详情页面
    detailsHandler(cid) {
      this.$router.push({ name: 'details', params: { id:cid }})
    },
    loadCustomers() {
      let url = "http://www.xiaonainiu.top:8888/user/findAllCustomer"
      get(url).then(response => {
        this.customers = response.data;
      });
    },
    toAdd() {
      (this.form = {}), (this.visable = true);
    },
    del(id) {
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 删除
        let url = "http://www.xiaonainiu.top:8888/user/deleteById";
        get(url, { id: id }).then(response => {
          // 提示
          this.$message({ type: "success", message: response.message });
          // 刷新数据
          this.loadCustomers();
        });
      });
    },
    submit(form) {
      this.$refs[form].validate(valid => {
        if(valid) {
          // 表单验证成功的情况
          let url = "http://www.xiaonainiu.top:8888/user/saveOrUpdate";
          post(url, this.form).then(response => {
            this.$message({ type: "success", message: response.message });
            this.visable = false;
            this.loadCustomers();
            this.$refs[form].resetFields();
          });
        } else {
          return false;
        }
      });
    },
    close(form) {
      this.visable = false;
      this.loadCustomers();
     // this.$refs[form].resetFields();
    },
    edit(row) {
      this.form = row;
      this.visable = true;
    },
    
  }
};
</script>

<style>
</style>