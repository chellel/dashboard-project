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
                                <div class="statistic-item col-md-6" v-for="item in indexData" v-bind:key="item.name">
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
                    <div ref="multiPieChart" style="height: 500px"  class="chart"></div>

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
                            <tr v-for="(item,index) in datalist" :key="item.id">
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
                                <div ref="pieChart" class="chart"></div>

                    </div>
    
                </div>
    
                <div class="col-md-8">
                    <div class="row">
                        <div class="col-sm-12 col-md-12">
    
                            <div class="box-content">
                                <div class="caption">
                                    <div class="main">
                                        <h2>
                                                <ICountUp :endVal="totalSales" />
                                                 <small class="number warning">20.11%</small></h2>
                                    </div>
                                    <div>
                                        <h6>年销售额(万)(vs去年销售额)</h6>
                                    </div>
                                </div>
            <div style="height:400px;" id="mapContainer"></div>
                                <div ref="categoryChart" style="height: 400px" class="chart"></div>
                                
                                <div ref="barChart" class="chart"></div>
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
        var Order = "orderID";
    //对json进行升序排序函数  
    var asc = function(x, y) {
      return (Number(x[Order]) > Number(y[Order])) ? 1 : -1
    }
    //对json进行降序排序函数  
    var desc = function(x, y) {
      return (Number(x[Order]) < Number(y[Order])) ? 1 : -1
    }
    /** 将数值转换为万单位的数值 */
    function formatLargeNumber(num){
        num=num/10000;
        num=num.toFixed(2);
        return num;
    }
        function getXYData(data, property) {
            var res = [];
            data.forEach(function (item) {
                res.push(item[property])
            })
            return res
        }
    
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
                  totalSales:33710896.94,
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
                    monthCategoryData:{"自行车":[3804873,1406479,2789916,1617347,2498571,3191726,5888426,2988027,2896038,2095279,1727802,2600005],"配件":[151345,63623,171325,141297,508413,799577,1108360,558939,474022,376664,111145,183231],"服装":[10057,3658,9528,7123,74176,104206,168064,125808,72679,65051,42341,54097],"辅助用品":[4360,1776,5582,4279,11319,18578,17506,24614,12602,10096,4726,5563]},
               categoryClassifyData:[{"categoryid":"1","categoryName":"自行车","data":[{"name":"公路自行车","value":17232466.67},{"name":"山地自行车","value":11933122.51}]},{"categoryid":"2","categoryName":"服装","data":[{"name":"冬用手套","value":92345.33},{"name":"帽子","value":10478.11},{"name":"男士背带短裤","value":117193.08},{"name":"男士运动短裤","value":56870.52},{"name":"女士紧身衣","value":141553.37},{"name":"山地自行车袜子","value":2916.98},{"name":"夏用手套","value":16866.61},{"name":"长袖运动衫","value":121862.68}]},{"categoryid":"3","categoryName":"配件","data":[{"name":"耳机","value":41921.75},{"name":"公路自行车车把","value":19162.74},{"name":"公路自行车车架","value":1692677.66},{"name":"公路自行车后轮","value":70267.58},{"name":"公路自行车前轮","value":136291.26},{"name":"前叉","value":55493.04},{"name":"山地自行车车把","value":38364.18},{"name":"山地自行车车架","value":1541453.95},{"name":"山地自行车后轮","value":212537.95},{"name":"山地自行车前轮","value":73549.67}]},{"categoryid":"4","categoryName":"辅助用品","data":[{"name":"迷你气筒","value":9486.85},{"name":"线缆锁","value":11560},{"name":"运动头盔","value":82454.45}]}],
               provinceSale:[{"name":"安徽","value":1281924.04},{"name":"北京","value":1740834.61},{"name":"福建","value":1527851.97},{"name":"甘肃","value":1263797.84},{"name":"广东","value":1897525.56},{"name":"广西","value":942419.38},{"name":"贵州","value":903811.1},{"name":"海南","value":891996.77},{"name":"河北","value":956542.16},{"name":"河南","value":806524.49},{"name":"黑龙江","value":766534.02},{"name":"湖北","value":778046.06},{"name":"湖南","value":1437962.33},{"name":"吉林","value":1103217.26},{"name":"江苏","value":1331694.44},{"name":"江西","value":910583.23},{"name":"辽宁","value":1417750.03},{"name":"内蒙古","value":1139955.46},{"name":"宁夏","value":797518.77},{"name":"青海","value":632022.57},{"name":"山东","value":1231800.22},{"name":"山西","value":536195.23},{"name":"陕西","value":592725.6},{"name":"上海","value":1813714.79},{"name":"四川","value":1014240.54},{"name":"天津","value":1278654.56},{"name":"西藏","value":692687.19},{"name":"新疆","value":1084279.21},{"name":"云南","value":887189.6},{"name":"浙江","value":1263946.23},{"name":"重庆","value":786951.66}]
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
                    this.$echarts.registerMap('china', chinaJson)
                    var myChart = this.$echarts.init(document.getElementById('mapChart'));
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
                  var that=this;
                  var categorySale = this.categorySale;
                  
                  var provinceSale=JSON.parse(JSON.stringify(this.provinceSale));
                    var data = [
         {name: '海门', value: 9},
         {name: '鄂尔多斯', value: 12},
         {name: '招远', value: 12},
         {name: '舟山', value: 12},
         {name: '齐齐哈尔', value: 14},
         {name: '盐城', value: 15},
         {name: '赤峰', value: 16},
         {name: '青岛', value: 18},
         {name: '乳山', value: 18},
         {name: '金昌', value: 19},
         {name: '泉州', value: 21},
         {name: '莱西', value: 21},
         {name: '日照', value: 21},
         {name: '胶南', value: 22},
         {name: '南通', value: 23},
         {name: '拉萨', value: 24},
         {name: '云浮', value: 24},
         {name: '梅州', value: 25},
         {name: '文登', value: 25},
         {name: '上海', value: 25},
         {name: '攀枝花', value: 25},
         {name: '威海', value: 25},
         {name: '承德', value: 25},
         {name: '厦门', value: 26},
         {name: '汕尾', value: 26},
         {name: '潮州', value: 26},
         {name: '丹东', value: 27},
         {name: '太仓', value: 27},
         {name: '曲靖', value: 27},
         {name: '烟台', value: 28},
         {name: '福州', value: 29},
         {name: '瓦房店', value: 30},
         {name: '即墨', value: 30},
         {name: '抚顺', value: 31},
         {name: '玉溪', value: 31},
         {name: '张家口', value: 31},
         {name: '阳泉', value: 31},
         {name: '莱州', value: 32},
         {name: '湖州', value: 32},
         {name: '汕头', value: 32},
         {name: '昆山', value: 33},
         {name: '宁波', value: 33},
         {name: '湛江', value: 33},
         {name: '揭阳', value: 34},
         {name: '荣成', value: 34},
         {name: '连云港', value: 35},
         {name: '葫芦岛', value: 35},
         {name: '常熟', value: 36},
         {name: '东莞', value: 36},
         {name: '河源', value: 36},
         {name: '淮安', value: 36},
         {name: '泰州', value: 36},
         {name: '南宁', value: 37},
         {name: '营口', value: 37},
         {name: '惠州', value: 37},
         {name: '江阴', value: 37},
         {name: '蓬莱', value: 37},
         {name: '韶关', value: 38},
         {name: '嘉峪关', value: 38},
         {name: '广州', value: 38},
         {name: '延安', value: 38},
         {name: '太原', value: 39},
         {name: '清远', value: 39},
         {name: '中山', value: 39},
         {name: '昆明', value: 39},
         {name: '寿光', value: 40},
         {name: '盘锦', value: 40},
         {name: '长治', value: 41},
         {name: '深圳', value: 41},
         {name: '珠海', value: 42},
         {name: '宿迁', value: 43},
         {name: '咸阳', value: 43},
         {name: '铜川', value: 44},
         {name: '平度', value: 44},
         {name: '佛山', value: 44},
         {name: '海口', value: 44},
         {name: '江门', value: 45},
         {name: '章丘', value: 45},
         {name: '肇庆', value: 46},
         {name: '大连', value: 47},
         {name: '临汾', value: 47},
         {name: '吴江', value: 47},
         {name: '石嘴山', value: 49},
         {name: '沈阳', value: 50},
         {name: '苏州', value: 50},
         {name: '茂名', value: 50},
         {name: '嘉兴', value: 51},
         {name: '长春', value: 51},
         {name: '胶州', value: 52},
         {name: '银川', value: 52},
         {name: '张家港', value: 52},
         {name: '三门峡', value: 53},
         {name: '锦州', value: 54},
         {name: '南昌', value: 54},
         {name: '柳州', value: 54},
         {name: '三亚', value: 54},
         {name: '自贡', value: 56},
         {name: '吉林', value: 56},
         {name: '阳江', value: 57},
         {name: '泸州', value: 57},
         {name: '西宁', value: 57},
         {name: '宜宾', value: 58},
         {name: '呼和浩特', value: 58},
         {name: '成都', value: 58},
         {name: '大同', value: 58},
         {name: '镇江', value: 59},
         {name: '桂林', value: 59},
         {name: '张家界', value: 59},
         {name: '宜兴', value: 59},
         {name: '北海', value: 60},
         {name: '西安', value: 61},
         {name: '金坛', value: 62},
         {name: '东营', value: 62},
         {name: '牡丹江', value: 63},
         {name: '遵义', value: 63},
         {name: '绍兴', value: 63},
         {name: '扬州', value: 64},
         {name: '常州', value: 64},
         {name: '潍坊', value: 65},
         {name: '重庆', value: 66},
         {name: '台州', value: 67},
         {name: '南京', value: 67},
         {name: '滨州', value: 70},
         {name: '贵阳', value: 71},
         {name: '无锡', value: 71},
         {name: '本溪', value: 71},
         {name: '克拉玛依', value: 72},
         {name: '渭南', value: 72},
         {name: '马鞍山', value: 72},
         {name: '宝鸡', value: 72},
         {name: '焦作', value: 75},
         {name: '句容', value: 75},
         {name: '北京', value: 79},
         {name: '徐州', value: 79},
         {name: '衡水', value: 80},
         {name: '包头', value: 80},
         {name: '绵阳', value: 80},
         {name: '乌鲁木齐', value: 84},
         {name: '枣庄', value: 84},
         {name: '杭州', value: 84},
         {name: '淄博', value: 85},
         {name: '鞍山', value: 86},
         {name: '溧阳', value: 86},
         {name: '库尔勒', value: 86},
         {name: '安阳', value: 90},
         {name: '开封', value: 90},
         {name: '济南', value: 92},
         {name: '德阳', value: 93},
         {name: '温州', value: 95},
         {name: '九江', value: 96},
         {name: '邯郸', value: 98},
         {name: '临安', value: 99},
         {name: '兰州', value: 99},
         {name: '沧州', value: 100},
         {name: '临沂', value: 103},
         {name: '南充', value: 104},
         {name: '天津', value: 105},
         {name: '富阳', value: 106},
         {name: '泰安', value: 112},
         {name: '诸暨', value: 112},
         {name: '郑州', value: 113},
         {name: '哈尔滨', value: 114},
         {name: '聊城', value: 116},
         {name: '芜湖', value: 117},
         {name: '唐山', value: 119},
         {name: '平顶山', value: 119},
         {name: '邢台', value: 119},
         {name: '德州', value: 120},
         {name: '济宁', value: 120},
         {name: '荆州', value: 127},
         {name: '宜昌', value: 130},
         {name: '义乌', value: 132},
         {name: '丽水', value: 133},
         {name: '洛阳', value: 134},
         {name: '秦皇岛', value: 136},
         {name: '株洲', value: 143},
         {name: '石家庄', value: 147},
         {name: '莱芜', value: 148},
         {name: '常德', value: 152},
         {name: '保定', value: 153},
         {name: '湘潭', value: 154},
         {name: '金华', value: 157},
         {name: '岳阳', value: 169},
         {name: '长沙', value: 175},
         {name: '衢州', value: 177},
         {name: '廊坊', value: 193},
         {name: '菏泽', value: 194},
         {name: '合肥', value: 229},
         {name: '武汉', value: 273},
         {name: '大庆', value: 279}
    ];
    var geoCoordMap = {
'上海': [121.4648,31.2891],
'新疆': [87.9236,43.5883],
'甘肃': [103.5901,36.3043],
'总部': [70.4551,50.2539],
'北京': [116.4551,40.2539],
'江苏': [118.8062,31.9208],
'广西': [108.479,23.1152],
'江西': [116.0046,28.6633],
'安徽': [117.29,32.0581],
'内蒙古': [111.4124,40.4901],
'黑龙江': [127.9688,45.368],
'天津': [117.4219,39.4189],
'山西': [112.3352,37.9413],
'广东': [113.5107,23.2196],
'四川': [103.9526,30.7617],
'西藏': [91.1865,30.1465],
'云南': [102.9199,25.4663],
'浙江': [119.5313,29.8773],
'湖北': [114.3896,30.6628],
'辽宁': [123.1238,42.1216],
'山东': [117.1582,36.8701],
'海南': [110.3893,19.8516],
'河北': [114.4995,38.1006],
'福建': [119.4543,25.9222],
'青海': [101.4038,36.8207],
'陕西': [109.1162,34.2004],
'贵州': [106.6992,26.7682],
'河南': [113.4668,34.6234],
'重庆': [107.7539,30.1904],
'宁夏': [106.3586,38.1775],
'吉林': [125.8154,44.2584],
'湖南': [113.0823,28.2568]
}
    var convertData2 = function (originData) {
        var res = [];
var data=JSON.parse(JSON.stringify(originData))
        for (var i = 0; i < data.length; i++) {
            var geoCoord = geoCoordMap[data[i].name];
            if (geoCoord) {
                res.push({
                    name: data[i].name,
                    value: geoCoord.concat(parseInt(formatLargeNumber(data[i].value)))
                });
            }
        }
        return res;
    };
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
                         
            visualMap: {//左侧小导航图标
                show: true,
                x: 'left',
                y: 'center',
                top:'260',
                textStyle: {
                    color: "#8fc8f2"
                },
                splitList: [
                    { start: 200 }, { start: 150, end: 200 },
                    { start: 100, end: 150 }, { start: 50, end: 100 },
                    { start: 0, end: 50 }
                ],
               color: ['#9fb5ea','#F4E925','#85daef', '#74e2ca', '#e6ac53']
            },
                        //布局
                        grid: [
                            { x: '55%', y: '5%', width: '40%', height: '90%' },
                          //  { x: '50%', bottom: '5%', width: '40%', height: '25%' }
                        ],
                        xAxis: [{
                            gridIndex: 0,
                            type: 'value',
                            axisLabel: {
                                show: true,
                            },
                        }],
                        yAxis: [{
                            gridIndex: 0,
                            type: 'category',
                           
                            axisLabel: {
                                show: true,
                                textStyle: {
                                    color: '#fff'
                                }
                            },
                            label: {
                normal: {
                    show: true,
                }
            },
                            data: (function getXYData() {
                                    var data = provinceSale;
                                    var property = "name";
                                    var res = [];
                                    data.forEach(function (item) {
                                        res.push(item[property])
                                    })
                                    return res
                                })(),
                        }],
                        tooltip : {
            trigger: 'item'
        },
                 //布局e
                        geo: {
                            show: true,
                            map: 'china',
                          //  zoom: 1,  //地图绽放
                          //  scaleLimit: 2,
                            label: {
                                normal: {
                                    show: true,
                                    textStyle: {
                                       color: '#ccc'
                                    }
                                },
                                emphasis: {
                                    show: false,
                                }
                            },
                            roam: true,//是否开启鼠标缩放和平移漫游
                            itemStyle: {
                                normal: {
                                    areaColor: 'transparent',
                                   // borderColor: '#3fdaff',
                                    borderWidth:1,
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
                        layoutCenter: ['25%', '50%'],  
                        layoutSize: 400,
                        series: [
                        {
            name: '销售额',
            type: 'scatter',
       coordinateSystem: 'geo',
            data: convertData2(provinceSale),
            symbolSize: function (val) {
                return val[2] / 10;
            },
            label: {
                normal: {
                    formatter: function(a){
return a.value[2]+"万元";
                    },
                    position: 'right',
                    show: false
                },
                emphasis: {
                    show: true
                }
            },
            tooltip: {
                                    formatter: function(a){
                                        return `${a.name} ${a.seriesName}<br/>${a.value[2]}万元`
                                    }
                                },
        },
        {
            name: '销售额前三名',
            type: 'effectScatter',
            coordinateSystem: 'geo',
            data: convertData2(provinceSale.sort(function (a, b) {
                return b.value - a.value;
            }).slice(0, 3)),
            symbolSize: function (val) {
               
                
                return val[2] / 10;
            },
            showEffectOn: 'render',
            rippleEffect: {
                brushType: 'stroke'
            },
            hoverAnimation: true,
            label: {
                normal: {
                    formatter: function(a){
return a.value[2]+"万元";
                    },
                    position: 'right',
                    show: true
                }
            },
            tooltip: {
                                    formatter: function(a){
                                        return `${a.name} ${a.seriesName}<br/>${a.value[2]}万元`
                                    }
                                },
            zlevel: 1
        },
      /*  {
                                type: 'pie',
                                z: 2,
                                selectedMode: 'single',
                                radius: [0, '15%'],        //radius确定饼图的大小
                                center: ['20%', '60%'],    //center来确定饼图中心位置
                                selectedMode: 'single',
                                data: categorySale,
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
                                itemStyle: {
                                    emphasis: {
                                        shadowBlur: 10,
                                        shadowOffsetX: 0,
                                        shadowColor: 'rgba(0, 0, 0, 0.5)'
                                    }
                                }
                            },*/
                         {
                                id: 'bar',
                                name: '年销售额按省份',
                                type: 'bar',
                                xAxisIndex: 0,
                                yAxisIndex: 0,
                               
                                tooltip: {
            
            formatter:function(a,b,c){
            return `${a.name}<br/>${formatLargeNumber(a.value)}万元`;
            }
                },
                                markPoint: {
                                    data: [
                                        { type: 'max', name: '最大值' },
                                        { type: 'min', name: '最小值' }
                                    ]
                                },
                                markLine : {
                data : [
                    {type : 'average', name : '平均值'}
                ]
            },
                               /* itemStyle: {
                                    //每个柱子的颜色即为colorList数组里的每一项，如果柱子数目多于colorList的长度，则柱子颜色循环使用该数组
                                    color: function (params) {
                                        var colorList = ['#c23531', '#2f4554', '#61a0a8', '#d48265', '#91c7ae', '#749f83', '#ca8622', '#bda29a', '#6e7074', '#546570', '#c4ccd3'];
                                        return colorList[params.dataIndex];
                                    }
                                },*/
                                z: 2,
                                data:that.provinceSale
                            },
                          /*  {
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
                            }*/
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
                       
                        series: [
                            {
                                type: 'pie',
                                radius: '50%',
                                center: ['50%', '50%'],
                                selectedMode: 'single',
                                data: categorySale,
                                label: {
                                    show: true,
                                    formatter: function(res){
                                        
                                        return res.data.category;

                                    }
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
              drawMultiCategoryPieChart(){
                  var categoryClassifyData=this.categoryClassifyData;
                var option = {
        legend: {
            show:false
        },
        tooltip: {
            
    formatter:function(a,b,c){
    return `${a.name}<br/>${formatLargeNumber(a.value)}万元`;
    }
        },
      
        series: [{
            type: 'pie',
            radius: 50,
            center: ['25%', '20%'],
            data: categoryClassifyData[0].data
            // No encode specified, by default, it is '2012'.
        }, {
            type: 'pie',
            radius: 50,
            center: ['65%', '20%'],
            data: categoryClassifyData[1].data
        }, {
            type: 'pie',
            radius: 50,
            center: ['25%', '65%'],
            data: categoryClassifyData[2].data
        }, {
            type: 'pie',
            radius: 50,
            center: ['65%', '65%'],
            data: categoryClassifyData[3].data
        }]
    };
    let myChart = this.$echarts.init(this.$refs.multiPieChart);
                    window.onresize = myChart.resize;
                    myChart.setOption(option)
              },
                drawBarChart() {
                    var monthSale =this.monthSale;
                    var option = {
                        color: ['#3398DB'],
                        title: {
                            text: '月销售额（万）', 
                            left: 'center',
                            textStyle: {
                                color: '#9AA8D4'
                            }
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
                                data: monthArray,
                                axisLabel: {
                                    textStyle: {
                                        color: '#fff'
                                    }
                                }
                            }
                        ],
                        yAxis: [
                            {
                                type: 'value',
                                axisLabel: {
                                    textStyle: {
                                        color: '#fff'
                                    }
                                }
                            }
                        ],
                        series: [
                            {
                                name: '月销售总额',
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
    var itemStyle={
        normal:{
          label:{
            formatter:function(a){
                return formatLargeNumber(a.value);}
          }
        },
        };
        var markPoint= {
                                    data: [
                                        { type: 'max', name: '最大值' },
                                    ],
                                itemStyle:itemStyle
                                };
                    var monthCategoryData=this.monthCategoryData;
                    var option = {
                        title:{  
                             text: '销售额（按 自行车）(万)', 
                            textStyle: {
                                color: '#9AA8D4'
                            }},
                        tooltip: {
                            trigger: 'axis',
                            axisPointer: {            // 坐标轴指示器，坐标轴触发有效
                                type: 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                            },
                                    
    formatter:function(a,b,c){
    
    return `${a[0].axisValue}<br/>${a[0].seriesName}  ${formatLargeNumber(a[0].value)}万元`;
    }
                        },
                        legend: {
                            left:'right',
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
                            },
                            selectedMode: 'single'
                        },
                        grid: {
                            left: '3%',
                            right: '4%',
                            bottom: '3%',
                            containLabel: true
                        },
                        yAxis: {
                            type: 'value',
                                axisLabel: {
                                    textStyle: {
                                        color: '#fff'
                                    },
                                   
    
                                }
                        },
                        xAxis: {
                            type: 'category',
                            data: monthArray,
                                axisLabel: {
                                    textStyle: {
                                        color: '#fff'
                                    }
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
                                data: monthCategoryData["自行车"],
                                itemStyle,
                                markPoint,
                                
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
                                data: monthCategoryData["服装"],
                                itemStyle,
                                markPoint,
                                
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
                                data: monthCategoryData["配件"],
                                itemStyle,
                                markPoint
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
                                data: monthCategoryData["辅助用品"],
                                itemStyle,
                                markPoint
                            }
    
                        ]
                    };
                    this.myChart = this.$echarts.init(this.$refs.categoryChart);
                    window.onresize = this.myChart.resize;
                    this.myChart.setOption(option)
    
                    const data = this.myChart.getOption().legend[0].data;
                     
                let i = 0// 首次总是从0开始的
                // 开始轮播
                this.timer = setInterval(() => {
                    //legendUnSelect（取消选中图例）
                    this.myChart.dispatchAction({ type: 'legendUnSelect', name: data[ i % data.length ] })
                    // legendToggleSelect（切换图例的选中状态）
                    this.myChart.dispatchAction({ type: 'legendToggleSelect', name: data[ ++i % data.length ] })
             option.title.text=`销售额（按 ${data[ i % data.length ]}）`; //动态设置标题
             this.myChart.setOption(option)
                }, 3500)
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
                },
                getCategoryClassifyData(){
                    var subCategorySale=this.subCategorySale;
                var subcategory=this.subcategory;
                var map = {},
            dest = [];//新的json数组
        for(var i = 0; i < subCategorySale.length; i++){
            var ai = subCategorySale[i];
    var category=ai.name;
    var categoryItem=subcategory.find(m=>m.product==category);
            if(!map[categoryItem.categoryid]){//创建一个新的jsonobject
                dest.push({
                        categoryid: categoryItem.categoryid,
        categoryName:categoryItem.category,
                    data: [ai]
                });
                map[categoryItem.categoryid] = ai;
            }else{//放到对应的id下面
                for(var j = 0; j < dest.length; j++){
                    var dj = dest[j];
                    if(dj.categoryid == categoryItem.categoryid){
                        dj.data.push(ai);
                        break;
                    }
                }
            }
        }
                }
    
            },
            mounted() {
                // this.getCategoryData();
                this.drawMultiChart2();
                this.drawMultiCategoryPieChart();
                this.drawCategorybyMonth();
                this.drawPieChart();
                this.drawBarChart();
       // this.formatMonthCategoryData();
    //this.getbarData();
             
                //   this.drawChart();
               //   this.drawMapChart();
                //  this.drawMapChart2();
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