<template>
    <div id="weaMain" class="container mb-5">
        <div class="text-white font-weight-light my-3" id="weaCity">
            &nbsp;
            <span class="material-icons-round align-text-bottom" data-toggle="modal" data-target="#locationModal" id="locationOn">location_on</span>
            <span id="weaCityText">{{cityName}}</span>
        </div>
        <div class="text-white display-1 my-3" id="weaTemp">
            &nbsp;&nbsp;&nbsp;{{nowData.temp}}°
            <span class="text-white lead font-weight-normal my-3" id="weaToday">
                {{nowData.text}}
            </span>

        </div>
        <div class="m-4">
            <img :src="'img/icon/'+nowData.icon+'.svg'" id="weaTodayIcon" alt="weaTodayIcon">
        </div>
        <transition name="fade">
            <div class="text-white my-5 font-weight-light" id="lifeText">{{lifeText}}</div>
        </transition>

        <div class="row justify-content-center">
            <div class="col-sm-12 col-md-6 mt-4">
                <div class="card shadow-lg border-0 w-100 text-left mb-4">
                    <div class="card-header">
                        <span class="lead">实时天气</span>
                        <span class="float-right text-muted wea-update-time">数据更新于{{ nowData.obsTime }}</span>
                    </div>
                    <ul class="list-group list-group-flush">
                        <li class="list-group-item">
                            <img src="img/icon/wind.svg" class="wea-now-icon">
                            <span class="m-4 ">{{nowData.windDir}}</span>
                            <span class="float-right">&nbsp;{{nowData.windScale}}级</span>
                        </li>
                        <li class="list-group-item">
                            <img src="img/icon/humidity.svg" class="wea-now-icon">
                            <span class="m-4 ">湿度</span>
                            <span class="float-right">{{nowData.humidity}}%</span>
                        </li>
                        <li class="list-group-item">
                            <img src="img/icon/gauge.svg" class="wea-now-icon">
                            <span class="m-4 ">气压</span>
                            <span class="float-right">{{nowData.pressure}}hPa</span>
                        </li>
                        <li class="list-group-item">
                            <img src="img/icon/clouds.svg" class="wea-now-icon">
                            <span class="m-4 ">云量</span>
                            <span class="float-right">{{nowData.cloud}}%</span>
                        </li>
                        <li class="list-group-item">
                            <img src="img/icon/leaf2.svg" class="wea-now-icon" style="color: red">
                            <span class="m-4 ">空气质量指数</span>
                            <span class="float-right">{{airData.category}}</span>
                            <span class="float-right" id="airNum">{{airData.aqi}}&nbsp;</span>
                        </li>
                    </ul>
                </div>
                <div class="card shadow-lg border-0 w-100 text-left">
                    <div class="card-header">
                        <span class="lead">24h温度&times;降水量</span>
                    </div>
                    <div id="main" class="w-100" style="height: 300px"></div>
                </div>
            </div>

            <div class="col-sm-12 col-md-6 my-4">
                <div class="card shadow-lg border-0 w-100 ">
                    <div class="card-header text-left lead">
                        {{dailyDataNum}}天预报
                        <span class="mdc-chip-set mdc-chip-set1 user-select-none d-inline-block float-right p-0 h-25px" role="grid">
                            <div class="mdc-chip m-0 h-25px" @click="swn">
                                <div class="mdc-chip__ripple"></div>
                                <span role="gridcell">
                                    <span role="button" tabindex="0" class="mdc-chip__primary-action">
                                        <span class="mdc-chip__text text-muted">{{dailyDataNumRev}}天预报</span>
                                    </span>
                                </span>
                            </div>
                        </span>
                    </div>
                    <ul class="list-group list-group-flush" id="wea15dList" role="tablist">
                        <transition-group name="list">
                            <li v-for="(item,index) in dailyData" :key="item" class="list-group-item">
                            <span v-if="index==0" class="float-left w-25 text-left pl-1">
                                <div class="wea-week">今天</div>
                                <div class="wea-date text-muted">{{item.fxDate}}</div>
                            </span>
                                <span v-else class="float-left w-25 text-left pl-1">
                                <div class="wea-week">{{date2week(item.fxDate)}}</div>
                                <div class="wea-date text-muted">{{item.fxDate}}</div>
                            </span>
                                <span>
                                <img :src="'./img/icon/'+item.iconDay+'.svg'" class="wea-15d-icon" alt="wea-icon" :title="item.textDay">
                            </span>
                                <span class="wea-temp float-right w-25 text-info">{{item.tempMin}}° - {{item.tempMax}}°</span>
                            </li>
                        </transition-group>
                    </ul>
                </div>
            </div>
        </div>
    </div>
    <!-- snackbar -->
    <div class="mdc-snackbar mySnackbar">
        <div class="mdc-snackbar__surface">
            <div class="mdc-snackbar__label font-weight-light" role="status" aria-live="polite">
                {{snackbarTx}}
            </div>
        </div>
    </div>
    <!-- FAB -->
    <div class="mdc-touch-target-wrapper">
        <button class="mdc-fab" aria-label="refresh" @click="update" >
            <div class="mdc-fab__ripple"></div>
            <span class="material-icons-round material-icons" id="myFabIcon">refresh</span>
        </button>
    </div>
    <!-- Modal -->
    <div class="modal fade" id="locationModal" tabindex="-1" aria-labelledby="locationModalLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="locationModalLabel">切换城市</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body text-left">
                    <form>
                        <div class="form-group">
<!--                            <label for="exampleInputEmail1">Email address</label>-->
                            <input type="text" class="form-control" v-model="searchCity" id="cityInput" placeholder="搜索市、区、县等" aria-describedby="emailHelp">
                        </div>
                    </form>
                    <div class="text-muted">&nbsp;&nbsp;&nbsp;建议</div>
                    <div class="mdc-chip-set mdc-chip-set2 user-select-none" role="grid">
                        <div v-for="item in cityData" :key="item" class="mdc-chip" @click="chc(item.id,item.name)" data-dismiss="modal" role="row">
                            <div class="mdc-chip__ripple"></div>
                            <span role="gridcell">
                                <span role="button" tabindex="0" class="mdc-chip__primary-action">
                                    <span class="mdc-chip__text">{{item.name}}</span>
                                </span>
                            </span>
                        </div>
                    </div>

                </div>

            </div>
        </div>
    </div>
</template>

<script>
    // import $ from "jquery";
    import {MDCChipSet} from '@material/chips';
    import {MDCRipple} from '@material/ripple';
    import {MDCSnackbar} from '@material/snackbar';
    // 引入 ECharts 主模块
    var echarts = require('echarts/lib/echarts');
    require('echarts/lib/chart/bar');
    require('echarts/lib/chart/line');
    import light from "echarts/src/theme/light";
    require("zrender/lib/svg/svg");
    require("echarts/lib/component/dataZoom");

    export default {
        name: 'weaMain',
        props: {
            msg: String
        },
        data(){
          return{
              dailyData:[],
              dailyDataExt:[],
              dailyDataNum:7,
              dailyUdt:'',
              nowData:[],
              nowUdt:'',
              hourlyData:[],
              lifeData:[],
              lifeText:'',
              lifeTxInt:'',
              airData:[],
              cityData:[],
              searchCity:'',
              chcFlag:true,
              location:'101280703',
              cityName:'珠海',
              snackbarTx:'',
          }
        },
        computed:{
            dailyDataNumRev(){
                return (this.dailyDataNum===7) ? 15 : 7;
            },
            hourlyTemp(){
                let hourlyTemp = [];
                for (let val of this.hourlyData){
                    hourlyTemp.push([val.fxTime,val.temp])
                }
                return hourlyTemp;
            },
            hourlyPrecip(){
                let hourlyPrecip = [];
                for (let val of this.hourlyData){
                    hourlyPrecip.push([val.fxTime,val.precip])
                }
                return hourlyPrecip;
            }
        },
        watch:{
            searchCity(val){
                //搜索城市
                fetch('https://geoapi.heweather.net/v2/city/lookup?location='+val+'&key=5bb20c4da2c3457bb0ee0a709bbea01e')
                    .then(response => response.json())
                    .catch(err => console.error(err))
                    .then(response => this.cityData = response.location)
                    .then(()=>{
                        const chipSetEl2 = document.querySelector('.mdc-chip-set2');
                        new MDCChipSet(chipSetEl2);
                    });
                //热门城市
                if (val===''){
                    fetch('https://geoapi.heweather.net/v2/city/top?key=5bb20c4da2c3457bb0ee0a709bbea01e&number=20')
                        .then(response => response.json())
                        .catch(err => console.error(err))
                        .then(response => this.cityData = response.topCityList)
                        .then(()=>{
                            const chipSetEl2 = document.querySelector('.mdc-chip-set2');
                            new MDCChipSet(chipSetEl2);
                        });
                }
            },
        },
        methods: {
            date2week(dateStr) {
                const weekDay = ["星期天", "星期一", "星期二", "星期三", "星期四", "星期五", "星期六"];
                // var dateStr = "2008-08-08 08:08:08"
                let myDate = new Date(Date.parse(dateStr.replace(/-/g, "/")));
                return weekDay[myDate.getDay()];
            },
            chc(id,name){
                this.location = id;
                this.cityName = name;
                localStorage.setItem('location',id);
                localStorage.setItem('cityName',name);
                this.chcFlag=true;
                this.update();
            },
            swn(){
                this.dailyDataNum = (this.dailyDataNum===7) ? 15 : 7;
                if (this.dailyDataNum===15) this.dailyData=[...this.dailyData,...this.dailyDataExt];
                else this.dailyData=this.dailyData.slice(0,7);
            },
            update(){
                this.dailyDataNum=7;
                document.getElementById('myFabIcon').classList.add('rotateFab');
                setTimeout(()=>{
                    document.getElementById('myFabIcon').classList.remove('rotateFab');
                },1000);
                //15天预报
                fetch('https://devapi.heweather.net/v7/weather/15d?location='+this.location+'&key=5bb20c4da2c3457bb0ee0a709bbea01e')
                    .then(response => response.json())
                    .catch(err => console.error(err))
                    .then(response => {
                        if (response.updateTime!==this.dailyUdt||this.chcFlag){
                            this.dailyData = response.daily.slice(0,7);
                            this.dailyDataExt = response.daily.slice(7);
                            this.dailyUdt = response.updateTime;
                        }
                    });
                //实况天气
                fetch('https://devapi.heweather.net/v7/weather/now?location='+this.location+'&key=5bb20c4da2c3457bb0ee0a709bbea01e')
                    .then(response => response.json())
                    .catch(err => console.error(err))
                    .then(response => {
                        if (response.updateTime!==this.nowUdt||this.chcFlag){
                            this.nowData = response.now;
                            this.nowData.obsTime = this.nowData.obsTime.substr(11,5);
                            let bdClass = 'sunny';
                            let cloudyCode = [104,154,300,301,302,303,304,305,306,307,308,309,
                                310,311,312,313,314,315,316,317,318,350,351,399];
                            if (cloudyCode.includes(parseInt(this.nowData.icon))) bdClass='cloudy';
                            const body = document.getElementsByTagName('body')[0];
                            body.setAttribute('class',bdClass);

                            this.nowUdt = response.updateTime;
                            this.snackbarTx = '数据更新完成<(￣︶￣)>';
                        }
                        else {this.snackbarTx = '数据暂无更新';}
                        setTimeout(()=>{
                            //snackbar
                            const snackbar = new MDCSnackbar(document.querySelector('.mdc-snackbar'));
                            snackbar.open();
                            this.chcFlag=false;
                        },1000);

                    });
                //24h天气
                fetch('https://devapi.heweather.net/v7/weather/24h?location='+this.location+'&key=5bb20c4da2c3457bb0ee0a709bbea01e')
                    .then(response => response.json())
                    .catch(err => console.error(err))
                    .then(response => {
                        this.hourlyData = response.hourly;
                        // ECharts
                        let myChart = null;
                        myChart = echarts.init(document.getElementById('main'),light, {renderer: 'svg'});
                        myChart.setOption({
                            grid:{
                                height: '80%',
                                width: '86%',
                                left: 36,
                                top: 50
                            },
                            dataZoom: [{
                                type: 'slider',
                                show: true,
                                start: 0,
                                end: 33,
                                zoomLock:true,
                                showDetail: false,
                                bottom: '5%'
                            }],
                            xAxis: {
                                type:'time',
                                axisLine: {show: false},
                                axisTick: {show: false},
                                splitLine: {show: false},
                                position: 'top',
                                minInterval: 3600 * 1000,
                                axisLabel: {
                                    showMinLabel: false,
                                    showMaxLabel: false,
                                    fontSize: 16,
                                    formatter: function (value) {
                                        let date = new Date(value);
                                        let timeStr = date.format('hh:mm');
                                        if (timeStr === '00:00') return '明天';
                                        return timeStr;
                                    }
                                }
                            },
                            yAxis: {
                                show: false,
                                boundaryGap: ['20%', '30%']
                            },
                            series: [{
                                name: '温度',
                                type: 'line',
                                color: '#FBC02D',
                                smooth: true,
                                lineStyle: {width: 3},
                                symbol: 'circle',
                                symbolSize: 8,
                                label: {
                                    show: true,
                                    fontSize: 16,
                                    formatter: function (params) {
                                        return params.value[1]+'°';
                                    }
                                },
                                data: this.hourlyTemp
                            },
                                {
                                    name: '降水量',
                                    type: 'bar',
                                    color:'#2196F3',
                                    smooth: true,
                                    itemStyle: {
                                        barBorderRadius: [50, 50, 0, 0]
                                    },
                                    barWidth: "10%",
                                    label: {
                                        show: true,
                                        position: 'top',
                                        fontSize: 16,
                                        formatter: function (params) {
                                            let val = params.value[1];
                                            if (val==='0.0') return '';
                                            return val+'mm';
                                        }
                                    },
                                    data: this.hourlyPrecip
                                }]
                        });
                    });
                //生活指数
                fetch('https://devapi.heweather.net/v7/indices/1d?location='+this.location+'&key=5bb20c4da2c3457bb0ee0a709bbea01e&type=1,3,5,6,8,10,11,15')
                    .then(response => response.json())
                    .catch(err => console.error(err))
                    .then(response => {
                        this.lifeData = response.daily;
                        this.lifeText = this.lifeData[Math.floor(Math.random()*8)].text;
                        clearInterval(this.lifeTxInt);
                        this.lifeTxInt = setInterval(()=>{
                            // console.log("lifeTxInt ",this.lifeTxInt);
                            // console.log('old(',(new Date).getSeconds(),'):',this.lifeText);
                            document.getElementById('lifeText').style.opacity='0';
                            setTimeout(()=>{
                                this.lifeText = this.lifeData[Math.floor(Math.random()*8)].text;
                                document.getElementById('lifeText').style.opacity='1';
                            }, 1000);
                        },20*1000);
                    });
                //实况空气质量
                fetch('https://devapi.heweather.net/v7/air/now?location='+this.location+'&key=5bb20c4da2c3457bb0ee0a709bbea01e')
                    .then(response => response.json())
                    .catch(err => console.error(err))
                    .then(response => {
                        this.airData = response.now;
                        let airColor;
                        if (this.airData.aqi<=50) airColor = '#4CAF50';
                        else if (this.airData.aqi<=100) airColor = '#FDD835';
                        else if (this.airData.aqi<=150) airColor = '#FF9800';
                        else if (this.airData.aqi<=200) airColor = '#F44336';
                        else if (this.airData.aqi<=300) airColor = '#9C27B0';
                        else airColor = '#800000';
                        document.getElementById('airNum').style.color = airColor;
                    });
                // console.log('update---',this)
            }
        },
        created() {
            // console.log('created');
            if (localStorage.getItem('location')===null){
                localStorage.setItem('location','101280703');
                localStorage.setItem('cityName','珠海');
            } else {
                this.location=localStorage.getItem('location');
                this.cityName=localStorage.getItem('cityName')
            }
        },
        mounted () {
            // console.log('mounted');
            this.update();
            //热门城市
            fetch('https://geoapi.heweather.net/v2/city/top?key=5bb20c4da2c3457bb0ee0a709bbea01e&number=20')
                .then(response => response.json())
                .catch(err => console.error(err))
                .then(response => this.cityData = response.topCityList)
                .then(()=>{
                    const chipSetEl2 = document.querySelector('.mdc-chip-set2');
                    new MDCChipSet(chipSetEl2);
                });
            const chipSetEl = document.querySelector('.mdc-chip-set1');
            const chipSet = new MDCChipSet(chipSetEl);//eslint-disable-line no-unused-vars
            new MDCRipple(document.querySelector('.mdc-fab'));

        },
        updated() {}

    }
</script>

<style lang="scss">
    @use "@material/theme" with (
      //$primary: #FBC02D,
      $secondary: #FFFDE7,
      //$on-primary: #442b2d,
      $on-secondary: #FBC02D,
    );
    @use "@material/snackbar/mdc-snackbar";
    @use "@material/chips/mdc-chips";
    @use "@material/fab";
    @include fab.core-styles;

    .list-enter-active {
        transition: all .6s ease;
    }
    .list-leave-active{
        transition: all .6s ease;
    }
    .list-enter-from,
    .list-leave-to {
        opacity: 0;
        height: 0;
        transform: translateY(30px) rotateX(90deg);
    }
</style>
<style scoped>
    #weaCity{ height: 32px }
    #weaCityText{ line-height: 32px }
    #locationOn{
        transition: opacity 0.4s;
        opacity: 0.8;
    }
    #locationOn:hover{ opacity: 1 }
    #weaTodayIcon{ width: 100px }
    .wea-15d-icon{ width: 36px }
    .wea-now-icon{
        width: 24px;
        line-height: 30px;
        margin-bottom: 4px;
    }
    #lifeText{
        transition: all 0.8s;
    }
    #wea15dList{ line-height: 36px }
    .wea-week{ line-height: 20px }
    .wea-date{
        margin-top: 4px;
        font-size: 12px;
        line-height: 16px;
    }
    .wea-update-time{
        font-size: 14px;
        margin-top: 8px;
    }
    .h-25px{
        height: 28px;
    }
    .mdc-fab{
        position: fixed;
        right: 48px;
        bottom: 48px;
    }
    .rotateFab{
        animation: 1s ease-in both rotateFab;
    }
    @keyframes rotateFab {
        from { transform: rotate(0deg); }
        to   { transform: rotate(360deg); }
    }
</style>
