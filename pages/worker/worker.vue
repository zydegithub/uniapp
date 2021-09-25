<template>
	<view id="worker">
		<view class="content">
			<view class="workerInfo">
				<text class="name">张三</text></br>
				<text class="title">工种：木工</text></br>
				<text class="title">电话：18813191617</text>
			</view>
			<view class="imgInfo">
				<image class="logo" src="/static/logo.png"></image>
			</view>
		</view>
		<view class="workerOperation">
			<view class="opeBtnText" @click="showCalendar">
				<view class="opeBtn">
					<image class="opeImg" src="/static/worker/rili.png"></image>
				</view>
				<text class="optText">查询</text>
			</view>
			<view class="opeBtnText">
				<view class="opeBtn">
					<image class="opeImg" src="/static/worker/yuzhi.png"></image>
				</view>
				<text class="optText">预支</text>
			</view>
			<view class="opeBtnText">
				<view class="opeBtn">
					<image class="opeImg" src="/static/worker/hesuan.png"></image>
				</view>
				<text class="optText">结算</text>
			</view>
		</view>
		<view class="workerRecord ">
			
		</view>
		<uni-calendar 
		    ref="calendar"
			:lunar="false" 
		    :insert="false"
			:selected="selectDate"
		/>
	</view>
</template>

<script>
export default {
	components: {},
	data(){
		return{
			selectDate:[
				{date: '2021-09-24', info: '签到'},
				{date: '2021-09-25', info: '签到'},
				{date: '2021-09-26', info: '签到'},
			]
		}
	},
	methods:{
		showCalendar(){
			this.$refs.calendar.open();
		},
		confirm(){
			
		}
	}
}
</script>

<style lang="less">
#worker{
	position: absolute;
	top: 0px;
	bottom: 0px;
	right: 0px;
	left: 0px;
	background-color: #F5F5F5;
	.content{
		width: 100%;
		height: 210px;
		background-color: #007AFF;
		box-shadow: 0px 15px 10px -15px #000;;
		.workerInfo{
			float: left;
			padding-left: 30px;
			padding-top: 20px;
			color:#ffffff;
			.name{
				line-height: 60px;
				font-size: 22px;
				font-weight: 400;
			}
		}
		.imgInfo{
			float: right;
			padding: 36px;
			.logo{
				width:80px;
				height: 80px;
				border-radius: 50%;
			}
		}
	}
	.workerOperation{
		position: absolute;
		height: 100px;
		top: 160px;
		right: 20px;
		left: 20px;
		border-radius: 5px;
		background-color: #FFFFFF;
		display: flex;
		justify-content: space-around;
		align-items: center;
		.opeBtnText{
			width: 46px;
			height: 70px;
			text-align: center;
			border-radius: 30%;
			.opeBtn{
				width: 46px;
				height: 46px;
				background-color: #007AFF;
				border-radius: 30%;
				.opeImg{
					width: 26px;
					height: 26px;
					margin-top: 10px;
				}
			}
			.optText{
				margin-top: 100px;
			}
		}
	}
	.workerRecord{
		position: absolute;
		bottom: 0px;
		top: 280px;
		right: 20px;
		left: 20px;
		border-radius: 5px;
		background-color: #FFFFFF;
		display: flex;
		justify-content: space-around;
		align-items: center;
	}
}
</style>
