<template>
	<el-row>
		<el-col :span="18">
			<el-row>
				<el-col :span="12">
					<!-- 采购需求 -->
					<div class="chart" :style="{height:((height-80)/2 - 30)+'px'}">
						<div class="itemtitle" style="">行政区邮件阈值率</div>
						<v-chart ref="xuqiuchart" :style="{height:(((height-80)/2 - 30 - 25)+'px')}" :option="xuqiuchart" />
					</div>
				</el-col>
				<el-col :span="12">
					<!-- 在售产品 -->
					<div class="chart" :style="{height:((height-80)/2 - 30)+'px'}">
						<div class="itemtitle" style="">行政区邮件增长量对比</div>
						<v-chart ref="goodschart" :style="{height:(((height-80)/2 - 30 - 25)+'px')}" :option="goodschart" />
					</div>
				</el-col>
			</el-row>
			<div>
				<!-- 价格走势 -->
				<div class="chart" :style="{height:((height-80)/2 - 30)+'px'}">
					<div class="itemtitle" style="">各行政区邮件量年变化趋势</div>
					<v-chart ref="costchart" :style="{height:(((height-80)/2 - 30 - 25)+'px')}" :option="costchart" />
				</div>
			</div>
		</el-col>
		<el-col :span="6">
			<!-- 市场资讯 -->
			<div class="chart" :style="{height:((height-80 - 20) )+'px'}">
				<div class="itemtitle" style="">中国邮政新闻	</div>
				<div>
					<div class="swiper-container" :style="{height:(((height-80) - 20  - 45)+'px')}">
						<div class="swiper-wrapper" :style="{height:(((height-80) - 20 - 45)+'px')}">
							<div v-for="(item,index) in news" class="swiper-slide newitem" style="height: 110px;">
								<div class="innner">
									<div class="title">{{item.title}}</div>
									<div class="flex">
										<div class="flex_item">时间：{{item.date}}</div>
										<div class="flex_item">来源：{{item.from_}}</div>
									</div>
									<div class="desc">
										{{item.desc}}
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</el-col>
	</el-row>
</template>

<script>
	import $ from 'jquery'
	import config from '/public/config.js'
	import utils from '/public/utils.js'
	import api from '/public/api.js'
	import VChart, {
		THEME_KEY
	} from "vue-echarts";
	import * as echarts from 'echarts';
	
	import Swiper from 'swiper';
	import 'swiper/dist/css/swiper.min.css';

	export default {
		components: {
			VChart
		},
		provide: {
			[THEME_KEY]: "dark"
		},
		data() {
			return {
				height: $(window).height(),
				charts: {},
				timer: null,
				xuqiuchart: {},
				goodschart: {},
				costchart: {},
				categorys : ['东城区', '西城区', '海淀区', '朝阳区', '丰台区'],
				news: [{
					id: 1,
					title: '缙云县气象台发布暴雨橙色预警[Ⅱ级/严重]',
					desc: '缙云县气象台2022年04月26日21时20分将暴雨黄色预警信号升级为暴雨橙色预警信号：过去三小时我县新建、新碧、东方、壶镇、前路、五云等中北部乡镇出现50mm以上的暴雨',
					date: '2022-04-26 21:20:00',
					from_: '中国天地图'
				}, {
					id: 1,
					title: '和平县气象台发布暴雨橙色预警',
					desc: '和平县气象台于04月26日20时47分将和平县暴雨黄色预警升级为橙色预警，请注意加强防御。',
					date: '2022-04-26 20:55:00',
					from_: '中国天地图'
				}, {
					id: 1,
					title: '建阳区气象台发布暴雨橙色预警[Ⅱ级/严重]',
					desc: '建阳区气象台2022年04月26日20时10分发布暴雨橙色预警信号：过去3小时我区徐市镇出现明显降水，雨量达62.6毫米，预计未来3小时内我区部分乡镇仍有较强降水。请注意防范！',
					date: '2022-04-26 20:10:00',
					from_: '中国天地图'
				}, {
					id: 1,
					title: '台州市黄岩区气象台发布暴雨橙色预警[Ⅱ级/严重]',
					desc: '台州市黄岩区气象台2022年4月26日19时10分发布暴雨橙色预警信号：受强对流云团影响，目前上游仙居已出现短时暴雨，预计今天夜里我区有阵雨或雷雨，其中上郑乡、富山乡、屿头乡、宁溪镇等乡（镇、街道）将出现50毫米以上的降水，最大小时雨强可达30毫米以上，有雷雨时可伴有短时强降水、强雷电等强对流天气。请注意防范强降雨及可能引发的小流域山洪、山体滑坡和城乡积涝等次生灾害。',
					date: '2022-04-25 20:27:00',
					from_: '中国天地图'
				}, {
					id: 1,
					title: '渝水区气象台更新暴雨橙色预警[II级/严重]',
					desc: '渝水区气象台2022年04月25日20时27分变更暴雨橙色预警信号：目前我区新和累计降雨量已达90毫米以上，预计未来3小时内降水仍将持续，请注意防范强降雨引发的城乡内涝和山洪、泥石流等地质灾害。',
					date: '2022-04-25 20:27:00',
					from_: '中国天地图'
				}, {
					id: 1,
					title: '五峰土家族自治县气象台发布暴雨橙色预警[II级/严重]',
					desc: '五峰土家族自治县气象台2022年04月25日18时28分发布暴雨橙色预警信号：目前我县长乐坪镇、五峰镇、湾潭镇、渔洋关镇及周边1小时降水超过20毫米',
					date: '2022-04-25 18:31:20',
					from_: '中国天地图'
				}],
				swiper:null
			}
		},
		mounted: function() {
			var that = this;
			$(window).resize(function() {
				this.height = $(window).height() - this.titleheight;

				for (var key in that.charts) {
					that.charts[key].resize();
				}
				that.$refs['xuqiuchart'].resize();
				that.$refs['goodschart'].resize();
				that.$refs['costchart'].resize();
			});
			
			this.swiper = new Swiper('.swiper-container', {
				autoplay: 1000, //可选选项，自动滑动
				loop: true,
				direction: 'vertical',
				slidesPerView: (((this.height - 80) - 35)) / 110,
			})

			this.initcharts();

			//开始定时刷新报表数据
			this.startRefreshChart();
		},
		unmounted: function() {
			if (this.timer) {
				clearInterval(this.timer);
			}
		},
		methods: {
			/**
			 * 定时刷新报表数据
			 */
			startRefreshChart: function() {
				if (this.timer) {
					clearInterval(this.timer);
				}
				var that = this;

				//获取刷新周期，TODO 配置变动时，此处需自动更新
				var refreshtime = 60 * 1000;
				config.getConfig().forEach(function(item, index) {
					if (item.key == 'refreshtime') {
						refreshtime = item.value;
					}
				});

				this.timer = setInterval(function() {
					that.swiper.slideNext();
					
					//---------------
					var values = [(Math.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0)];
					that.xuqiuchart.series[0].data = values;
					that.xuqiuchart.series[1].data = values;
					that.xuqiuchart.series[2].data = values;
							
					//---------------
					var values = [(Math.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0)];
					that.goodschart.series[0].data = values;
					that.goodschart.series[1].data = values;
					that.goodschart.series[2].data = values;
							
					//---------------
					var dates = [];
					var start = new Date();
					start.setDate(start.getDate()-30);
					for(var i=new Date(start.getTime());i.getTime()<new Date().getTime();i.setDate(i.getDate()+1)){
						dates.push(i.format('MM-dd'));
					}
					
					var series = [];
					var xData = [];
					that.categorys.forEach(function (item,index){
						xData.push(item);
						series.push({
							name: item,
							data: [],
							type: 'line',
							stack:'1',
							// smooth:true,
							lineStyle: {
								nomal:{
									width:1,
									color: config.colors[index%config.colors.length]
								}
							},
							label: {
								show: true,
								formatter: '{c}%',
								position: 'top',
								color: 'rgba(255,228,59,1)',
								fontSize: 12,
								textAlign: 'center',
							}
						});
						for(var i=new Date(start.getTime());i.getTime()<new Date().getTime();i.setDate(i.getDate()+1)){
							series[index].data.push((Math.random(100) * 100).toFixed(0));
						}
					});		
					that.costchart.xAxis.data = dates;
					that.costchart.series = series;
				}, refreshtime);
			},
			initcharts: function() {
				this.xuqiuchart = this.initxuqiuchart();
				this.goodschart = this.initgoodschart();
				this.costchart = this.initcostchart();
			},
			initxuqiuchart: function() {
				var values = [(Math.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0)];
				return {
					backgroundColor: '#000000',
					grid: utils.createChartGaid(null, null, "20px", "20px"),
					tooltip: {
						trigger: 'axis',
						axisPointer: {
							type: 'shadow',
						},
					},
					xAxis: {
						axisLine: {
							show: true,
							lineStyle: {
								color: utils.getChartXColor()
							}
						},
						axisLabel: {
							color: utils.getChartXTextColor()
						},
						axisTick: {
							show: false
						},
						splitLine: {
							show: false
						},
						// boundaryGap: false,//
						data: this.categorys,
						type: 'category',
					},
					yAxis: {
						axisLabel: {
							color: utils.getChartYTextColor(),
						},
						axisLine: {
							show: true,
							lineStyle: {
								color: utils.getChartXColor()
							}
						},
						splitLine: {
							lineStyle: {
								color: utils.getChartYColor(),
								type: 'dashed'
							},
						},
						name: '',
					},
					series: [
					// 	{
					// 	name: "含量",
					// 	data: [(Math.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
					// 		.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
					// 		.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
					// 		.random(100) * 100).toFixed(0)],
					// 	type: 'bar',
					// 	barWidth: '20px',
					// 	label: {
					// 		show: true,
					// 		formatter: '{c}%',
					// 		position: 'top',
					// 		color: 'rgba(255,228,59,1)',
					// 		fontSize: 12,
					// 		textAlign: 'center',
					// 	},
					// 	itemStyle: {
					// 		color: function(pama) {
					// 			var colors = ['#fac858', '#2aa7ee', '#ee6666', '#91cc75', '#38cafb',
					// 				'#4caff9', '#4adeca', '#0270f2', '#488cf7'
					// 			];
					// 			return new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
					// 					offset: 0,
					// 					color: colors[pama.dataIndex % colors.length] + 'ff',
					// 				},
					// 				{
					// 					offset: 1,
					// 					color: colors[pama.dataIndex % colors.length] + '66',
					// 				},
					// 			])
					// 		},
					// 		barBorderRadius: 20
					// 	}
					// },
					{
						name: '新员工数量',
						type: 'bar',
						barWidth: '20px',
						barGap: '60%',
						itemStyle: {
							color: '#FF5252aa',
							borderColor: '#FF5252aa',
							borderWidth: 1,
							borderType: 'solid',
						},
						label: {
							show: true,
							formatter: '{c}' + '%',
							position: 'top',
							color: 'rgba(255,228,59,1)',
							fontSize: 12,
							textAlign: 'center',
						},
						zlevel: 2,
						data: values,
					},
					{
						name: '员工离职数量',
						type: 'pictorialBar',
						symbolSize: [20, 10],
						symbolOffset: [0, -5],
						symbolPosition: 'end',
						z: 15,
						color: '#FF5252',
						zlevel: 3,
						data: values,
					},
					{
						name: '在职员工数量',
						type: 'pictorialBar',
						symbolSize: [20, 10],
						symbolOffset: [0, 5],
						symbolPosition: 'start',
						z: 15,
						color: '#FF5252',
						zlevel: 3,
						data: values,
					}]
				};
			},
			initgoodschart: function() {
				var values = [(Math.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
							.random(100) * 100).toFixed(0)];
				return {
					backgroundColor: '#000000',
					grid: utils.createChartGaid(null, null, "20px", "20px"),
					tooltip: {
						trigger: 'axis',
						axisPointer: {
							type: 'shadow',
						},
					},
					xAxis: {
						axisLine: {
							show: true,
							lineStyle: {
								color: utils.getChartXColor()
							}
						},
						axisLabel: {
							color: utils.getChartXTextColor()
						},
						axisTick: {
							show: false
						},
						splitLine: {
							show: false
						},
						// boundaryGap: false,//
						data: this.categorys,
						type: 'category',
					},
					yAxis: {
						axisLabel: {
							color: utils.getChartYTextColor(),
						},
						axisLine: {
							show: true,
							lineStyle: {
								color: utils.getChartXColor()
							}
						},
						splitLine: {
							lineStyle: {
								color: utils.getChartYColor(),
								type: 'dashed'
							},
						},
						name: '',
					},
					series: [
					// 	{
					// 	name: "含量",
					// 	data: [(Math.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
					// 		.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
					// 		.random(100) * 100).toFixed(0), (Math.random(100) * 100).toFixed(0), (Math
					// 		.random(100) * 100).toFixed(0)],
					// 	type: 'bar',
					// 	barWidth: '20px',
					// 	label: {
					// 		show: true,
					// 		formatter: '{c}%',
					// 		position: 'top',
					// 		color: 'rgba(255,228,59,1)',
					// 		fontSize: 12,
					// 		textAlign: 'center',
					// 	},
					// 	itemStyle: {
					// 		color: function(pama) {
					// 			var colors = ['#fac858', '#4adeca', '#0270f2', '#488cf7', '#2aa7ee', '#ee6666', '#91cc75', '#38cafb',
					// 				'#4caff9'
					// 			];
					// 			return new echarts.graphic.LinearGradient(0, 0, 0, 1, [{
					// 					offset: 0,
					// 					color: colors[pama.dataIndex % colors.length] + 'ff',
					// 				},
					// 				{
					// 					offset: 1,
					// 					color: colors[pama.dataIndex % colors.length] + '66',
					// 				},
					// 			])
					// 		},
					// 		barBorderRadius: 20
					// 	}
					// },
						{
							name: '新员工数量',
							type: 'bar',
							barWidth: '20px',
							barGap: '60%',
							itemStyle: {
								color: '#BAFF7Faa',
								borderColor: '#BAFF7Faa',
								borderWidth: 1,
								borderType: 'solid',
							},
							label: {
								show: true,
								formatter: '{c}' + '%',
								position: 'top',
								color: 'rgba(255,228,59,1)',
								fontSize: 12,
								textAlign: 'center',
							},
							zlevel: 2,
							data: values,
						},
						{
							name: '离职员工数量',
							type: 'pictorialBar',
							symbolSize: [20, 10],
							symbolOffset: [0, -5],
							symbolPosition: 'end',
							z: 15,
							color: '#BAFF7F',
							zlevel: 3,
							data: values,
						},
						{
							name: '在职员工数量',
							type: 'pictorialBar',
							symbolSize: [20, 10],
							symbolOffset: [0, 5],
							symbolPosition: 'start',
							z: 15,
							color: '#BAFF7F',
							zlevel: 3,
							data: values,
						}
					]
				};
			},
			initcostchart: function() {
				var dates = [];
				var start = new Date();
				start.setDate(start.getDate()-30);
				for(var i=new Date(start.getTime());i.getTime()<new Date().getTime();i.setDate(i.getDate()+1)){
					dates.push(i.format('MM-dd'));
				}
				
				var series = [];
				var xData = [];
				this.categorys.forEach(function (item,index){
					xData.push(item);
					series.push({
						name: item,
						data: [],
						type: 'line',
						stack:'1',
						// smooth:true,
						lineStyle: {
							nomal:{
								width:1,
								color: config.colors[index%config.colors.length]
							}
						},
						label: {
							show: true,
							formatter: '{c}%',
							position: 'top',
							color: 'rgba(255,228,59,1)',
							fontSize: 12,
							textAlign: 'center',
						}
					});
					for(var i=new Date(start.getTime());i.getTime()<new Date().getTime();i.setDate(i.getDate()+1)){
						series[index].data.push((Math.random(100) * 100).toFixed(0));
					}
				});
				return {
					backgroundColor: '#000000',
					grid: utils.createChartGaid(null, null, "20px", "40px"),
					tooltip: {
						trigger: 'axis',
						axisPointer: {
							type: 'shadow',
						},
					},
					legend:{
						bottom:'0',
						x:'center',
						data:xData
					},
					xAxis: {
						axisLine: {
							show: true,
							lineStyle: {
								color: utils.getChartXColor()
							}
						},
						axisLabel: {
							color: utils.getChartXTextColor()
						},
						axisTick: {
							show: false
						},
						splitLine: {
							show: false
						},
						boundaryGap: false,//
						data: dates,
						type: 'category',
					},
					yAxis: {
						axisLabel: {
							color: utils.getChartYTextColor(),
						},
						axisLine: {
							show: true,
							lineStyle: {
								color: utils.getChartXColor()
							}
						},
						splitLine: {
							lineStyle: {
								color: utils.getChartYColor(),
								type: 'dashed'
							},
						},
						name: '',
					},
					series: series
				};
			}
		}
	}
</script>

<style scoped="scoped">
	.chart {
		padding: 10px;
		overflow: hidden;
	}

	.itemtitle {
		background: linear-gradient(to right, #40a55f, #000, #000);
		height: 35px;
		line-height: 35px;
		padding-left: 10px;
	}

	.name_border {
		width: 100%;
		height: 1px;
		margin: 5px auto;
		background: linear-gradient(90deg, rgba(0, 0, 0, 0), #40a55f, #40a55f, #40a55f, #40a55f, rgba(0, 0, 0, 0));
	}

	.name_v_border {
		width: 1px;
		height: 70px;
		background: linear-gradient(0deg, rgba(0, 0, 0, 0), #40a55f, #40a55f, rgba(0, 0, 0, 0));
	}
	
	
	.newitem {
		height: 110px;
		border-bottom: 1px dotted rgba(255, 255, 255, 0.5);
		font-size: 14px;
		line-height: 20px;
		color: #999;
		cursor: pointer;
	}
	
	.newitem .title {
		font-size: 16px;
		line-height: 35px;
		height: 35px;
		text-align: left;
		color: #FF5722;
	}
	
	.newitem .desc {
		height: 40px;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 2;
		/* // 显示的行 */
	}
	
	.newitem:hover {
		color: #fac858aa;
	}
</style>
