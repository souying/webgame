<template>
  <div id="Query">
    <h1>{{ msg }}</h1>
    <!-- address: "0xcAF35BA550ADFF051767f61bf8b1C6407CA2d150"
blockHash: "0x8bac8635162b1a963badcd4b3c388593f50e76ae1c9e55106b5c4b5c05361116"
blockNumber: 68013
event: "betIn"
id: "log_8bb034e3"
logIndex: 0
raw:
data: "0x000000000000000000000000e9f6d5f43b6d61d6cc146aafcb3e5af3c8f774e5000000000000000000000000000000000000000000000000000000000000012c"
topics: ['0xbcecf70a6530170bed34966cface87b39e7bad29c875b814a2f6fb60ff80d87b']
[[Prototype]]: Object
removed: false
returnValues: Result
0: "0xE9f6d5F43b6D61d6cC146aafCB3E5Af3C8f774E5"
1: "300"
[[Prototype]]: Object
signature: "0xbcecf70a6530170bed34966cface87b39e7bad29c875b814a2f6fb60ff80d87b"
transactionHash: "0xd788f0861c969339c412dd880f9c239885531abcc2302cbf8caef839d5a8c9d5"
transactionIndex: 0 -->
    <div class="query_box">
      <p class="query_box_p">
        <span class="query_content">transactionHash:</span
        ><el-input
          class="el-input"
          v-model="transactionHash"
          placeholder="请输入交易哈希值"
          clearable
        ></el-input>
      </p>
      <p class="query_box_p">
        <span class="query_content">blockNumber:</span
        ><el-input
          class="el-input"
          v-model="blockNumber"
          placeholder="请输入区块号"
          clearable
        ></el-input>
      </p>
      <p class="query_box_p">
        <span class="query_content">blockHash:</span
        ><el-input
          class="el-input"
          v-model="blockHash"
          placeholder="请输入区块哈希值"
          clearable
        ></el-input>
      </p>
      <p class="query_box_p">
        <span class="query_content">event:</span
        ><el-input
          class="el-input"
          v-model="event"
          placeholder="请输入查询事件"
          clearable
        ></el-input>
      </p>
      <p class="query_box_p">
        <el-button type="primary" @click="onScreen">筛选查询</el-button>
        <el-button type="success" @click="onEvent">所有事件查询</el-button>
      </p>
    </div>
    <!-- 展示内容 -->
    <div class="query_box2">
      <p class="query_box2_text">查询结果</p>
      <el-divider></el-divider>
      <div v-for="(item, i) in result" :key="i">
          <div>
              <p>address:{{item.address}}</p>
              <p>blockHash:{{item.blockHash}}</p>
              <p>blockNumber:{{item.blockNumber}}</p>
              <p>event:{{item.event}}</p>
              <p>transactionHash:{{item.transactionHash}}</p>
              <p>returnValues:{{item.returnValues}}</p>
              <el-divider></el-divider>
          </div>
      </div>
    </div>
  </div>
</template>

<script>
// 账号： 0xE9f6d5F43b6D61d6cC146aafCB3E5Af3C8f774E5
// 转账账号： 0x7a3e4ACB8E428cba0800A3518666Ba2Df071E711
// 矿机测试服：http://192.168.11.120:8548/
// 链ID：84350
// 合约地址：0xcAF35BA550ADFF051767f61bf8b1C6407CA2d150
// 注意钱包加配置！
// 引入web3.js@1.5.1

import Web3 from "web3";
// 智能合约生成的ABI参数，调用智能合约里的函数
import { abi } from "../assets/ABI/ABI.json";
// 签署交易的JavaScript库ethereumjs-tx@1.3.7
// import Tx from "ethereumjs-tx";
// 测试服地址
// const rpcURL = "http://192.168.11.120:8548";
// 建立Web3连接
// const web3 = new Web3(rpcURL);
const web3 = new Web3(
  Web3.givenProvider ||
    new Web3.providers.WebsocketProvider("ws://192.168.11.88:8545")
);

// 合约地址
const address = "0xcAF35BA550ADFF051767f61bf8b1C6407CA2d150";
// 通证智能合约的Javascript对象：
const contract = new web3.eth.Contract(abi, address);

export default {
  name: "Query",
  // 模板引入
  components: {},
  // 数据
  data() {
    return {
      msg: "Welcome to Query",
      result: [], //展示内容
      transactionHash: "", //绑定transactionHash
      blockNumber: "", //绑定blockNumber
      blockHash: "", //绑定blockHash
      event: "", //绑定event
    };
  },
  // 方法
  methods: {
    onEvent() {
      // getPastEvents读取合约历史事件
      contract.getPastEvents(
        "AllEvents", // 过滤事件参数，这里获取全部事件
        {
          filter: {
            //可选，按索引参数过滤事件
          },
          // 可选，仅读取从该编号开始的块中的历史事件,默认值为0
          fromBlock: 0, // 起始块
          // 可选，仅读取截止到该编号的块中的历史事件，默认值为"latest"
          toBlock: "latest", // 终止块
        },
        (err, events) => {
          //所有事件
          console.log(events);
          this.result = events;
        }
      );
    },
    onAll() {
      this.onEvent();
    },
    onScreen() {
      // 获取参数event
      let event = "AllEvents";
      if (this.event) {
        event = this.event;
      }
      // 获取参数blockNumber
      let fromBlock = 0;
      let toBlock = "latest";
      if (this.blockNumber) {
        fromBlock = this.blockNumber;
        toBlock = this.blockNumber;
      }
      // getPastEvents读取合约历史事件
      contract.getPastEvents(
        event, // 过滤事件参数，这里获取全部事件
        {
          filter: {
            //可选，按索引参数过滤事件
          },
          // 可选，仅读取从该编号开始的块中的历史事件,默认值为0
          fromBlock: fromBlock, // 起始块
          // 可选，仅读取截止到该编号的块中的历史事件，默认值为"latest"
          toBlock: toBlock, // 终止块
        },
        (err, events) => {
          //所有事件
          console.log(events);
          this.result = events;
          //  遍历筛选transactionHash
          //   let transactionHash = "";
          if (this.transactionHash) {
            // transactionHash = this.transactionHash;
            let arrEvent = events.filter((item) => {
              if (item.transactionHash == this.transactionHash) {
                // console.log(item);
                return item;
              }
            });
            // console.log(arrEvent);
            this.result = arrEvent;
          }
          //  遍历筛选transactionHash
          if (this.blockHash) {
            let arrEvent = events.filter((item) => {
              if (item.blockHash == this.blockHash) {
                // console.log(item);
                return item;
              }
            });
            // console.log(arrEvent);
            this.result = arrEvent;
          }
        }
      );
    },
  },
  // 创建后
  created() {},
  // 挂载后
  mounted() {},
  // 更新后
  updated() {},
};
</script>
<style lang="less" scoped>
#Query {
  width: 100%;
  padding-top: 2rem;
  h1 {
    color: rgb(112, 112, 9);
  }
}
.query_box {
  margin: auto;
  width: 34rem;
  border: 0.1rem solid #000;
  display: flex;
  flex-direction: column;
  .query_box_p {
    display: flex;
    justify-content: space-around;
    line-height: 4rem;
    .query_content {
      display: inline-block;
      width: 10rem;
    }
    .el-input {
      width: 20rem;
    }
  }
}
.query_box2 {
  margin: 2rem auto 0;
  width: 34rem;
  border: 0.1rem solid #000;
  .query_box2_text{
      font-size: 2rem;
      text-align: center;
      line-height: 5rem;
  }
}
</style>
