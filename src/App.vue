<template>
  <div id="app">
    <div class="tableCon">
      <el-table
        :data="tableData"
        style="width: 100%"
        :border="true"
        height="500"
      >
        <el-table-column label="分离器编号" width="250" prop="id"> </el-table-column>
        <el-table-column label="液体密度pl(kg/m3)" width="250" prop="pl"> </el-table-column>
        <el-table-column label="气体密度pg(kg/m3)" width="250" prop="pg"> </el-table-column>
        <el-table-column label="动力粘度ug(N*s/m2)" width="250" prop="ug"> </el-table-column>
        <el-table-column label="重力加速度g(m2/s)" width="250" prop="g"> </el-table-column>
        <el-table-column label="操作">
          <template slot-scope="scope">
            <el-button size="mini" @click="handleEdit(scope.row)" type="primary"
              >计算油降速度</el-button
            >
          </template>
        </el-table-column>
      </el-table>
    </div>

    <div v-show="vecs.length!=0" class="result">
      <div class="title">
        油降速度计算结果：
      </div>
      <div class="re">迭代试算法求得的油滴沉降速度为w=<span>{{vecs[0]}}m</span>/s</div>
      <div  class="re">流态区分法求得的油滴沉降速度为w=<span>{{vecs[1]}}</span>m/s</div>
      <div class="re">图解法求得的油滴沉降速度为w=<span>{{vecs[2]}}</span>m/s</div>
      <div  class="re">阿基米德法求得的油滴沉降速度为w=<span>{{vecs[3]}}</span>m/s</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      tableData: [],
      currentData: {},
      vecs:[]
    };
  },
  created() {
    this.getData();
  },
  methods: {
    async getData() {
      let res = await axios.get("http://localhost:8080/getAllData");
      this.tableData = res.data.data;
    },
    handleEdit(data) {
      this.currentData = data;
      /* 输入直径 */
      let that = this;
      this.$prompt("请输入油滴直径d(m)", "提示")
        .then(async ({ value }) => {
          let res = await axios.get(`http://localhost:8080/getVec?pl=${data.pl}&pg=${data.pg}&d=${value}&g=${data.g}&ug=${data.ug}`);
          this.vecs = res.data.data;
        })
        .catch(() => {
          this.$message({
            type: "info",
            message: "取消输入",
          });
        });
    },
  },
};
</script>

<style>
#app {
  width: 100vw;
  height: 100vh;
  position: relative;
}
.tableCon {
  width: 100vw;
  /* height: 100vh; */
}
.title{
  color: cornflowerblue;
  font-weight: bolder;
}
.result span{
    color: cornflowerblue;
  font-weight: bolder;
}
.re{
  margin-top: 10px;
}
</style>
