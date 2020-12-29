<template>
<div class="wrapper">
    <div class="header-wrapper">
      <div class="header-title">
      <span>空气质量-{{AirText}}</span>
      <span>{{Area}}-{{City}}</span>
      </div>
      <div class="header-text">
      <span>{{AirValue}}</span>
      <span>{{weather}}</span>
      </div>
      <div class="weather-advice">{{weatherAdvice}}</div>
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
      Area:'请求中',
      City:'请求中',
      AirText:'请求中',//空气优良
      AirValue:0,//空气指数
      weather:'请求中',//天气
      weatherAdvice:'请求中'//天气建议
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
      let dataFromDev = {}
      dataFromDev = JSON.parse(message)
      console.log(dataFromDev)
      that.Temp = dataFromDev.Temp
      that.Hum = dataFromDev.Hum
      that.Light = dataFromDev.Light
      that.Led = dataFromDev.Led
      that.Shower = dataFromDev.Shower
    });
    wx.getLocation({
      type: 'wgs84',
      success (res) {
        const  type = 0;
        const latitude = res.latitude
        const longitude = res.longitude
        const key = '8247bd295aed49f0a75787bf24cd932b'
        wx.request({
          url: `https://devapi.qweather.com/v7/weather/now?location=${longitude},${latitude}&key=${key}`, //仅为示例，并非真实的接口地址
          success (res) {
            const {now}=res.data
            that.weather = now.text
          }
        })
        wx.request({
            url: `https://devapi.qweather.com/v7/air/now?location=${longitude},${latitude}&key=${key}`, //仅为示例，并非真实的接口地址
            success (res) {
              const {now}=res.data
              that.AirValue = now.aqi
              that.AirText = now.category
            }
          })
        wx.request({
          url: `https://geoapi.qweather.com/v2/city/lookup?location=${longitude},${latitude}&key=${key}`, //仅为示例，并非真实的接口地址
          success (res) {
            const {location} = res.data
            that.Area = location[0].adm2
            that.City = location[0].name
          }
        })
        wx.request({
          url: `https://devapi.qweather.com/v7/indices/1d?location=${longitude},${latitude}&key=${key}&type=${type}`, //仅为示例，并非真实的接口地址
          success (res) {
            that.weatherAdvice = res.data.daily[1].text
          }
        })
      }
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
