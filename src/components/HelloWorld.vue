<template>
  <div id="main">
  </div>
</template>
<script src="echarts.min.js"></script>
<script>
    import echarts from 'echarts'
export default {
  name: 'HelloWorld',
  data () {
    return {
      client : new Paho.MQTT.Client('10.14.207.185', 8080, '111'),//第三个参数是clientID可以为空
      topic: '/APPKEY001/UPDATE/#'
    }
  },
  methods: {
    test () {
      console.log('000')

    },
    onConnect() {
      console.log("onConnected");
      this.client.subscribe(this.topic);//订阅主题
    },
    onConnectionLost (responseObject) {
      if (responseObject.errorCode !== 0) {
        console.log("onConnectionLost:" + responseObject.errorMessage);
        console.log("连接已断开");
      }
    },
    onMessageArrived (message) {
      let test = JSON.parse(message.payloadString)
      console.log("收到消息:", test.vehicleData.SOC)
      let myChart = echarts.init(document.getElementById('main'))
      let option = {
        tooltip : {
          formatter: "{a} <br/>{b} : {c}%"
        },
        toolbox: {
          feature: {
            restore: {},
            saveAsImage: {}
          }
        },
        series: [
          {
            name: '业务指标',
            type: 'gauge',
            detail: {formatter:'{value}%'},
            data: [{value: 50, name: '剩余电量'}]
          }
        ]
      }
      option.series[0].data[0].value = (test.vehicleData.SOC).toFixed(2) - 0
      myChart.setOption(option)
//      setInterval(setRandom, 2000)
//      function setRandom() {
//      }
    },
  },
  mounted () {
    this.test()
  },
  created () {
    console.log('888')
    this.client.connect({
      userName: 'admin',
      password: '123456',
      timeout: 10,
      useSSL: false,
      onSuccess: this.onConnect
      })
    this.client.onConnectionLost = this.onConnectionLost;//注册连接断开处理事件
    this.client.onMessageArrived = this.onMessageArrived;//注册消息接收处理事件
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
 #main {
    height: 400px;
    width: 50%;
    margin-left: 25%;
}
</style>
