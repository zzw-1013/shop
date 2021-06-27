<template>
  <div >
    <div>
      <el-button plain round icon="el-icon-view" type="primary" style="margin-left: 50px" @click="main">首页</el-button>
      <el-button plain round icon="el-icon-view" v-if="this.userid === ''" type="info" style="margin-left: 15px" @click="denglu">登录</el-button>
      <el-button plain round icon="el-icon-view" v-if="this.userid != ''" type="info" style="margin-left: 15px" @click="user">{{this.userid}}</el-button>
      <el-button plain round icon="el-icon-view" type="info" style="margin-left: 15px" @click="car">购物车</el-button>
      <el-button plain round icon="el-icon-view" type="danger" style="margin-left: 15px" @click="tui">退出</el-button>
    </div>
      <el-container style="height: 600px; border: 5px solid #eee">
        <el-header  style="background-color: rgb(138, 201, 296)">
            <p6 style="margin-left: 10px;height: 500px;font-size:30px;">店铺管理系统</p6>
            <el-button plain round icon="el-icon-view" type="success" style="margin-left: 20px" @click="allgood">全部宝贝</el-button>
            <el-button plain round icon="el-icon-view" type="success" @click="daifahuo" >待发货订单</el-button> 
            <el-button plain round icon="el-icon-view" type="success" @click="yiwancheng" >已完成订单</el-button>
            <el-button icon="el-icon-circle-plus" type="warning" @click="addgood">添加商品</el-button>
        </el-header>
        <el-main style="height:0;flex-grow:1;">
            <el-table :data="tableData.slice((currentPage - 1) * pageSize, currentPage*pageSize)"
            @row-click="openDetails" border>
                <el-table-column prop="id" label="用户ID" width="80">
                </el-table-column>
                <el-table-column prop="goodid" label="商品ID" width="100">
                </el-table-column>
                <el-table-column prop="name" label="商品名称" width="80">
                </el-table-column>
                <el-table-column prop="price" label="商品总价" width="90">
                </el-table-column>
                <el-table-column prop="intro" label="商品简介" width="160">
                </el-table-column>
                <el-table-column prop="newo" label="新旧程度/成" width="80">
                </el-table-column>
                <el-table-column prop="fenlei" label="商品类别" width="60" >
                </el-table-column>
                <el-table-column prop="size" label="商品尺寸" width="90" >
                </el-table-column>
                <el-table-column prop="yijia" label="是否议价" width="60" >
                </el-table-column>
                <el-table-column prop="count" label="商品数量" width="60" >
                </el-table-column>
                <el-table-column prop="address" label="用户地址" width="110" >
                </el-table-column>
                <el-table-column prop="time" label="下单时间" width="100" >
                </el-table-column>
                <el-table-column prop="imgUrl" label="商品图片" width="310" >
                    <template slot-scope="scope">
                        <el-carousel  style="width: 300px; height:300px" >
                            <el-carousel-item v-for="i in scope.row.imgUrl" :key="i">
                            <el-image style="width: 300px; height:300px"
                                :src="i" ></el-image>
                            </el-carousel-item>
                        </el-carousel>
                    </template>
                </el-table-column>
                <el-table-column  width="80" label="操作">
                    <template slot-scope="scope">
                        <el-button size="mini" plain icon="el-icon-plus" type="warning" @click.native.prevent="deleteRow(scope.$index, tableData)">发货</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </el-main>
        <el-footer>
            <el-pagination
                @size-change="sizechange"
                @current-change="currentchange"
                style="margin-left: 1010px"
                background
                :current-page="currentPage"
                :page-size="pageSize"
                layout="prev, pager, next"
                :total="length">
            </el-pagination>
        </el-footer>
    </el-container>
    <el-dialog
      title="提示"
      :visible.sync="centerDialogVisible"
      width="30%"
      center>
      <span>请您确定已发货？</span>
      <span slot="footer" class="dialog-footer">
        <el-button @click="quxiao">取 消</el-button>
        <el-button type="primary" @click="sure">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
export default {
  data () {
    return {
      userid: '',
      userword: '',
      centerDialogVisible: false,
      tableData: [],
      currentPage: 1,
      pageSize: 8,
      length: 0,
      index: '',
      rows: '',
      column: '',
      str: [],
      data: []
    }
  },
  mounted () {
    this.getData()
  },
  methods: {
    car () {
      if (sessionStorage.getItem('userid') === '') {
        this.$router.push('/login')
      } else {
        this.$router.push('/car')
      }
    },
    main () {
      this.$router.push('/')
    },
    denglu () {
      this.$router.push('/login')
    },
    user () {
      this.$router.push('/personalinfo')
    },
    tui () {
      sessionStorage.setItem('userid', '')
      sessionStorage.setItem('userword', '')
      this.$router.push('/')
    },
    allgood () {
      this.$router.push('/shopmanage')
    },
    daifahuo () {
      this.$router.push('/sendgood')
    },
    yiwancheng () {
      this.$router.push('/finished')
    },
    addgood () {
      this.$router.push('/addgood')
    },
    openDetails (row) { // 获取点击行的数据
      this.column = row
    },
    getData () { // 后台获取数据
      this.userid = sessionStorage.getItem('userid')
      this.userword = sessionStorage.getItem('userword')
      let formData = new FormData()
      formData.append('id', sessionStorage.getItem('userid'))
      formData.append('word', sessionStorage.getItem('userword'))
      let config = {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }
      this.$axios.post('http://localhost:8080/sendgood', formData, config)
        .then(res => {
          for (var i = 0; i < res.data.length; i++) {
            this.str = res.data[i].imgUrl.split(',')
            this.str.pop()
            res.data[i].imgUrl = this.str
          }
          this.tableData = res.data
          for (let k = 0; k < this.tableData.length; k++) {
            for (let j = 0; j < this.tableData[k].imgUrl.length; j++) {
              this.tableData[k].imgUrl[j] = require('C:/Users/zzw12/Pictures/good/' + this.tableData[k].imgUrl[j])
            }
          }
          for (let f = 0; f < this.tableData.length; f++) {
            this.tableData[f].address = '手机号:' + this.tableData[f].phone + '\xa0\xa0\xa0\xa0\xa0\xa0姓名:' + this.tableData[f].uname +
            '\xa0\xa0\xa0\xa0\xa0\xa0地址:' + this.tableData[f].address
          }
          this.length = this.tableData.length
        })
    },
    deleteRow (index, rows) {
      this.centerDialogVisible = true
      this.index = (this.currentPage - 1) * 4 + index
      this.rows = rows
    },
    sure () { // 将商品下架
      this.centerDialogVisible = false
      let formData = new FormData()
      formData.append('id', this.column.id)
      formData.append('shopid', sessionStorage.getItem('userid'))
      formData.append('word', sessionStorage.getItem('userword'))
      formData.append('goodid', this.column.goodid)
      let config = {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }
      this.$axios.post('http://localhost:8080/fahuo', formData, config)
        .then(res => {
          this.getData()
          this.$notify({
            title: '成功',
            message: '操作成功',
            type: 'success'
          })
        })
    },
    quxiao () {
      this.centerDialogVisible = false
      this.$notify({
        title: '警告',
        message: '操作取消',
        type: 'warning'
      })
    },
    sizechange (val) {
      this.pageSize = val
    },
    currentchange (val) {
      this.currentPage = val
    }
  }
}
</script>
<style>
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 50px;
  }
</style>
