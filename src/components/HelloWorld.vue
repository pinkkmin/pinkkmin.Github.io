<!--
 * @Descripttion: 
 * @Date: 2020-09-30 17:03:56
-->
<template>
  <div class="div_main">
    <h1>This is a Problem.</h1>
    <el-divider>
      <i class="el-icon-basketball" />
    </el-divider>
    <el-form label-position="left" :model="form" :rules="rules" ref="form">
      <el-form-item prop="content">
        <el-input
          placeholder="Listen to me, world!"
          class="input-text"
          type="textarea"
          :autosize="{ minRows: 2 }"
          v-model="form.content"
        ></el-input>
      </el-form-item>
      <el-form-item>
        <el-button style="float: right" round type="success" @click="publish()"
          >发布</el-button
        >
      </el-form-item>
    </el-form>
    <el-timeline>
      <el-timeline-item
        v-for="(item, index) in data"
        :key="index"
        color="#67C23A"
        size="large"
        type="succuss"
        :timestamp="item.contentDate"
        placement="top"
      >
        <el-card>
          <el-button
            type="danger"
            plain
            size="mini"
            style="float: right"
            icon="el-icon-delete"
            circle
            @click="deleteItem(index)"
          />
          <p class="content">{{ item.content }}</p>
        </el-card>
      </el-timeline-item>
      <el-timeline-item
        color="#67C23A"
        size="large"
        type="succuss"
        :timestamp="
          new Date().toLocaleDateString() +
          ' ' +
          new Date().toLocaleTimeString()
        "
        placement="top"
      />
      <p>End</p>
    </el-timeline>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      baseUrl: 'http://47.97.192.85:5207/api/',
      form: {
        content: '',
      },
      rules: {
        content: [
          { required: true, message: '请输入内容', trigger: 'blur' },
          { max: 140, message: '长度在 140 以内', trigger: 'blur' },
        ],
      },
      queryForm: {
        page: 0,
        pageSize: 10,
      },
      data: [],
    }
  },
  created() {
    this.query()
  },
  methods: {
    query() {
      this.axios.post(this.baseUrl + 'query', this.queryForm).then((res) => {
        res.data.data.forEach(element => {
          this.data.push(element)
        })
      })
    },
    deleteItem(index) {
      console.log(this.data[index])
      this.axios.post(this.baseUrl + 'delete', this.data[index]).then((res) => {
        this.query()
        this.$message({
          message: '删除成功',
          type: 'success',
        })
      })
    },

    publish() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          this.axios.post(this.baseUrl + 'insert', this.form).then((res) => {
            this.$message({
              message: '创建成功',
              type: 'success',
            })
          })
        } else {
          this.$notify({
            title: '提示',
            message: '请输入内容',
            type: 'success',
          })
        }
      })
    },
    scroll() {
      let isLoading = false
      window.onscroll = () => {
        // 距离底部200px时加载一次
        let bottomOfWindow =
          document.documentElement.offsetHeight -
            document.documentElement.scrollTop -
            window.innerHeight <=
          10
        if (!bottomOfWindow) isLoading = false
        if (bottomOfWindow && !isLoading) {
          isLoading = true
          this.queryForm.page += 1
          this.query()
        }
      }
    },
  },
  mounted() {
    this.scroll()
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  font-weight: normal;
  text-align: center;
}
.div_main {
  margin: 10px 10px 10px 10px;
}
.title {
  margin: 20px 0 20px 0;
}
.content {
  font-size: 16px;
}
.input-text {
  font-size: 16px;
  font-weight: bolder;
  color: #000000;
}
</style>