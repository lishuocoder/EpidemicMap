<template>
	<div class="hello">
		<!-- 初始化echarts 需要个有宽高的盒子 -->
		<div ref="mapbox" class="box">
		</div>
	</div>
</template>

<script>
	import echarts from 'echarts'
	import 'echarts/map/js/china.js'
	import jsonp from 'jsonp'

	const option = {
		title: {
			text: '疫情地图'
		},
		series: [{
			type: 'map', //告诉echarts 渲染地图
			map: 'china',
			label: {
				show: true, //显示各个省份名称
				fontSize: 8,
			},
			itemStyle: {
				areaColor: '#fff' //区域的背景颜色
			},
			emphasis: {
				//控制鼠标滑过时的高亮样式
				itemStyle: {
					areaColor: '#c7fffd'
				}
			},
			zoom: 1.2, //控制地图的大小
		}],
		visualMap: [{
			type: 'piecewise',
			show: true,
			splitNumber: 6,
			pieces: [{
					min: 10000
				}, // 不指定 max，表示 max 为无限大（Infinity）。
				{
					min: 1000,
					max: 9999
				},
				{
					min: 100,
					max: 999
				},
				{
					min: 10,
					max: 99
				},
				{
					min: 1,
					max: 9
				},
				{
					min: 0,
					max: 0
				}
			],
			//align:'right' // 默认是left
			inRange: {
				symbol: 'rect',
				color: ['#fff', '#ffaa85', '#ff7b69', '#cc2929', '#8c0d0d', '#660208']
			},
			itemHeight: 10,
			itemWidth: 20
		}]
	};
	export default {
		name: 'Map',
		mounted() {
			this.getData();
			this.mychart = echarts.init(this.$refs.mapbox); //获取mapbox盒子
			this.mychart.setOption(option);
		},
		methods: {
			getData() {
				jsonp(
					'http://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427', {}, (erros, data) => {
						// console.log(data);
						// console.log(data.data.list)
						var lists = data.data.list.map(item => {
							return {
								name: item.name,
								value: item.value
							}
						})
						console.log(lists)
						option.series[0].data = lists;
						this.mychart.setOption(option);
					})
			}
		}
	}
</script>

<style scoped>
	.box{
		width: 750px;
		height: 650px;
		margin: auto;
		/* border: 2px solid aquamarine; */
	}
</style>
