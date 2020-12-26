<template>
<div class="wrapper">
    <div class="header-wrapper">
      <div class="header-title">
      <span>空气质量-良好</span>
      <span>杭州市</span>
      </div>
      <div class="header-text">
      <span>55</span>
      <span>阴天</span>
      </div>
      <div class="weather-advice">空气质量良好，健康人群外出无需可以防护</div>
    </div>
  <div class="body-wrapper">
    <div class="data-wrapper">
      <div class="data">
        <img class="data-logo" src="/static/images/temperature.png"/>
        <div class="data-text">
          <div class="data-title">温度</div>
          <div class="data-value">{{Temp}}</div>
        </div>
      </div>
      <div class="data">
        <img class="data-logo" src="/static/images/humidity.png"/>
        <div class="data-text">
          <div class="data-title">湿度</div>
          <div class="data-value">{{Hum}}</div>
        </div>
      </div>
      </div>
      <div class="data-wrapper">
      <div class="data">
        <img class="data-logo" src="/static/images/light.png"/>
        <div class="data-text">
          <div class="data-title">光照度</div>
          <div class="data-value">{{Light}}</div>
        </div>
      </div>
       <div class="data">
        <img class="data-logo" src="/static/images/led.png"/>
        <div class="data-text">
          <div class="data-title">LED</div>
          <div class="data-value">
            <switch @change="onLedChange" :checked="Led" color="#3d7ef9"/>
          </div>
        </div>
      </div>
      </div>
      <div class="data-wrapper">
       <div class="data">
        <img class="data-logo" src="/static/images/Shower.png"/>
        <div class="data-text">
          <div class="data-title">洒水器</div>
          <div class="data-value">
            <switch @change="onShowerChange" :checked="Shower" color="#3d7ef9"/>
          </div>
        </div>
      </div>
    </div>
  </div>
  </div>
</template>
<script>
import card from '@/components/card'
import {connect} from 'mqtt/dist/mqtt'
const mqttUrl='wxs://mqtt.xyxlzqq.top:8084/mqtt'

export default {
  data () {
    return {
      client:{},
      Temp:0,
      Hum:0,
      Light:0,
      Led:false,
      Shower:false,
    }
  },

  components: {

  },

  methods: {
      onLedChange(event){
        let that = this
        let sw = event.mp.detail.value;
        if(sw){
          that.client.publish('/mysmarthome/sub','LED_SW:1',function (err){
            if(!err){
              console.log('成功下发命令——开灯')
            }
          })
        }else{
          that.client.publish('/mysmarthome/sub','LED_SW:0',function (err){
            if(!err){
              console.log('成功下发命令——关灯')
            }
          })
        }
      },
      onShowerChange(event){
        let that = this
        let sw = event.mp.detail.value;
        if(sw){
          that.client.publish('/mysmarthome/sub','Shower_SW:1',function (err){
            if(!err){
              console.log('成功下发命令——洒水')
            }
          })
        }else{
          that.client.publish('/mysmarthome/sub','Shower_SW:0',function (err){
            if(!err){
              console.log('成功下发命令——关水')
            }
          })
        }
      }
  },

  created () {
    // let app = getApp()
  },
  onShow(){
    let that = this;
    that.client = connect(mqttUrl);
    that.client.on('connect',function(){
      console.log("成功连接mqtt服务器!")
      console.log(that.client)
      that.client.subscribe("/mysmarthome/pub",function(err){
        if(!err){
          console.log("成功订阅!")
        }
      })
    })
    that.client.on("message",function(topic,message){
      console.log(topic);
      //console.log(message);
      //message是十六进制的buf流
      let dataFromDev = {}
      dataFromDev = JSON.parse(message)
      console.log(dataFromDev)
      that.Temp = dataFromDev.Temp
      that.Hum = dataFromDev.Hum
      that.Light = dataFromDev.Light
      that.Led = dataFromDev.Led
      that.Shower = dataFromDev.Shower
    })

  }
}
</script>

<style lang="scss" scoped>
.wrapper{
  padding:15px;
  .header-wrapper{
    background-color: #3d7ef9;
    border-radius: 20px;
    color:#fff;
    box-shadow:#d6d6d6 0px 0px 5px;
    padding:15px 30px;
    .header-title{
      display:flex;
      justify-content:space-between;
    }
    .header-text{
      font-size:32px;
      font-weight: 400;
      display: flex;
      justify-content: space-between;
    }
    .weather-advice{
      margin-top: 20px;
      font-size: 12px;
      }
  }
  .data-wrapper{
    margin-top: 20px;
    display: flex;
    justify-content: space-between;
    .data{
      background-color: #fff;
      width:150px;
      height: 80px;
      border-radius: 20px;
      display: flex;
      justify-content: space-between;
      padding:0 8px;
      box-shadow: #d6d6d6 0px 0px 5px;
     .data-logo{
       height:50px;
       width:50px;
       margin-top:15px;
     }
     .data-text{
       margin-top: 15px;
       color: #7f7f7f;
       .data-title{
         text-align: right;
       }
       .data-value{
         font-size:26px;
       }
     }
    }
  }
}

</style>
