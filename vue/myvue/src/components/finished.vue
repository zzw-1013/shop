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
            <el-table :data="tableData" border>
                <el-table-column prop="id" label="用户ID" width="80">
                </el-table-column>
                <el-table-column prop="goodid" label="商品ID" width="80">
                </el-table-column>
                <el-table-column prop="name" label="商品名称" width="80">
                </el-table-column>
                <el-table-column prop="price" label="商品价格" width="90">
                </el-table-column>
                <el-table-column prop="intro" label="商品简介" width="160">
                </el-table-column>
                <el-table-column prop="newo" label="新旧程度/成" width="80">
                </el-table-column>
                <el-table-column prop="fenlei" label="商品类别" width="80" >
                </el-table-column>
                <el-table-column prop="count" label="购买数量" width="80" >
                </el-table-column>
                <el-table-column prop="address" label="我的地址" width="110" >
                </el-table-column>
                <el-table-column prop="time" label="下单时间" width="120" >
                </el-table-column>
                <el-table-column prop="imgUrl" label="商品图片" width="320" >
                    <template slot-scope="scope">
                        <el-carousel  style="width: 300px; height:300px" >
                            <el-carousel-item v-for="i in scope.row.imgUrl" :key="i">
                            <el-image style="width: 300px; height:300px"
                                :src="i" ></el-image>
                            </el-carousel-item>
                        </el-carousel>
                    </template>
                </el-table-column>
            </el-table>
        </el-main>
    </el-container>
  </div>
</template>
<script>
export default {
  data () {
    return {
      userid: '',
      userword: '',
      tableData: [],
      index: '',
      rows: '',
      column: '',
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
      this.$axios.post('http://localhost:8080/horder1', formData, config)
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
          this.length = this.tableData.length
          this.getData1()
        })
    },
    getData1 () { // 后台获取数据
      for (var j = 0; j < this.tableData.length; j++) {
        let formData = new FormData()
        formData.append('id', this.tableData[j].id)
        formData.append('goodid', this.tableData[j].goodid)
        let config = {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        }
        this.$axios.post('http://localhost:8080/uc', formData, config)
          .then(res => {
            this.data.push(res.data)
          })
      }
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
