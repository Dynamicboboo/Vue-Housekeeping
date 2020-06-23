<template>
  <div class="commont">
    <!-- 表格 -->
    <el-table :data="commont">
      <el-table-column prop="id" label="编号" width="80" />
      <el-table-column prop="score" label="分数" />
      <el-table-column prop="content" :show-overflow-tooltip="true" label="评论内容" />
      <el-table-column prop="commentTime" label="评论时间" >
        <template slot-scope="scope">{{scope.row.commentTime | formatDate}}</template>
      </el-table-column>
      <el-table-column prop="orderId" label="订单编号" />
      <el-table-column prop="status" label="评论状态" />
      <el-table-column label="操作" fixed="right">
        <template slot-scope="scope">
          <el-button type="danger" size="small" @click="del(scope.row.id)">删除</el-button>
           <el-button type="primary" size="small" @click="pinglun(scope.row)">审核</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 表格结束 -->
    <!-- 模态框 -->
    <el-dialog title="评论审核" :visible.sync="visible">
      <p>订单编号：{{this.sh.orderId}}</p>
      <p>评论分数：{{this.sh.score}}</p>
      <p>评论内容：{{this.sh.content}}</p>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submit">审核通过</el-button>
        <el-button type="danger" @click="refuse">拒绝审核</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>
</template>
<script>
import { get } from "@/utils/request";
import { formatDate } from '@/utils/date.js';
export default {
  data() {
    return {
      visible: false,
      commont: [],
      sh: []
    };
  },
  created() {
    this.loadCommint();
  },
   //时间戳格式化
    filters: {
    formatDate(time) {
      var date = new Date(time);
      return formatDate(date, "yyyy-MM-dd hh:mm");
    }
  },
  methods: {
    loadCommint() {
      let url = "http://www.xiaonainiu.top:8888/comment/findAll";
      get(url).then(response => {
        this.commont = response.data;
      });
    },
   

   del(id) {
      this.$confirm("此操作将永久删除该数据, 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        // 删除
        let url = "http://www.xiaonainiu.top:8888/comment/deleteById";
        get(url, { id: id }).then(response => {
          // 提示
          this.$message({ type: "success", message: response.message });
          // 刷新数据
          this.loadCommint();
        });
      });
    },
    pinglun(row) {
      this.sh = row;
      this.visible = true;
    },
    submit() {
      let url = "http://www.xiaonainiu.top:8888/comment/auditing";
      get(url, { id: this.sh.id }).then(response => {
        this.$message({ type: "success", message: response.message });
        this.loadCommint();
      });
      
      this.visible = false;
 
    },
    refuse() {
      let url = "http://www.xiaonainiu.top:8888/comment/refuseAuditing";
      get(url, { id: this.sh.id }).then(response => {
        this.$message({ type: "success", message: response.message });
        this.loadCommint();
      });
      this.visible = false;      
    }
  }
}
</script>
