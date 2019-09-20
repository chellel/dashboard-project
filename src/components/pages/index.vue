<template>
    <div class="page">
        <div class="header">
            <div class="title">
                2019年度销售总览
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
                            <div class="statistic-item col-md-6" v-for="item in indexData" v-bind:key="item">
                                <div class="icon-content">
                                    <span class="iconfont icon-shuju"></span>
                                </div>
                                <div class="text-content">
                                    <div class="text">
                                        <span class="counter">
                                            <ICountUp :delay="delay" :endVal="item.value" :options="options"
                                                @ready="onReady" />
                                        </span>
                                    </div>
                                    <div class="sub-title">{{item.name}}</div>
                                </div>

                            </div>

                        </div>

                    </div>
                </div>
                <div class="col-md-12">
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
                            <div class="caption">
                                <div class="main">
                                    <h3>155852000 <small class="number warning">20.11%</small></h3>
                                </div>
                                <div>
                                    <h6>销售额(万)(vs昨日销售额)</h6>
                                </div>
                            </div>
                            <div ref="categoryChart" style="height: 1000px" class="chart"></div>
                            <div ref="pieChart" class="chart"></div>
                            <div ref="lineChart" class="chart"></div>
                            <div ref="barChart" class="chart"></div>
                            <div style="height:1000px;" id="mapContainer"></div>

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
    import 'echarts/map/js/china.js'
    import chinaJson from 'echarts/map/json/china.json'
    // import sale from '../../../src/assets/js/sale.json'

    function getXYData(data, property) {
        var res = [];
        data.forEach(function (item) {
            res.push(item[property])
        })
        return res
    }

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


    var textColor = "9AA8D4";
    var COLORS = ["#070093", "#1c3fbf", "#1482e5", "#70b4eb", "#b4e0f3", "#ffffff"];
    var monthArray = ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月']

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
                indexData: [{ name: '当前月销售额', value: 2341560 }, { name: '对比上月增长', value: 1582 }, { name: '总订单数', value: 2320 }, { name: '客户数', value: 1560 }, { name: '总客户数', value: 1560 }],
                datalist: [
                    { ranking: 1, area: "广东", nValue: 1758000, yValue: 214589 },
                    { ranking: 1, area: "汕头", nValue: 175180, yValue: 145389 },
                    { ranking: 1, area: "潮汕", nValue: 174580, yValue: 142589 }
                ],
                //  sale: sale.data.result,
                category: [{
                    "categoryid": "1",
                    "category": "自行车"
                },
                {
                    "categoryid": "2",
                    "category": "服装"
                },
                {
                    "categoryid": "3",
                    "category": "配件"
                },
                {
                    "categoryid": "4",
                    "category": "辅助用品"
                }
                ],
                subcategory: [{
                    "productid": "743",
                    "product": "山地自行车",
                    "subcategory": "山地自行车",
                    "category": "自行车",
                    "categoryid": "1"
                },
                {
                    "productid": "745",
                    "product": "山地自行车车架",
                    "subcategory": "山地自行车车架",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "709",
                    "product": "公路自行车",
                    "subcategory": "公路自行车",
                    "category": "自行车",
                    "categoryid": "1"
                },
                {
                    "productid": "731",
                    "product": "帽子",
                    "subcategory": "帽子",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "759",
                    "product": "长袖运动衫",
                    "subcategory": "运动衫",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "758",
                    "product": "运动头盔",
                    "subcategory": "头盔",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "711",
                    "product": "公路自行车车架",
                    "subcategory": "公路自行车车架",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "752",
                    "product": "山地自行车袜子",
                    "subcategory": "袜子",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "750",
                    "product": "山地自行车前轮",
                    "subcategory": "车轮",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "744",
                    "product": "山地自行车车把",
                    "subcategory": "车把",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "748",
                    "product": "山地自行车后轮",
                    "subcategory": "车轮",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "734",
                    "product": "男士运动短裤",
                    "subcategory": "短裤",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "735",
                    "product": "女士紧身衣",
                    "subcategory": "紧身衣",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "733",
                    "product": "男士背带短裤",
                    "subcategory": "背带短裤",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "756",
                    "product": "夏用手套",
                    "subcategory": "手套",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "705",
                    "product": "冬用手套",
                    "subcategory": "手套",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "732",
                    "product": "迷你气筒",
                    "subcategory": "打气筒",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "713",
                    "product": "公路自行车后轮",
                    "subcategory": "车轮",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "715",
                    "product": "公路自行车前轮",
                    "subcategory": "车轮",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "710",
                    "product": "公路自行车车把",
                    "subcategory": "车把",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "708",
                    "product": "耳机",
                    "subcategory": "耳机",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "757",
                    "product": "线缆锁",
                    "subcategory": "车锁",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "740",
                    "product": "前叉",
                    "subcategory": "前叉",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "749",
                    "product": "山地自行车脚踏板",
                    "subcategory": "脚踏板",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "706",
                    "product": "短袖经典运动衫",
                    "subcategory": "运动衫",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "721",
                    "product": "经典背心",
                    "subcategory": "背心",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "738",
                    "product": "骑行包",
                    "subcategory": "骑行包",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "742",
                    "product": "清洗剂",
                    "subcategory": "清洗剂",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "702",
                    "product": "车顶固定架",
                    "subcategory": "货架",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "755",
                    "product": "水壶",
                    "subcategory": "水壶架",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "736",
                    "product": "女士山地短裤",
                    "subcategory": "短裤",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "746",
                    "product": "山地自行车车座",
                    "subcategory": "车座",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "724",
                    "product": "旅游自行车",
                    "subcategory": "旅游自行车",
                    "category": "自行车",
                    "categoryid": "1"
                },
                {
                    "productid": "741",
                    "product": "前刹车",
                    "subcategory": "刹车",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "739",
                    "product": "前变速器",
                    "subcategory": "变速器",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "723",
                    "product": "链条",
                    "subcategory": "链条",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "727",
                    "product": "旅游自行车车座",
                    "subcategory": "车座",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "719",
                    "product": "后变速器",
                    "subcategory": "变速器",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "726",
                    "product": "旅游自行车车架",
                    "subcategory": "旅游自行车车架",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "703",
                    "product": "大齿盘",
                    "subcategory": "大齿盘",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "720",
                    "product": "后刹车",
                    "subcategory": "刹车",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "725",
                    "product": "旅游自行车车把",
                    "subcategory": "车把",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "704",
                    "product": "底托架",
                    "subcategory": "中轴",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "714",
                    "product": "公路自行车脚踏板",
                    "subcategory": "脚踏板",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "722",
                    "product": "竞速袜",
                    "subcategory": "袜子",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "737",
                    "product": "女士山地自行车短裤",
                    "subcategory": "短裤",
                    "category": "服装",
                    "categoryid": "2"
                },
                {
                    "productid": "701",
                    "product": "补胎套件",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "712",
                    "product": "公路自行车车座",
                    "subcategory": "车座",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "728",
                    "product": "旅游自行车脚踏板",
                    "subcategory": "脚踏板",
                    "category": "配件",
                    "categoryid": "3"
                },
                {
                    "productid": "716",
                    "product": "公路自行车水壶架",
                    "subcategory": "水壶架",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "751",
                    "product": "山地自行车水壶架",
                    "subcategory": "水壶架",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "717",
                    "product": "公路自行车外胎",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "718",
                    "product": "公路自行车外胎内胎",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "707",
                    "product": "多用途支架",
                    "subcategory": "支架",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "753",
                    "product": "山地自行车外胎",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "754",
                    "product": "山地自行车外胎内胎",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "729",
                    "product": "旅游自行车外胎",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "730",
                    "product": "旅游自行车外胎内胎",
                    "subcategory": "内外胎",
                    "category": "辅助用品",
                    "categoryid": "4"
                },
                {
                    "productid": "747",
                    "product": "山地自行车挡泥板",
                    "subcategory": "挡泥板",
                    "category": "辅助用品",
                    "categoryid": "4"
                },

                ],
                subCategorySaleData: [{ "province": "广东", "value": 199425 }, { "province": "福建", "value": 164133 }, { "province": "江苏", "value": 160782 }, { "province": "湖南", "value": 280300 }, { "province": "北京", "value": 253081 }, { "province": "四川", "value": 232221 }, { "province": "甘肃", "value": 334846 }, { "province": "新疆", "value": 150796 }, { "province": "吉林", "value": 139778 }, { "province": "海南", "value": 152369 }, { "province": "辽宁", "value": 299327 }, { "province": "天津", "value": 201057 }, { "province": "西藏", "value": 131962 }, { "province": "河北", "value": 199834 }, { "province": "湖北", "value": 152413 }, { "province": "广西", "value": 100364 }, { "province": "青海", "value": 140823 }, { "province": "上海", "value": 191918 }, { "province": "贵州", "value": 94650 }, { "province": "陕西", "value": 104321 }, { "province": "宁夏", "value": 125371 }, { "province": "安徽", "value": 464109 }, { "province": "重庆", "value": 111396 }, { "province": "云南", "value": 129407 }, { "province": "内蒙古", "value": 160949 }, { "province": "江西", "value": 113205 }, { "province": "山西", "value": 52035 }, { "province": "浙江", "value": 128498 }, { "province": "山东", "value": 216002 }, { "province": "黑龙江", "value": 79891 }, { "province": "河南", "value": 54682 }],
                categorySale: [{
                    "category": "自行车",
                    "value": 5095558
                }, {
                    "category": "配件",
                    "value": 205763
                }, {
                    "category": "服装",
                    "value": 12896
                }, {
                    "category": "辅助用品",
                    "value": 5728
                }],
                subCategorySale: [
                    { name: '冬用手套', value: 92345.33 },
                    { name: '耳机', value: 41921.75 },
                    { name: '公路自行车', value: 17232466.67 },
                    { name: '公路自行车车把', value: 19162.74 },
                    { name: '公路自行车车架', value: 1692677.66 },
                    { name: '公路自行车后轮', value: 70267.58 },
                    { name: '公路自行车前轮', value: 136291.26 },
                    { name: '帽子', value: 10478.11 },
                    { name: '迷你气筒', value: 9486.85 },
                    { name: '男士背带短裤', value: 117193.08 },
                    { name: '男士运动短裤', value: 56870.52 },
                    { name: '女士紧身衣', value: 141553.37 },
                    { name: '前叉', value: 55493.04 },
                    { name: '山地自行车', value: 11933122.51 },
                    { name: '山地自行车车把', value: 38364.18 },
                    { name: '山地自行车车架', value: 1541453.95 },
                    { name: '山地自行车后轮', value: 212537.95 },
                    { name: '山地自行车前轮', value: 73549.67 },
                    { name: '山地自行车袜子', value: 2916.98 },
                    { name: '夏用手套', value: 16866.61 },
                    { name: '线缆锁', value: 11560 },
                    { name: '运动头盔', value: 82454.45 },
                    { name: '长袖运动衫', value: 121862.68 },
                    //     { name: '总计', value: 33710896.94 }
                ],
                monthSale: [
                    { name: '1月', value: 3970636.431 },
                    { name: '2月', value: 1475538.359 },
                    { name: '3月', value: 2976353.267 },
                    { name: '4月', value: 1770047.398 },
                    { name: '5月', value: 3092481.872 },
                    { name: '6月', value: 4114089.254 },
                    { name: '7月', value: 3427252.401 },
                    { name: '8月', value: 2177666.754 },
                    { name: '9月', value: 3455342.385 },
                    { name: '10月', value: 2547092.007 },
                    { name: '11月', value: 1873757.517 },
                    { name: '12月', value: 2830639.294 },
                    //  { name: '总计', value: 33710896.94 }
                ],
                monthCategoryData:{"自行车":[3804873,1406479,2789916,1617347,2498571,3191726,5888426,2988027,2896038,2095279,1727802,2600005],"配件":[151345,63623,171325,141297,508413,799577,1108360,558939,474022,376664,111145,183231],"服装":[10057,3658,9528,7123,74176,104206,168064,125808,72679,65051,42341,54097],"辅助用品":[4360,1776,5582,4279,11319,18578,17506,24614,12602,10096,4726,5563]}
            }
              //  monthCategoryData: [{"name":"1月","items":[{"category":"1","name":"自行车","value":3804873.102},{"category":"3","name":"配件","value":151345.70369999998},{"category":"2","name":"服装","value":10057.342},{"category":"4","name":"辅助用品","value":4360.284}]},{"name":"2月","items":[{"category":"1","name":"自行车","value":1406479.6561},{"category":"3","name":"配件","value":63623.648},{"category":"2","name":"服装","value":3658.6428},{"category":"4","name":"辅助用品","value":1776.412}]},{"name":"3月","items":[{"category":"1","name":"自行车","value":2789916.613},{"category":"3","name":"配件","value":171325.62079999998},{"category":"2","name":"服装","value":9528.1197},{"category":"4","name":"辅助用品","value":5582.9128}]},{"name":"4月","items":[{"category":"1","name":"自行车","value":1617347.2740000002},{"category":"3","name":"配件","value":141297.3023},{"category":"2","name":"服装","value":7123.2834},{"category":"4","name":"辅助用品","value":4279.538}]},{"name":"5月","items":[{"category":"2","name":"服装","value":74176.75589999999},{"category":"3","name":"配件","value":508413.7611999999},{"category":"1","name":"自行车","value":2498571.606},{"category":"4","name":"辅助用品","value":11319.7482}]},{"name":"6月","items":[{"category":"2","name":"服装","value":104206.8286},{"category":"3","name":"配件","value":799577.1469},{"category":"1","name":"自行车","value":3191726.3880000003},{"category":"4","name":"辅助用品","value":18578.891300000003}]},{"name":"7月","items":[{"category":"2","name":"服装","value":168064.72689999998},{"category":"3","name":"配件","value":1108360.5566000002},{"category":"1","name":"自行车","value":5888426.419000001},{"category":"4","name":"辅助用品","value":17506.2486}]},{"name":"8月","items":[{"category":"2","name":"服装","value":125808.0117},{"category":"3","name":"配件","value":558939.5143},{"category":"1","name":"自行车","value":2988027.6212},{"category":"4","name":"辅助用品","value":24614.8185}]},{"name":"9月","items":[{"category":"2","name":"服装","value":72679.6351},{"category":"3","name":"配件","value":474022.54800000007},{"category":"1","name":"自行车","value":2896038.057},{"category":"4","name":"辅助用品","value":12602.145100000002}]},{"name":"10月","items":[{"category":"2","name":"服装","value":65051.4763},{"category":"3","name":"配件","value":376664.3716},{"category":"1","name":"自行车","value":2095279.4780000001},{"category":"4","name":"辅助用品","value":10096.681}]},{"name":"11月","items":[{"category":"2","name":"服装","value":42341.2166},{"category":"3","name":"配件","value":111145.17439999999},{"category":"1","name":"自行车","value":1727802.9057},{"category":"4","name":"辅助用品","value":4726.8275}]},{"name":"12月","items":[{"category":"2","name":"服装","value":54097.6215},{"category":"3","name":"配件","value":183231.4208},{"category":"1","name":"自行车","value":2600005.8090000004},{"category":"4","name":"辅助用品","value":5563.049999999999}]}] }

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
            drawMultiChart2() {
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
                        { x: '50%', bottom: '5%', width: '40%', height: '25%' }
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
                        zoom: 1,  //地图绽放
                        scaleLimit: 2,
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
                                color: '#fff'
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
            getCategoryData() {
                axios.get('https://easy-mock.com/mock/5d721076b158cf18134a822b/dashboard/category').then(res => {

                })
            },
            drawPieChart() {

                var categorySale = this.categorySale;
                var option = {
                    title: {
                        text: '销售额（万）（按一级分类）',
                        left: 'center',
                        textStyle: {
                            color: '#9AA8D4'
                        }
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: function (params) {
                            return `${params.data.category} 
                            <br/>${params.data.value}元(${params.percent}%)`;
                        }
                    },
                    legend: {
                        // orient: 'vertical',
                        // top: 'middle',
                        bottom: 10,
                        left: 'center',
                        data: (function getXYData(data, property) {
                            var data = categorySale;
                            var property = "category";
                            var res = [];
                            data.forEach(function (item) {
                                res.push(item[property])
                            })
                            return res
                        })()
                    },
                    series: [
                        {
                            type: 'pie',
                            radius: '50%',
                            center: ['50%', '50%'],
                            selectedMode: 'single',
                            data: categorySale,
                            label: {
                                show: true,
                                formatter: '{b}'
                            },
                            itemStyle: {
                                emphasis: {
                                    shadowBlur: 10,
                                    shadowOffsetX: 0,
                                    shadowColor: 'rgba(0, 0, 0, 0.5)'
                                }
                            }
                        }
                    ]
                };
                let myChart = this.$echarts.init(this.$refs.pieChart);
                window.onresize = myChart.resize;
                myChart.setOption(option)
            },
            drawLineChart() {

                var monthSale = this.monthSale;
                var option = {
                    title: {
                        text: '月销售额（万）',
                        left: 'center',
                        textStyle: {
                            color: '#9AA8D4'
                        }
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: "{b}月份 {a}  <br/> {c}元"
                    },
                    xAxis: {
                        type: 'category',
                        data: monthArray
                    },
                    yAxis: {
                        type: 'value'
                    },
                    series: [{
                        name: "销售额",
                        data: (function () {
                            var res = [];
                            monthSale.forEach(function (item) {
                                res.push(item.value)
                            })
                            return res

                        })(),
                        type: 'line'
                    }]
                };
                let myChart = this.$echarts.init(this.$refs.lineChart);
                window.onresize = myChart.resize;
                myChart.setOption(option)
            },
            drawBarChart() {
                var monthSale =this.monthSale;
                var option = {
                    color: ['#3398DB'],
                    title: {
                        text: '月销售额',
                    },
                    tooltip: {
                        trigger: 'axis'
                    },
                    legend: {
                        data: ['月份']
                    },
                    toolbox: {
                        show: true,
                        feature: {
                            magicType: { show: true, type: ['line', 'bar'] },
                            saveAsImage: { show: true }
                        }
                    },
                    calculable: true,
                    xAxis: [
                        {
                            type: 'category',
                            data: monthArray
                        }
                    ],
                    yAxis: [
                        {
                            type: 'value'
                        }
                    ],
                    series: [
                        {
                            name: '月销售额',
                            type: 'bar',
                            data: (function getXYData() {
                                var data = monthSale;
                                var property = "value";
                                var res = [];
                                data.forEach(function (item) {
                                    res.push(item[property])
                                })
                                return res
                            })(),
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
                        }
                    ]
                };
                let myChart = this.$echarts.init(this.$refs.barChart);
                window.onresize = myChart.resize;
                myChart.setOption(option)
            },
            drawCategorybyMonth() {
                var category = this.category;
                var monthCategoryData=this.monthCategoryData;
                var option = {
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                            type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                        }
                    },
                    legend: {
                        data: (function getXYData(data, property) {
                            var data = category; var property = "category";
                            var res = [];
                            data.forEach(function (item) {
                                res.push(item[property])
                            })

                            return res
                        })(),
                        textStyle: {
                            color: textColor
                        }
                    },
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: {
                        type: 'value',
                        nameTextStyle: {
                            color: "#fff"
                        }
                    },
                    yAxis: {
                        type: 'category',
                        data: monthArray,
                        nameTextStyle: {
                            color: "#3398DB"
                        }
                    },
                    series: [
                        {
                            name: '自行车',
                            type: 'bar',
                            stack: '总量',
                            label: {
                                normal: {
                                    show: true,
                                    position: 'insideRight'
                                }
                            },
                            data: monthCategoryData["自行车"]
                        },
                        {
                            name: '服装',
                            type: 'bar',
                            stack: '总量',
                            label: {
                                normal: {
                                    show: true,
                                    position: 'insideRight'
                                }
                            },
                            data: monthCategoryData["服装"]
                        },
                        {
                            name: '配件',
                            type: 'bar',
                            stack: '总量',
                            label: {
                                normal: {
                                    show: true,
                                    position: 'insideRight'
                                }
                            },
                            data: monthCategoryData["配件"]
                        },
                        {
                            name: '辅助用品',
                            type: 'bar',
                            stack: '总量',
                            label: {
                                normal: {
                                    show: true,
                                    position: 'insideRight'
                                }
                            },
                            data: monthCategoryData["辅助用品"]
                        }

                    ]
                };
                let myChart = this.$echarts.init(this.$refs.categoryChart);
                window.onresize = myChart.resize;
                myChart.setOption(option)
            },
            getClassifyData(groupby) {
                var sales = this.formatData(this.sale);

                var result = []; var map = {};

                sales.forEach(item => {
                    //var groupby = "category";
                    if (!map[item[groupby]]) {
                        result.push({
                            [groupby]: item[groupby],
                            value: item.value
                            // items: [item]
                        });
                        map[item[groupby]] = item;
                    } else {
                        if (result.length > 0) {
                            var destItem = result.find(m => m[groupby] == item[groupby]);
                            destItem.value += item.value;
                            //  destItem.items.push(item)
                        }
                    }
                })
                return result;
                /* var result = []; var map = {};
 
 sales.forEach(item => {
     var groupby = "month";
     var itemitem = [];
     var mapitem = [];
     if (!map[item[groupby]]) {
         var category = item.category;
 
         mapitem.push({ category: category, amount: item.amount })
 
         result.push({
             [groupby]: item[groupby],
             items: mapitem,
             amount: item.amount,
            
         });
 
         map[item[groupby]] = {};
         map[item[groupby]]["item"] = item;
         map[item[groupby]]["itemitem"] = mapitem;
     } else {
         if (result.length > 0) {
             var destItem = result.find(m => m[groupby] == item[groupby]);
             destItem.amount += item.amount;
             var category = item.category;
             var mapitem = destItem.items.find(m => m.category == category);
             if (!mapitem) {
                 destItem.items.push({ category: category, amount: item.amount })
             } else {
                 mapitem.amount += item.amount;
             }
             return
         }
     }
 })
    
                 return result;*/
            },
            formatData() {

                var sales = this.sale;
                sales.map((i) => {
                    i.quantity = parseInt(i.quantity);
                    var d = i.unitprice.replace(",", "");
                    i.unitprice = parseInt(i.unitprice.replace(",", ""))
                    i.amount = parseInt(i.amount.replace(",", ""))
                    i.month = i.orderdate.split("/")[1];
                    var subcategory = this.subcategory;
                    var categoryTemp = subcategory.find(m => m.productid == i.productid);
                    if (categoryTemp != undefined) {
                        i.categoryid = categoryTemp.categoryid;
                        i.category = categoryTemp.category;
                        i.subcategory = categoryTemp.subcategory;
                    }
                    return i;
                })
                return sales;
            },
            formatMonthCategoryData(){
                var monthCategoryData = this.monthCategoryData;
            var subcategory = this.subcategory;
            var result = [];
            monthCategoryData.forEach(item => {
                result.push({
                            name: item.name,
                            items: []
                        })
            var map = {};
                item.items.forEach(itemitem => {
                    var category = subcategory.find(m => m.productid == itemitem.productid)
                    var categoryid = category.categoryid;
                    if (!map[categoryid]) {
                        map[categoryid] = itemitem;
                        var dept = result.find(m => m.name == item.name);
                  
                        dept.items.push({
                                category: categoryid,
                                name:category.category,
                                value: itemitem.value
                            })
                    } else {
                        var dept = result.find(m => m.name == item.name);
                        var deptdept = dept.items.find(m => m.category == categoryid);
                        if (!deptdept)
                            dept.items.push({
                                category: categoryid,
                                name:category.category,
                                value: itemitem.value
                            })
                        else
                            deptdept.value += itemitem.value;

                    }
                })
            })
            console.log(JSON.stringify(result))

            },
            getbarData(){
                var monthCategoryData=this.monthCategoryData;
                var result={};
                monthCategoryData.forEach(function(item,index){
                    {
                    item.items.forEach(itemitem=>{
                        if(!result[itemitem.name])
                        result[itemitem.name]=[];
                        result[itemitem.name][index]=parseInt(itemitem.value.toFixed(2));
                    })
                }
                })
                console.log(JSON.stringify(result))
            }


        },
        mounted() {
            // this.getCategoryData();
            this.drawCategorybyMonth();
            this.drawPieChart();
            this.drawBarChart();
            this.drawLineChart();
   // this.formatMonthCategoryData();
//this.getbarData();
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

    .bottom-line {
        width: 100%;
        height: 30px;
        background-image: url("~@/assets/images/bottomline.png");
        background-size: 100%;
        background-repeat: no-repeat;
        background-position: center center;
    }

    .box-content {
        display: block;
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
        background: #3b4e82;
        border-radius: 50%;
    }

    .icon-content .iconfont {
        font-size: 24px;
        color: #fff;
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
        color: rgb(154, 168, 212);
    }

    .chart {
        height: 300px;
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

    .chart-container {
        height: 200px;
    }


    .box-content .caption {
        text-align: left;
        color: #fff;
    }

    small.number {
        color: #0b8603;
        font-weight: bold;
    }

    .warning {
        color: red;
    }
</style>