<template>
  <div class="all" id="box" >
    <div>
      <el-button plain round icon="el-icon-view" type="primary" style="margin-left: 50px" @click="main">首页</el-button>
      <el-button plain round icon="el-icon-view" v-if="this.userid === ''" type="info" style="margin-left: 15px" @click="denglu">登录</el-button>
      <el-button plain round icon="el-icon-view" v-if="this.userid != ''" type="info" style="margin-left: 15px" @click="user">{{this.userid}}</el-button>
      <el-button plain round icon="el-icon-view" type="info" style="margin-left: 15px" @click="car">购物车</el-button>
      <el-button plain round icon="el-icon-view" type="danger" style="margin-left: 15px" @click="tui">退出</el-button>
    </div>
    <div>
      <div>
    <el-col :span="3">
      <el-input style="margin-left: 50px" width="250" v-model="search" v-on:input ="inputFunc"
      clearable="true" placeholder="输入关键字搜索"/>
    </el-col>
    <template>
      <el-button-group>
        <el-button  style="margin-left: 400px" type="success" icon="el-icon-d-arrow-left" @click="sheng">价格升序</el-button>
        <el-button   type="success" icon="el-icon-d-arrow-right" @click="jiang">价格降序</el-button>
        <el-button   type="success" icon="el-icon-menu" @click="zong">综合排序</el-button>
      </el-button-group>
    </template>  
      </div>
      <div class="ttt">
    <ul v-for="item in table" :key="item">
      <li>
        <div class="block">
          <el-carousel height="300px" width="350px" >
            <el-carousel-item v-for="i in item.imgUrl" :key="i">
              <el-image style="width: 350px; height: 300px"
                        :src="i" ></el-image>
            </el-carousel-item>
          </el-carousel>
        </div>
      </li>
      <div class="t">
      <li>
        <p style="color:black; font-size:30px">¥{{item.price}}</p>
      </li>
      <li>
        <p @click="info(item)" style="text-decoration:underline">{{item.name}}</p>
      </li>
      <li>
        <a @click="shop(item)" style="text-decoration:underline">{{item.id}}</a>的店铺
      </li>
      </div>
    </ul>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
      userid: '',
      userword: '',
      tableData: [],
      table: [],
      str: [],
      search: ''
    }
  },
  mounted () {
    this.getData()
  },
  methods: {
    main () {
      this.$router.push('/')
    },
    denglu () {
      this.$router.push('/login')
    },
    car () {
      if (sessionStorage.getItem('userid') === '') {
        this.$router.push('/login')
      } else {
        this.$router.push('/car')
      }
    },
    user () {
      this.$router.push('/personalinfo')
    },
    tui () {
      sessionStorage.setItem('userid', '')
      sessionStorage.setItem('userword', '')
      this.$router.push('/')
    },
    shop (item) {
      sessionStorage.setItem('shopid', item.id)
      this.$router.push('/usershop')
    },
    info (item) {
      sessionStorage.setItem('shopid', item.id)
      sessionStorage.setItem('goodid', item.goodid)
      this.$router.push('/goodinfo')
    },
    inputFunc () { // 动态搜索
      this.getList()
    },
    getList () { // 根据id搜索结果集
      this.table = this.tableData.filter((item, index) =>
        item.intro.includes(this.search)
      )
    },
    clear () { // 搜索转全部
      this.search = ''
      this.getList()
    },
    sheng () {
      this.table.sort((a, b) => {
        return a.price - b.price
      })
    },
    jiang () {
      function sortData (a, b) {
        return b.price - a.price
      }
      this.table.sort(sortData)
    },
    zong () {
      this.getData()
    },
    getData () { // 后台获取数据
      this.userid = sessionStorage.getItem('userid')
      this.userword = sessionStorage.getItem('userword')
      let formData = new FormData()
      let config = {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      }
      this.$axios.post('http://localhost:8080/getAllgood', formData, config)
        .then(res => {
          // alert(res.data[0].imgUrl)
          for (var i = 0; i < res.data.length; i++) {
            this.str = res.data[i].imgUrl.split(',')
            this.str.pop()
            res.data[i].imgUrl = this.str
          }
          // alert(this.str.length)
          this.table = res.data
          for (let k = 0; k < this.table.length; k++) {
            for (let j = 0; j < this.table[k].imgUrl.length; j++) {
              this.table[k].imgUrl[j] = require('C:/Users/zzw12/Pictures/good/' + this.table[k].imgUrl[j])
            }
          }
          this.tableData = this.table
        })
    }
  }
}
</script>
<style>

.all {
  background-color: rgb(238, 249, 255);
}
.t {
  background-color: rgb(128, 345, 255);
}
#box ul{
  float: left;
  width: 200px;
  height: 500px;
}
#box li{
  list-style: none;
  padding: auto;
}
.el-carousel__item:nth-child(2n) {
  background-color: #cdeafd;
}
.el-carousel__item:nth-child(2n+1) {
  background-color: #d0ecff;
}
</style>
