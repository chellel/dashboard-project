<template>
  <div class="page ">
    <div class="row">
    
      <div class="col-md-4">
          <div class="col-sm-12 col-md-12">
            <div class="box-content">
              <div class="statistic-box row">
                <div class="statistic-item col-md-6"
                     v-for="item in [1,2,3,4,5,6]"
                     v-bind:key="item">
                  <div class="icon-content">
                    <span class="iconfont icon-shuju"></span>
                  </div>
                  <div class="text-content">
                    <div class="text">
                      <span class="counter">
                        <ICountUp :delay="delay"
                                  :endVal="endVal"
                                  :options="options"
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
              <div class="box-content">
                <div class="piechart"></div>
                
              </div>
            </div>
      </div>

      <div class="col-md-8">
        <div class="row">
          <div class="col-sm-12 col-md-12">
            <div class="box-content">
              <div id="myChart"
                   style="width: 600px;height:400px;"></div>

              <div class="caption">
                <h3>Thumbnail label</h3>
                <p>Cras justo odio, dapibus ac facilisis in, egestas eget quam. Donec id elit non mi
                  porta gravida at eget metus. Nullam id dolor id nibh ultricies vehicula ut id elit.
                </p>
                <p><a href="#"
                     class="btn btn-primary"
                     role="button" @click="changeData">Button</a>
                      <a href="#"
                     class="btn btn-default"
                     role="button">Button</a></p>
              </div>
            </div>
          </div>
        </div>

      </div>
      <div class="col-md-12">
            <div class="chart" id="yqyMain"></div>
       </div>
    </div>
  </div>
</template>
<script>
import ICountUp from 'vue-countup-v2'

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

//用java代码：自动生成关系图数据（因为前期，没有理解需求——_——:全是自己用java自动生成的json数据）
var relatData = [{
    "node": "系统节点1",
    "endpoint": ["系统节点3"],
    "service": ["java.local.name_1", "java.local.name_11"]
}, {
    "node": "系统节点2",
    "endpoint": ["系统节点3", "系统节点1"],
    "service": ["java.local.name_2", "java.local.name_22"]
}, {
    "node": "系统节点3",
    "endpoint": ["系统节点4", "系统节点2"],
    "service": ["java.local.name_3", "java.local.name_33"]
}, {
    "node": "系统节点4",
    "endpoint": ["系统节点3"],
    "service": ["java.local.name_4", "java.local.name_44"]
}, {
    "node": "系统节点5",
    "endpoint": ["系统节点2"],
    "service": ["java.local.name_5", "java.local.name_55"]
}, {
    "node": "系统节点6",
    "endpoint": ["系统节点3"],
    "service": ["java.local.name_6", "java.local.name_66"]
}, {
    "node": "系统节点7",
    "endpoint": ["系统节点2"],
    "service": ["java.local.name_7", "java.local.name_77"]
}, {
    "node": "系统节点8",
    "endpoint": ["系统节点2"],
    "service": ["java.local.name_8", "java.local.name_88"]
}];

//获取节点数据
function get_nodes(relatData) { //官方的方法改造了一下=自定义生成：关系图中节点位置
    var nodes = [];
    var tmp_nodes = [];
    var x1 = 1200;
    var y1 = 100;

    for (var nodes_i in relatData) {
        //x,y数据归位
        x1 = 5;
        y1 = 5;
        //三个节点为一排，之字形增加
        x1 = x1 + 10 * (nodes_i % 3); //保持0，1，2
        y1 = y1 + 10 * Math.floor(nodes_i / 3); //向下取整

        console.log("x1=" + x1 + " y1=" + y1);
        console.log("x1=" + (nodes_i % 3) + " y1=" + (Math.floor(nodes_i / 3)));
        tmp_nodes.push(relatData[nodes_i].node);
        nodes.push({

            'name': relatData[nodes_i].node,
            'value': [x1, y1] //4.通过x,y来确定关系图的节点位置
        });
    }
    return nodes;
}

//获取节点数据关系
function get_links(relatData) {
    var links = [];
    for (var nodes_i in relatData) {
        var node = relatData[nodes_i].node;
        var endpoint = relatData[nodes_i].endpoint;
        var service = relatData[nodes_i].service;
        // console.log(service);
        for (var service_i in endpoint) {
            links.push({
                'source': node,
                'target': endpoint[service_i],
                'label': {
                    'normal': {
                        'show': false,
                        'textStyle': {
                            'fontSize': 5
                        },
                        'formatter': service
                    }
                },
                'lineStyle': {
                    'normal': {
                        'curveness': 0.1
                    }
                }
            })
        }
    }
    for (var i = 0, len1 = links.length; i < len1; i++) {
        for (var j = i, len2 = len1 - 1; j < len2; j++) {
            if (links[i].source == links[j].target) {
                links[j].lineStyle.normal.curveness = -0.1;
            }
        }
    }
    // console.log(links);
    return links;
}
//-----------------------------关系图获取数据:end--------------------------------------------

//按老大要求：多个图表数据整合成一个option
//【关键点】：1.用最少的代码显示出一个图（eg:饼图只要一个series就可以显示出来）
//2.确定每个图表的位置，占的区域（eg:饼图通过配置center来确定中心位置，radius确定饼图的大小）。


//我选折线图作为基础option
var multiChartOption = {
    xAxis: [{
            data: yqyData[0],
            gridIndex: 0
        }, //折线图x轴数据赋值，指定坐标信息
        {
            gridIndex: 1,
            type: 'value'
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
    }],

    grid: [ //指定坐标轴位置，大小
        {
            x: '7%',
            y: '7%',
            width: '50%',
            height: '31%'
        }, {
            x: '60%',
            bottom: '1%',
            height: '90%',
            width: '35%',
            contianLabel: true
        } //关系图位置
    ],

    series: [{
            type: 'line',
            xAxisIndex: 0, //指定折线图数据显示到：grid坐标系：0
            yAxisIndex: 0,
            showSymbol: false,
            data: yqyData[1] //折线图y轴数据赋值
        },
        //关系图数据
        {
            type: 'graph',
            // layout: 'circular',
            layout: 'force', //1.力引导图
            coordinateSystem: 'cartesian2d', //2.笛卡尔坐标系设置
            xAxisIndex: 1, //3.选取的第二个坐标系，为关系图数据位置
            yAxisIndex: 1,
            focusNodeAdjacency: true,
            legendHoverLink: true,
            hoverAnimation: true,
            symbolSize: 30,
            edgeSymbolSize: 10,
            roam: true,
            symbol: "roundRect",
            label: {
                normal: {
                    show: true,
                }
            },
            edgeSymbol: ['circle', 'arrow'],
            edgeLabel: {
                normal: {
                    textStyle: {
                        fontSize: 20
                    }
                }
            },
            data: get_nodes(relatData), //节点数据赋值
            links: get_links(relatData),

            lineStyle: {
                normal: {
                    opacity: 0.9,
                    width: 2,
                    curveness: 0,
                    type: 'dashed'
                }
            }
        },
        {
            name: '面积模式',
            type: 'pie',
            radius: [10, 80],
            center: ['18%', '75%'],
            data: pieData[0] //饼图数据赋值
        }
    ]
};


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
      total: 100
    }
  },
  methods: {
    drawChart() {
      // 基于准备好的dom，初始化echarts实例
      let element=document.getElementById('myChart');
      if(element==null)
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
    drawMultiChart(){
        let element=document.getElementById('yqyMain');
      if(element==null)
      return
      let chart = this.$echarts.init(element)
          //每次窗口大小改变的时候都会触发onresize事件，这个时候我们将echarts对象的尺寸赋值给窗口的大小这个属性，从而实现图表对象与窗口对象的尺寸一致的情况
            window.onresize = chart.resize;
          
		    /*接受自定义Option.js中的函数返回的：option*/
            chart.setOption(multiChartOption);
            this.multiChart=chart;
            
    },
    changeData(){
        var pieChartSeries2=  {
            name: '面积模式',
            type: 'pie',
            radius: [10, 50],
            center: ['48%', '75%'],
            data: pieData[0] //饼图数据赋值
        };
        multiChartOption.series.push(pieChartSeries2)
        this.multiChart.setOption(multiChartOption);
    },
    onReady: function(instance, CountUp) {
      return
      const that = this
      instance.update(that.endVal + 100)
    }
  },
  mounted() {
    this.drawChart();
    this.drawMultiChart();
  }
}
</script>
 <style scoped>


.box-content {
  display: block;
  padding: 10px 18px;
  margin-bottom: 20px;
  line-height: 1.42857143;
  background-color: #fff;
  -webkit-transition: border 0.2s ease-in-out;
  -o-transition: border 0.2s ease-in-out;
  transition: border 0.2s ease-in-out;
  box-shadow: 1px 2px 8px 0px #888888;
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
}

.statistic-item .sub-title {
  font-size: 14px;
  text-align: left;
}
.chart{height: 700px;}
</style>