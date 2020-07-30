<template>
  <div class="pos">
    <!-- :span='7'占7栏 -->
    <!-- 设置行 -->
      <el-row>
        <!-- 设置列 -->
        <!--Element支持24栏布局 -->
        <!-- 左边 -->
           <el-col :span='7' class="pos-order" id="pos-order">
             <!-- 选项卡 -->
             <el-tabs type="border-card">
               <el-tab-pane label="点餐">
                 <!--用:data 绑定表格中的数据 -->
                 <!-- 选项卡的表格 -->
                 <el-table :data="tableData" border style="width:100%">
                   <!-- 表格中的每一列 prop读取数据的属性  label标签的标题-->
                   <el-table-column prop="goodsName" label="商品名称"></el-table-column>
                   <el-table-column prop="count" label="数量" width="50"></el-table-column>
                   <el-table-column prop="price" label="金额" width="70"></el-table-column>
                   <!-- fixed固定操作这一列 -->
                   <el-table-column label="操作" width="100" fixed="right">
                     <!-- scope的作用就是获取当前所在元素的数据 scope.row相当于当前行的数据对象-->
                     <template scope="scope">
                       <!-- type="text"文本的格式，没有边框 -->
                       <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                       <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                     </template>
                   </el-table-column>
                 </el-table>
                 <!--  -->
                 <div class="totalDiv">
                   <small>数量：</small>{{totalCount}}   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  <small>金额：</small>{{totalMoney}}
                 </div>
                 <!-- 点餐表格下面的按钮 -->
                 <div class="div-btn">
                    <el-button type="warning">挂单</el-button>
                    <el-button type="danger" @click="delAllGoods()">删除</el-button>
                    <el-button type="success" @click="checkout()">结账</el-button>
                 </div>

               </el-tab-pane>
               <el-tab-pane label="挂单">挂单</el-tab-pane>
               <el-tab-pane label="外卖">外卖</el-tab-pane>
             </el-tabs>
           </el-col>
           <!-- 右边 -->
           <el-col :span='17'>
             <!-- 右边的常用商品 -->
             <div class="often-goods">
                <div class="title">常用商品</div>
                <div class="often-goods-list">
                  <ul class="cookList">
                    <li v-for="goods in oftenGoods" @click="addOrderList(goods)">
                      <span>{{goods.goodsName}}</span>
                      <span class="often-price">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
             </div>

             <div class="goods-type">
               <el-tabs>
                 <el-tab-pane label="汉堡">
                    <div>
                      <ul class="cookList">
                        <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img src="../../assets/img/hanbao.jpg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                        </li>
                      </ul>
                    </div>
                 </el-tab-pane>
                 <el-tab-pane label="小食">
                    <ul class="cookList">
                        <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img src="../../assets/img/hanbao.jpg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                        </li>
                      </ul>
                 </el-tab-pane>
                 <el-tab-pane label="饮料">
                    <ul class="cookList">
                        <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img src="../../assets/img/hanbao.jpg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                        </li>
                      </ul>
                 </el-tab-pane>
                 <el-tab-pane label="套餐">
                    <ul class="cookList">
                        <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                            <span class="foodImg"><img src="../../assets/img/hanbao.jpg" width="100%"></span>
                            <span class="foodName">{{goods.goodsName}}</span>
                            <span class="foodPrice">￥{{goods.price}}元</span>
                        </li>
                    </ul>
                 </el-tab-pane>
               </el-tabs>
             </div>
           </el-col>
      </el-row>
  </div>
</template>

<script>
//引入axios 由于使用了npm来进行安装，不需要填写路径
import axios from 'axios'
export default {
  name: "pos",
  data() {
    return {
      tableData: [],
      oftenGoods: [
         {
              goodsId:1,
              goodsName:'香辣鸡腿堡',
              price:18
          }, {
              goodsId:2,
              goodsName:'田园鸡腿堡',
              price:15
          }, {
              goodsId:3,
              goodsName:'和风汉堡',
              price:15
          }, {
              goodsId:4,
              goodsName:'快乐全家桶',
              price:80
          }, {
              goodsId:5,
              goodsName:'脆皮炸鸡腿',
              price:10
          }
      ],
      type0Goods:[],
      type1Goods:[],
      type2Goods:[],
      type3Goods:[],
      totalMoney:0,
      totalCount:0
    };
  },
  created:function(){
    axios.get('http://old.jspag.com/DemoApi/oftenGoods.php')
      .then(response=>{
        //  console.log(1111);
        //  console.log(response);
         this.oftenGoods=response.data;
     })
    .catch(err=>{
      // console.log(error);
      alert('网络错误，不能访问');
    })

   //读取分类商品列表
    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(response=>{
         console.log(response);
         //this.oftenGoods=response.data;
         this.type0Goods=response.data[0];
         this.type1Goods=response.data[1];
         this.type2Goods=response.data[2];
         this.type3Goods=response.data[3];

    })
    .catch(error=>{
        console.log(error);
        alert('网络错误，不能访问');
    })
    axios.get('http://old.jspag.com/DemoApi/oftenGoods.php')
      .then(response=>{
        //  console.log(1111);
        //  console.log(response);
         this.oftenGoods=response.data;
     })
    .catch(err=>{
      // console.log(error);
      alert('网络错误，不能访问');
    })
  },
  // 使用原生js设置高度
  mounted: function() {
    var orderHeight = document.body.clientHeight;
    console.log(orderHeight);
    document.getElementById("pos-order").style.height = orderHeight + "px";
  },
  methods:{
    addOrderList(goods){
      //清零
      this.totalMoney=0;
      this.totalCount=0;
      
      //商品是否已经存在于订单列表中 
      let isHave=false;
      for(let i=0;i<this.tableData.length;i++){
        //如果列表中的goodsId等于传过来的goodsId，表示已经存在
        if(this.tableData[i].goodsId==goods.goodsId){
           isHave=true
        }
      }
       //根据判断的值编写业务逻辑
      if(isHave){
        //存在就进行数量添加
            let arr = this.tableData.filter(o =>o.goodsId == goods.goodsId);
            arr[0].count++;
            //console.log(arr);
      }else{
        //不存在就重新加入
        let newGoods={
          goodsId:goods.goodsId,
          goodsName:goods.goodsName,
          price:goods.price,
          count:1
        }
        //构造之后就加入到列表中
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
    },
    //删除单个商品
    delSingleGoods(goods){
      console.log(goods);
      this.tableData=this.tableData.filter(o => o.goodsId !=goods.goodsId);
      this.getAllMoney();
    },
    //汇总数量和金额
    getAllMoney(){
        this.totalCount=0;
        this.totalMoney=0;
        if(this.tableData){
            this.tableData.forEach((element) => {
            this.totalCount+=element.count;
            this.totalMoney=this.totalMoney+(element.price*element.count);   
        });
      }

    },
    //删除所有商品
    delAllGoods() {
      this.tableData = [];
      this.totalCount = 0;
      this.totalMoney = 0;
      // this.getAllMoney();
    }, 
    //模拟结账
    checkout() {
      if (this.totalCount!=0) {
         this.$message({
              message: '结账成功，感谢你又为店里出了一份力!',
              type: 'success'
          });
          this.tableData = [];
          this.totalCount = 0;
          this.totalMoney = 0;
      }else{
          this.$message.error('不能空结。老板了解你急切的心情！');
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#pos-order {
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
  /* 设置高度为100%不起作用，Element组件，会自动生产虚拟DOM，无法设置高度100%； */
}
.div-btn {
  margin-top: 10px;
}
.title {
  height: 20px;
  border-bottom: 1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li {
  list-style-type: none;
  float: left;
  border: 1px solid #f5f9f2;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
}
.often-price {
  color: #5887ff;
}
.goods-type {
  clear: both;
}
.cookList li{
       list-style: none;
       width:23%;
       border:1px solid #E5E9F2;
       height: auot;
       overflow: hidden;
       background-color:#fff;
       padding: 2px;
       float:left;
       margin: 2px;
       cursor: pointer;
   }
   .cookList li span{

        display: block;
        float:left;
   }
   .foodImg{
       width: 40%;
   }
   .foodName{
       font-size: 16px;
       padding-left: 10px;
       color:brown;

   }
   .foodPrice{
       font-size: 16px;
       padding-left: 10px;
       padding-top:10px;
   }
   .totalDiv{
      background-color: #fff;
      padding: 10px;
      border-bottom: 1px solid #d3dce6;
   }
</style>
