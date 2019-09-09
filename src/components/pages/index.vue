<template>
    <div class="page">
        <div class="header">
            <div class="title">
                年销售全览
            </div>
            <div class="title-bottom">
                <div class="bottom-sidebar"></div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-4">
                <div class="col-sm-12 col-md-12">
                    <div class="box-content">
                        <div class="statistic-box row">
                            <div class="statistic-item col-md-6" v-for="item in [1,2,3,4,5,6]" v-bind:key="item">
                                <div class="icon-content">
                                    <span class="iconfont icon-shuju"></span>
                                </div>
                                <div class="text-content">
                                    <div class="text">
                                        <span class="counter">
                                            <ICountUp :delay="delay" :endVal="endVal" :options="options"
                                                @ready="onReady" />
                                        </span>
                                    </div>
                                    <div class="sub-title">指标</div>
                                </div>

                            </div>

                        </div>

                    </div>
                </div>
                <div class="col-md-12">
                    <div class="piechart"></div>
                    <table class="table border-table darkblue" style="width: 100%" cellpadding="0" cellspacing="0">
                        <tr>
                            <td class="th" style="width: 90px;">排名</td>
                            <td class="th">区域</td>
                            <td class="th td_right">
                                年销售量
                            </td>
                            <td class="th td_right">
                                月销售量
                            </td>
                            <!--<th>二级部门</th>-->
                        </tr>
                        <tr v-for="(item,index) in datalist">
                            <td class="border_bottom border_left_no">{{index+1}}</td>
                            <td class="border_bottom enable" @click='tdSelect(item)'>
                                {{item.area}}
                            </td>
                            <td class="td_right">
                                {{item.nValue}}
                            </td>
                            <td class="td_right">
                                {{item.yValue}}

                            </td>
                        </tr>
                    </table>
                </div>

            </div>

            <div class="col-md-8">
                <div class="row">
                    <div class="col-sm-12 col-md-12">
                        <div class="box-content">
                <div style="height:1000px;" id="mapContainer"></div>
                            <div class="caption">
                                <h3>Thumbnail label</h3>
                                <p>Cras justo odio, dapibus ac facilisis in, egestas eget quam. Donec id elit non mi
                                    porta gravida at eget metus. Nullam id dolor id nibh ultricies vehicula ut id elit.
                                </p>
                                <p><a href="#" class="btn btn-primary" role="button" @click="changeData">Button</a>
                                    <a href="#" class="btn btn-default" role="button">Button</a></p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="footer">
                <div class="bottom-line"></div>

        </div>
    </div>
</template>
<script>
    import axios from 'axios';
    import ICountUp from 'vue-countup-v2'
    // import 'echarts/map/js/china.js'
    import chinaJson from 'echarts/map/json/china.json'
    //饼图数据
    var pieData = [
        [{
            "name": "A系统",
            "value": 2
        }, {
            "name": "B系统",
            "value": 4
        }, {
            "name": "C系统",
            "value": 3
        }, {
            "name": "D系统",
            "value": 3
        }, {
            "name": "E系统",
            "value": 7
        }, {
            "name": "F系统",
            "value": 3
        }, {
            "name": "G系统",
            "value": 3
        }, {
            "name": "H系统",
            "value": 3
        }],
        ["A系统", "B系统", "C系统", "D系统", "E系统", "F系统", "G系统", "H系统"]
    ];

    //折线图数据
    var yqyData = [
        [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
        [600, 1150, 400, 850, 600, 650, 100, 1150, 100, 700, 600, 800, 500]
    ]

    //-------------------------------关系图数据获取:start，有点多，可先忽略查看-------------------------------------------

    
    var mydata = [
        { name: '北京', value: '100' }, { name: '天津', value: randomData() },
        { name: '上海', value: randomData() }, { name: '重庆', value: randomData() },
        { name: '河北', value: randomData() }, { name: '河南', value: randomData() },
        { name: '云南', value: randomData() }, { name: '辽宁', value: randomData() },
        { name: '黑龙江', value: randomData() }, { name: '湖南', value: randomData() },
        { name: '安徽', value: randomData() }, { name: '山东', value: randomData() },
        { name: '新疆', value: randomData() }, { name: '江苏', value: randomData() },
        { name: '浙江', value: randomData() }, { name: '江西', value: randomData() }

    ];

    var optionMap = {
        title: {
            text: '全国地图大数据',
            subtext: '',
            x: 'center',
            textStyle: {
                color: "#fff"
            }
        },
        tooltip: {
            trigger: 'item'
        },

        //左侧小导航图标
        visualMap: {
            show: true,
            x: 'left',
            y: 'center',
            textStyle: {
                color: "#8fc8f2"
            },
            splitList: [
                { start: 500, end: 600 }, { start: 400, end: 500 },
                { start: 300, end: 400 }, { start: 200, end: 300 },
                { start: 100, end: 200 }, { start: 0, end: 100 }
            ],
            color: ['#5475f5', '#9feaa5', '#85daef', '#74e2ca', '#e6ac53', '#9fb5ea']
        },

        //配置属性
        series: [{
            name: '数据',
            type: 'map',
            mapType: 'china',
            roam: true,
            label: {
                normal: {
                    show: true  //省份名称  
                },
                emphasis: {
                    show: false
                }
            },
            data: mydata  //数据
        }]
    };
 
    function renderItem(params, api) {
        var coords = [
            [116.7, 39.53],
            [103.73, 36.03],
            [112.91, 27.87],
            [120.65, 28.01],
            [119.57, 39.95]
        ];
        var points = [];
        for (var i = 0; i < coords.length; i++) {
            points.push(api.coord(coords[i]));
        }
        var color = api.visual('color');

        return {
            type: 'polygon',
            shape: {
                points: echarts.graphic.clipPointsByRect(points, {
                    x: params.coordSys.x,
                    y: params.coordSys.y,
                    width: params.coordSys.width,
                    height: params.coordSys.height
                })
            },
            style: api.style({
                fill: color,
                stroke: echarts.color.lift(color)
            })
        };
    }

    function randomData() {
        return Math.round(Math.random() * 500);
    }

    
    
    var COLORS = ["#070093", "#1c3fbf", "#1482e5", "#70b4eb", "#b4e0f3", "#ffffff"];

    var multiChartOption = {
        title: {
            text: '某地区蒸发量和降水量',
            subtext: '纯属虚构'
        },
        xAxis: [{
            data: yqyData[0],
            gridIndex: 0
        }, //折线图x轴数据赋值，指定坐标信息
        {
            gridIndex: 1,
            type: 'value'
        },
        {
            gridIndex: 2,
            type: 'category',
            data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']
        }
        ],
        yAxis: [{
            name: '交易量',
            splitLine: {
                show: false
            },
            gridIndex: 0
        }, {
            gridIndex: 1,
            type: 'value'
        },
        {
            gridIndex: 2,
            type: 'value'
        }],

        grid: [ //指定坐标轴位置，大小
            {
                x: '7%',
                y: '7%',
                width: '50%',
                height: '31%'
            }, {
                x: '60%',
                height: '40%',
                width: '35%',
                contianLabel: true
            },
            {
                x: '60%',
                bottom: '8%',
                height: '40%',
                width: '35%',
                contianLabel: true
            }
        ],

        series: [{
            type: 'line',
            xAxisIndex: 0, //指定折线图数据显示到：grid坐标系：0
            yAxisIndex: 0,
            showSymbol: false,
            data: yqyData[1] //折线图y轴数据赋值
        },

        {
            name: '面积模式',
            type: 'pie',
            radius: [10, 80],
            center: ['18%', '75%'],
            data: pieData[0] //饼图数据赋值
        },
        {

            xAxisIndex: 2,
            yAxisIndex: 2,
            name: '蒸发量',
            type: 'bar',
            data: [2.0, 4.9, 7.0, 23.2, 25.6, 76.7, 135.6, 162.2, 32.6, 20.0, 6.4, 3.3],
            markPoint: {
                data: [
                    { type: 'max', name: '最大值' },
                    { type: 'min', name: '最小值' }
                ]
            },
            markLine: {
                data: [
                    { type: 'average', name: '平均值' }
                ]
            }
        },
        {
            xAxisIndex: 2,
            yAxisIndex: 2,
            name: '降水量',
            type: 'bar',
            data: [2.6, 5.9, 9.0, 26.4, 28.7, 70.7, 175.6, 182.2, 48.7, 18.8, 6.0, 2.3],
            markPoint: {
                data: [
                    { name: '年最高', value: 182.2, xAxis: 7, yAxis: 183 },
                    { name: '年最低', value: 2.3, xAxis: 11, yAxis: 3 }
                ]
            },
            markLine: {
                data: [
                    { type: 'average', name: '平均值' }
                ]
            }
        },
        { // 进行相关配置
            type: 'map',

                        backgroundColor: 'transparent',
                        tooltip: {}, // 鼠标移到图里面的浮动提示框
                        dataRange: {
                            show: false,
                            min: 0,
                            max: 1000,
                            text: ['High', 'Low'],
                            realtime: true,
                            calculable: true,
                            //  color: ['orangered', 'yellow', 'lightskyblue']
                        },

                        geo: { // 这个是重点配置区
                            map: 'china', // 表示中国地图
                            roam: true,
                            label: {
                                normal: {
                                    show: true, // 是否显示对应地名
                                    textStyle: {
                                        color: 'rgba(0,0,0,0.4)'
                                    }
                                }
                            },
                            itemStyle: {
                                normal: {
                                    borderColor: 'rgba(0, 0, 0, 0.2)',
                                    //    areaColor: "#5698d9",
                                    color: "#5698d9"
                                },
                                emphasis: {
                                    //    areaColor: "#5698d9",
                                    shadowOffsetX: 0,
                                    shadowOffsetY: 0,
                                    shadowBlur: 20,
                                    borderWidth: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                },
                                itemStyle: {
                                    normal: {
                                        color: '#8fc8f2',
                                        shadowBlur: 10,
                                        shadowColor: '#333'
                                    }
                                }
                            }
                        },

                        visualMap: {
                            min: 0,
                            max: 200,
                            calculable: true,
                            inRange: {
                                color: ['#50a3ba', '#eac736', '#d94e5d']
                            },
                            textStyle: {
                                color: '#fff'
                            }
                        },
                        bmap: {
                            center: [104.114129, 37.550339],
                            zoom: 5,
                            roam: true,
                            mapStyle: {
                                styleJson: [
                                    {
                                        "featureType": "water",
                                        "elementType": "all",
                                        "stylers": {
                                            "color": "#044161"
                                        }
                                    },
                                    {
                                        "featureType": "land",
                                        "elementType": "all",
                                        "stylers": {
                                            "color": "#004981"
                                        }
                                    },
                                    {
                                        "featureType": "boundary",
                                        "elementType": "geometry",
                                        "stylers": {
                                            "color": "#064f85"
                                        }
                                    },
                                    {
                                        "featureType": "railway",
                                        "elementType": "all",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "highway",
                                        "elementType": "geometry",
                                        "stylers": {
                                            "color": "#004981"
                                        }
                                    },
                                    {
                                        "featureType": "highway",
                                        "elementType": "geometry.fill",
                                        "stylers": {
                                            "color": "#005b96",
                                            "lightness": 1
                                        }
                                    },
                                    {
                                        "featureType": "highway",
                                        "elementType": "labels",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "arterial",
                                        "elementType": "geometry",
                                        "stylers": {
                                            "color": "#004981"
                                        }
                                    },
                                    {
                                        "featureType": "arterial",
                                        "elementType": "geometry.fill",
                                        "stylers": {
                                            "color": "#00508b"
                                        }
                                    },
                                    {
                                        "featureType": "poi",
                                        "elementType": "all",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "green",
                                        "elementType": "all",
                                        "stylers": {
                                            "color": "#056197",
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "subway",
                                        "elementType": "all",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "manmade",
                                        "elementType": "all",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "local",
                                        "elementType": "all",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "arterial",
                                        "elementType": "labels",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    },
                                    {
                                        "featureType": "boundary",
                                        "elementType": "geometry.fill",
                                        "stylers": {
                                            "color": "#029fd4"
                                        }
                                    },
                                    {
                                        "featureType": "building",
                                        "elementType": "all",
                                        "stylers": {
                                            "color": "#1a5787"
                                        }
                                    },
                                    {
                                        "featureType": "label",
                                        "elementType": "all",
                                        "stylers": {
                                            "visibility": "off"
                                        }
                                    }
                                ]
                            }
                        },

                        series: [{
                            type: 'scatter',
                            coordinateSystem: 'geo' // 对应上方配置

                        },
                        {
                            name: 'pm2.5',
                            type: 'scatter',
                            coordinateSystem: 'bmap',
                            data: [{ "name": "上海", "value": [121.48, 31.22, 25] }],
                            symbolSize: function (val) {
                                return val[2] / 10;
                            },
                            label: {
                                normal: {
                                    formatter: '{b}',
                                    position: 'right',
                                    show: false
                                },
                                emphasis: {
                                    show: true
                                }
                            },
                            itemStyle: {
                                normal: {
                                    color: '#ddb926'
                                }
                            }
                        },
                        {
                            name: '启动次数', // 浮动框的标题
                            type: 'map',
                            geoIndex: 0,
                            data: geoValue,
                            itemStyle: {
                                normal: {
                                    //  areaColor: COLORS[2]
                                    //   color: '#fff'
                                }
                            }
                        }
                        ]
                    }
               
        ]
    };

    var geoCoordMap = { //这里放你打点的坐标信息，虚拟信息
        '莱芜市': [117.678856, 36.223361],
        '威海市': [122.121804, 37.524054],
        '滨州市': [117.982412, 37.392056],
        '临沂市': [118.364156, 35.119965],
        '淄博市': [118.069799, 36.82475],
        '日照市': [119.532384, 35.425496],
        '烟台市': [121.454902, 37.480081],
        '菏泽市': [115.48038, 35.248354],
        '青岛市': [120.397057, 36.07041],
        '东营市': [118.681509, 37.447084],
        '潍坊市': [119.164438, 36.71744],
        '济南市': [117.122338, 36.661876],
        '聊城市': [115.986305, 36.465225],
        '泰安市': [117.090143, 36.212179],
        '枣庄市': [117.329308, 34.824652],
        '济宁市': [116.588817, 35.433025]
    };
    var locValue = [
        { name: "莱芜市", value: "100" },
        { name: "威海市", value: "50" },
        { name: "滨州市", value: "20" },
        { name: "临沂市", value: "90" },
        { name: "淄博市", value: "170" },
        { name: "日照市", value: "190" },
        { name: "德州市", value: "160" },
        { name: "烟台市", value: "140" },
        { name: "菏泽市", value: "130" },
        { name: "青岛市", value: "110" },
        { name: "东营市", value: "105" },
        { name: "潍坊市", value: "142" },
        { name: "济南市", value: "80" },
        { name: "聊城市", value: "184" },
        { name: "泰安市", value: "155" },
        { name: "枣庄市", value: "130" },
        { name: "济宁市", value: "140" }
    ];
    var convertData = function (geoCoordMap, data) {
        var res = [];
        for (var i = 0; i < data.length; i++) {
            var geoCoord = geoCoordMap[data[i].name];
            if (geoCoord) {
                res.push({
                    name: data[i].name,
                    value: geoCoord.concat(data[i].value)
                });
            }
        }
        return res;
    };

    var geoValue = [{
        "name": "北京",
        "value": 599
    }, {
        "name": "上海",
        "value": 142
    }, {
        "name": "黑龙江",
        "value": 44
    }, {
        "name": "湖北",
        "value": 810
    }, {
        "name": "四川",
        "value": 453
    }];
    //var convertResult = convertData(geoCoordMap, locValue).slice(0, 4);
    //var convertResult2 = convertData(geoCoordMap, locValue).slice(4, 8);

    export default {
        name: 'index',
        components: {
            ICountUp
        },

        data() {
            return {
                delay: 0,
                endVal: 120500,
                options: {
                    useEasing: true,
                    useGrouping: true,
                    separator: ',',
                    decimal: '.',
                    prefix: '',
                    suffix: ''
                },
                total: 100,
                datalist: [
                    { ranking: 1, area: "广东", nValue: 1758000, yValue: 214589 },
                    { ranking: 1, area: "汕头", nValue: 175180, yValue: 145389 },
                    { ranking: 1, area: "潮汕", nValue: 174580, yValue: 142589 }
                ]
            }
        },
        methods: {
            drawChart() {
                // 基于准备好的dom，初始化echarts实例
                let element = document.getElementById('myChart');
                if (element == null)
                    return
                let myChart = this.$echarts.init(element)
                let option = {
                    xAxis: {
                        type: 'category',
                        data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun']
                    },
                    yAxis: {
                        type: 'value'
                    },
                    series: [
                        {
                            data: [820, 932, 901, 934, 1290, 1330, 1320],
                            type: 'line'
                        }
                    ]
                }
                myChart.setOption(option)
            },
            drawMultiChart() {
                let element = document.getElementById('yqyMain');
                if (element == null)
                    return
            this.$echarts.registerMap('china', chinaJson)

                let chart = this.$echarts.init(element)
                //每次窗口大小改变的时候都会触发onresize事件，这个时候我们将echarts对象的尺寸赋值给窗口的大小这个属性，从而实现图表对象与窗口对象的尺寸一致的情况
                window.onresize = chart.resize;

                /*接受自定义Option.js中的函数返回的：option*/
                chart.setOption(multiChartOption);
                this.multiChart = chart;

            },
            changeData() {
                var pieChartSeries2 = {
                    name: '面积模式',
                    type: 'pie',
                    radius: [10, 50],
                    center: ['48%', '75%'],
                    data: pieData[0] //饼图数据赋值
                };
                multiChartOption.series.push(pieChartSeries2)
                this.multiChart.setOption(multiChartOption);
            },
            drawMapChart() {
                //初始化echarts实例
                var myChart = this.$echarts.init(document.getElementById('mapChart'));

                //使用制定的配置项和数据显示图表
                myChart.setOption(optionMap);

            },
            chinaConfigure() {
                this.$echarts.registerMap('china', chinaJson)
                let myChart = this.$echarts.init(this.$refs.myEchart);
                window.onresize = myChart.resize;
                myChart.setOption(
                    { // 进行相关配置
                        backgroundColor: 'transparent',
                        tooltip: {}, // 鼠标移到图里面的浮动提示框
                        dataRange: {
                            show: false,
                            min: 0,
                            max: 1000,
                            text: ['High', 'Low'],
                            realtime: true,
                            calculable: true,
                            //  color: ['orangered', 'yellow', 'lightskyblue']
                        },

                        geo: { // 这个是重点配置区
                            map: 'china', // 表示中国地图
                            roam: true,
                            label: {
                                normal: {
                                    show: true, // 是否显示对应地名
                                    textStyle: {
                                        color: 'rgba(0,0,0,0.4)'
                                    }
                                }
                            },
                            itemStyle: {
                                normal: {
                                    borderColor: 'rgba(0, 0, 0, 0.2)',
                                    //    areaColor: "#5698d9",
                                    color: "#5698d9"
                                },
                                emphasis: {
                                    //    areaColor: "#5698d9",
                                    shadowOffsetX: 0,
                                    shadowOffsetY: 0,
                                    shadowBlur: 20,
                                    borderWidth: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                },
                                itemStyle: {
                                    normal: {
                                        color: '#8fc8f2',
                                        shadowBlur: 10,
                                        shadowColor: '#333'
                                    }
                                }
                            }
                        },

                        visualMap: {
                            min: 0,
                            max: 200,
                            calculable: true,
                            inRange: {
                                color: ['#50a3ba', '#eac736', '#d94e5d']
                            },
                            textStyle: {
                                color: '#fff'
                            }
                        },
                      

                        series: [{
                            type: 'scatter',
                            coordinateSystem: 'geo' // 对应上方配置

                        },
                        {
                            name: 'pm2.5',
                            type: 'scatter',
                            coordinateSystem: 'bmap',
                            data: [{ "name": "上海", "value": [121.48, 31.22, 25] }],
                            symbolSize: function (val) {
                                return val[2] / 10;
                            },
                            label: {
                                normal: {
                                    formatter: '{b}',
                                    position: 'right',
                                    show: false
                                },
                                emphasis: {
                                    show: true
                                }
                            },
                            itemStyle: {
                                normal: {
                                    color: '#ddb926'
                                }
                            }
                        },
                        {
                            name: '启动次数', // 浮动框的标题
                            type: 'map',
                            geoIndex: 0,
                            data: geoValue,
                            itemStyle: {
                                normal: {
                                    //  areaColor: COLORS[2]
                                    //   color: '#fff'
                                }
                            }
                        }
                        ]
                    }
                )
            },
            drawMultiChart2(){
                var convertData2 = function (data) {
        var res = [];
        for (var i = 0; i < data.length; i++) {
            var geoCoord = geoCoordMap[data[i].name];
            if (geoCoord) {
                res.push({
                    name: data[i].name,
                    value: geoCoord.concat(data[i].value)
                });
            }
        }
        return res;
    };
                var geoCoordMap = { '河南省': [113.664496, 34.817821], '河南': [113.75783, 34.502434], '北京市': [116.403694, 39.915378], '天津市': [117.216837, 39.142415], '上海市': [121.479662, 31.234329], '河北省': [114.494585, 38.129532], '山西省': [112.569095, 37.908919], '辽宁省': [123.445046, 41.806913], '吉林省': [126.582519, 43.86473], '新疆': [87.559985, 43.879367], '西藏': [91.160816, 29.653148], '内蒙古': [111.771822, 40.93481], '四川省': [106.492302, 29.601285], '青海省': [101.73138, 36.627027], '广东省': [113.254301, 23.129454], '湖南省': [112.953187, 28.265007] };
    var data = [{ name: '河南省', value: 3 }, { name: '河南', value: 1 }, { name: '北京市', value: 1 }, { name: '天津市', value: 1 }, { name: '上海市', value: 2 }, { name: '河北省', value: 1 }, { name: '山西省', value: 1 }, { name: '辽宁省', value: 0 }, { name: '吉林省', value: 1 }, { name: '新疆', value: 0 }, { name: '西藏', value: 1 }, { name: '内蒙古', value: 0 }, { name: '四川省', value: 0 }, { name: '青海省', value: 0 }, { name: '广东省', value: 0 }, { name: '湖南省', value: 0 }];
                this.$echarts.registerMap('china', chinaJson)
                var myChart = this.$echarts.init(document.getElementById('mapContainer'));
  //每次窗口大小改变的时候都会触发onresize事件，这个时候我们将echarts对象的尺寸赋值给窗口的大小这个属性，从而实现图表对象与窗口对象的尺寸一致的情况
  window.onresize = myChart.resize;

              var option = {
                    backgroundColor: 'transparent',
                    title: {
                        top: 20,
                        left: 100,
                        text: '销量地域分布',
                        subtext: '',
                        x: 'left',
                        textStyle: {
                            color: '#ccc'
                        }
                    },
                    tooltip: {
                trigger: 'axis',
                axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                    type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                },
                formatter: function (params) {
                    if (typeof (params.value)[2] == "undefined") {
                        //return params.name + ' : ' + params.value;
                    } else {//只有数据不为空才显示
                        return params.name + ' : ' + params.value[2];
                    }
                }
            },
            visualMap: {
                show: true,
                min: 0,
                max: 500,
                left: 'left',
               top: '300',
                text: ['高', '低'], // 文本，默认为数值文本
                calculable: true,
                seriesIndex: [1],
                inRange: {
                },
                dimension: 0
            },
            //布局
            grid: [
                { x: '55%', y: '5%', width: '40%', height: '40%' },
                { x: '50%',  bottom:'5%',width: '40%', height: '25%' }
            ],
            xAxis: [{
                title: "asdf",
                gridIndex: 0,
                type: 'value',
                max: 10,//x轴刻度
                axisLabel: {
                    show: true,
                },
                splitLine: {
                    show: true
                }
            }, {
                gridIndex: 1,
                type: 'category',
                axisLabel: {
                    show: true,
                },
                splitLine: {
                    show: true
                },   
                 data: ['2018-07-30', '2018-07-31', '2018-08-01', '2018-08-02', '2018-08-03', '2018-08-04', '2018-08-05', '2018-08-06'] 
            }],
            yAxis: [{
                gridIndex: 0,
                type: 'category',
                axisLabel: {
                    show: true,
                    textStyle: {
                        color: '#ddd'
                    }
                }
            }, {
                gridIndex: 1,
                type: 'value',
                axisLabel: {
                    show: true,
                    textStyle: {
                        color: '#ddd'
                    }
                }
            }],
            //布局e
            geo: {
                show: true,
                map: 'china',
                zoom:1,  //地图绽放
                scaleLimit:2,
                label: {
                    normal: {
                        show: true,
                        textStyle: {
                                color: '#fff'
                                    }
                    },
                    emphasis: {
                        show: false,
                    }
                },
                roam: false,//是否开启鼠标缩放和平移漫游
                itemStyle: {
                    normal: {
                        areaColor: 'transparent',
                        borderColor: '#3fdaff',
                        borderWidth: 2,
                        shadowColor: 'rgba(63, 218, 255, 0.5)',
                        shadowBlur: 30
                    },
                    emphasis: {
                        areaColor: '#2B91B7',
                        color:'#fff'
                    }
                }
            },
            //调整显示级别
            layoutCenter: ['25%', '22%'],
            layoutSize: 400,

            series: [
           
                {
                    name: '地图台站',
                    type: 'effectScatter',
                    coordinateSystem: 'geo',
                    data: convertData2(data.sort(function (a, b) {
                        return b.value - a.value;
                    }).slice(0, 5)),
                    symbolSize: function (val) {
                        //return val[2] / 10;//地图闪动
                        return 20;
                    },
                    showEffectOn: 'render',
                    rippleEffect: {
                        brushType: 'stroke'
                    },
                    hoverAnimation: true,
                    label: {
                        normal: {
                            formatter: '{b}',
                            position: 'right',
                            show: true
                        }
                    },
                    itemStyle: {
                        normal: {
                            color: '#F4E925',
                            shadowBlur: 10,
                            shadowColor: '#05C3F9'
                        }
                    },
                    zlevel: 1
                }, {
                            name: '启动次数', // 浮动框的标题
                            type: 'map',
                           geoIndex: 0,
                            data: geoValue,
                            itemStyle: {
                                normal: {
                                     areaColor: COLORS[2],
                                      color: '#fff'
                                }
                            }
                        }, {
                    name: '政治面貌',
                    type: 'pie',
                    z: 2,
                    color: ['#c23531', '#2f4554', '#61a0a8', '#d48265', '#91c7ae', '#749f83', '#ca8622', '#bda29a', '#6e7074', '#546570', '#c4ccd3'],
                    selectedMode: 'single',
                    radius: [0, '15%'],        //radius确定饼图的大小
                    center: ['20%', '60%'],    //center来确定饼图中心位置
                    label: {
                        normal: {
                            position: 'inner'
                        }
                    },
                    labelLine: {
                        normal: {
                            show: false
                        }
                    },
                    //显示series中信息，提示框组件
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    data: [{ value: 2, name: '无党派人士' }, { value: 4, name: '党员', selected: true }, { value: 5, name: '团员' }, { value: 1, name: '其它' }]
                },
                {
                    name: '年龄占比',
                    type: 'pie',
                    z: 2,
                    // 全局调色盘。
                    color: ['#c23531', '#2f4554', '#61a0a8', '#d48265', '#91c7ae', '#749f83', '#ca8622', '#bda29a', '#6e7074', '#546570', '#c4ccd3'],
                    radius: ['20%', '30%'],
                    center: ['20%', '60%'],
                    label: { formatter: "{b}占{d}%" },
                    itemStyle: {  //itemStyle有正常显示：normal，有鼠标hover的高亮显示：emphasis
                        emphasis: {  //normal显示阴影,与shadow有关的都是阴影的设置
                            shadowBlur: 10,  //阴影大小
                            shadowOffsetX: 0,  //阴影水平方向上的偏移
                            shadowColor: 'rgba(0, 0, 0, 0.5)'  //阴影颜色
                        }
                    },
                    //显示series中信息，提示框组件
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} <br/>{b} : {c} ({d}%)"
                    },
                    data: [{ value: 2, name: '18-25' }, { value: 8, name: '26-30' }, { value: 2, name: '31-40' }, { value: 0, name: '41-55' }, { value: 12, name: '55以上' }]
                }
                , {
                    id: 'bar',
                    name: '台站排名',
                    type: 'bar',
                    xAxisIndex: 0,
                    yAxisIndex: 0,
                    label: {
                        normal: {
                            show: true,
                            position: ["100%", "40%"],
                            color: "#ffffff",
                            formatter: "{b} : {c}"
                        }
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: "{b} : {c}"
                    },
                    itemStyle: {
                        //每个柱子的颜色即为colorList数组里的每一项，如果柱子数目多于colorList的长度，则柱子颜色循环使用该数组
                        color: function (params) {
                            var colorList = ['#c23531', '#2f4554', '#61a0a8', '#d48265', '#91c7ae', '#749f83', '#ca8622', '#bda29a', '#6e7074', '#546570', '#c4ccd3'];
                            return colorList[params.dataIndex];
                        }
                    },
                    z: 2,
                    data: [{ value: 3, name: '河南省' }, { value: 1, name: '河南' }, { value: 1, name: '北京市' }, { value: 1, name: '天津市' }, { value: 2, name: '上海市' }, { value: 1, name: '河北省' }, { value: 1, name: '山西省' }, { value: 0, name: '辽宁省' }, { value: 1, name: '吉林省' }, { value: 0, name: '新疆' }, { value: 1, name: '西藏' }, { value: 0, name: '内蒙古' }, { value: 0, name: '四川省' }, { value: 0, name: '青海省' }, { value: 0, name: '广东省' }, { value: 0, name: '湖南省' }]
                },
                {
                    name: '请假',
                    type: 'bar',
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} : {c}"
                    },
                    data: [1, 3, 2, 4, 1, 4, 3]
                },
                {
                    name: '迟到',
                    type: 'bar',
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} : {c}"
                    },
                    data: [1, 2, 3, 4, 3, 2, 2]
                },
                {
                    name: '早退',
                    type: 'bar',
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} : {c}"
                    },
                    data: [1, 2, 3, 4, 3, 2, 4]
                },
                {
                    name: '调休',
                    type: 'bar',
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} : {c}"
                    },
                    data: [1, 3, 2, 4, 1, 4, 2]
                },
                {
                    name: '加班',
                    type: 'bar',
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} : {c}"
                    },
                    data: [1, 3, 2, 4, 1, 4, 2]
                },
                {
                    name: '旷工',
                    type: 'bar',
                    xAxisIndex: 1,
                    yAxisIndex: 1,
                    tooltip: {
                        trigger: 'item',
                        formatter: "{a} : {c}"
                    },
                    data: [1, 3, 2, 4, 1, 4, 2]
                }
            ]
        };
        myChart.setOption(option);
            },
            onReady: function (instance, CountUp) {
                return
                const that = this
                instance.update(that.endVal + 100)
            },
            getData() {
                axios.get('https://easy-mock.com/mock/5d721076b158cf18134a822b/dashboard/getSales').then(res => {
                    debugger
                })
            }
        },
        mounted() {
            this.drawMultiChart2();
         //   this.drawChart();
           // this.drawMultiChart();
            //    this.drawMapChart();
        //    this.chinaConfigure();
        }
    }
</script>
<style scoped>
    .header {
        margin: 4px;
    }

    .header .title {
        font-size: 24px;
        color: #fff;

    }

    .title-bottom {
        height: 30px;
        justify-content: center;
        display: flex;

    }

    .bottom-sidebar {
        width: 100%;
        height: 100%;
        opacity: 1;
        background-image: url("~@/assets/images/bottombar.gif");
        background-size: 100%;
        background-repeat: no-repeat;
        background-position: center center;
        width: 400px;
    }
.bottom-line{
    width: 100%;
        height: 30px;
    background-image: url("~@/assets/images/bottomline.png");
        background-size: 100%;
        background-repeat: no-repeat;
        background-position: center center;
}
    .box-content {
        display: block;
        padding: 10px 18px;
        margin-bottom: 20px;
        line-height: 1.42857143;
        -webkit-transition: border 0.2s ease-in-out;
        -o-transition: border 0.2s ease-in-out;
        transition: border 0.2s ease-in-out;
    }

    .box-content.shadow {
        box-shadow: 1px 2px 8px 0px #888;

    }

    .statistic-item {
        display: flex;
        margin-bottom: 10px;
        align-items: center;
        overflow: hidden;
    }

    .statistic-item .icon-content {
        width: 50px;
        min-width: 50px;
        height: 50px;
        display: flex;
        align-items: center;
        justify-content: center;
        background: #f2f2f2;
        border-radius: 4px;
    }

    .icon-content .iconfont {
        font-size: 24px;
    }

    .text-content {
        margin-left: 10px;
        overflow: hidden;
    }

    .statistic-item .text {
        font-size: 26px;
        color: #fff;
    }

    .statistic-item .sub-title {
        font-size: 14px;
        text-align: left;
        color:rgb(154, 168, 212);
    }

    .chart {
        height: 700px;
    }

    .echart {
        width: 500px;
        height: 500px;
    }



    .table-container {
        width: 400px;
        padding: 20px;
    }

    .table {
        width: 100%;
        background-color: transparent;
        color: #666;
        border-width: 1px;
        border-style: solid;
        border-color: #e6e6e6;
    }

    .table.darkblue {
        border-color: rgba(130, 157, 248, 0.705);
    }

    .table.darkblue td {
        border: none;
    }

    .table.darkblue tr .th {
        color: rgb(154, 168, 212);
    }

    .table.darkblue tr td {
        color: rgb(229, 233, 247);

    }

    .table tr {
        transition: all .3s;
        -webkit-transition: all .3s;
    }

    .table td.border_bottom_none {
        border-bottom: none;
    }

    .table td.td_2 {
        padding: 0;
    }

    .table td,
    .table th {
        position: relative;
        padding: 9px 15px;
        min-height: 20px;
        line-height: 20px;
        font-size: 14px;
        border-width: 1px;
        border-style: solid;
        border-color: #e6e6e6;
        padding: 5px 10px;
        border-top: none;
        border-left: none
    }

    .table td:last-child {
        border-right: none;
    }

    .table tr:last-child {
        border-bottom: none;
    }

    .table td.padding_none {
        padding: 0;
    }

    .width_half {
        width: 50%;
    }

    .td_2_table tr:last-child td:last-child {
        border-bottom: none;
    }

    .td_right {
        text-align: right;
    }
    .chart-container{
        height: 200px;
    }
</style>