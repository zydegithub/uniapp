<template>
	<view id="worker">
		<view class="content">
			<view class="workerInfo">
				<text class="name">{{ workerInfo.name }}</text></br>
				<text class="title">工种：{{ workerInfo.type }}</text></br>
				<text class="title" @click="makePhone(workerInfo.phone)">电话：{{ workerInfo.phone }}</text>
			</view>
			<view class="imgInfo">
				<image class="logo" :src="'http://localhost:7001/'+ workerInfo.imgUrl"></image>
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
				<view class="opeBtn" @click="showAdvance">
					<image class="opeImg" src="/static/worker/yuzhi.png"></image>
				</view>
				<text class="optText">预支</text>
			</view>
			<view class="opeBtnText">
				<view class="opeBtn" @click="showBill">
					<image class="opeImg" src="/static/worker/hesuan.png"></image>
				</view>
				<text class="optText">结算</text>
			</view>
		</view>
		<view class="workerRecord ">
			<view class="tabs">
				<view class="tab_item" :class="{cur:index === tabCur}" v-for="(item, index) in ['打卡记录', '账单记录' ]" :key="index" @click="tabSelect(index)">{{ item }}</view>
			</view>
			<view class="clockInRecord" v-if="showClockIn">
				<p v-for="(item,index) in clockDate" :key="index">{{ item.date }} {{ item.time ? item.time : '00:00:00' }} 已签到</p>
			</view>
			<view class="clockInRecord" v-else>
				<p v-for="(item,index) in advanceDate" :key="index">{{ item.stringDate }}  {{ item.type === 'bill' ? '已结算' : '已预支' }}  {{ item.money }} 元</p>
			</view>
		</view>
		<uni-popup ref="advance" :mask-click="false" background-color="#fff">
		    <uni-popup-dialog title="预支金额" mode="input" type="warning" :before-close="true" @close="closeAdvance" @confirm="advance" placeholder="请输入预支金额"></uni-popup-dialog>
		</uni-popup>
		<uni-calendar 
		    ref="calendar"
			:lunar="false" 
		    :insert="false"
			:selected="clockDate"
		/>
	</view>
</template>

<script>
export default {
	components: {},
	data(){
		return{
			workerId: 0,
			tabCur: 0,
			workerInfo: {},
			clockDate: [],
			advanceDate: [],
			showClockIn: true,
			page: 0
		}
	},
	onLoad(option) { 
		this.workerId = option.id
	    this.getWorkerInfo(option.id)
	},
	onReachBottom(){
		if(this.showClockIn){
			++ this.page
			uni.request({
				url: 'http://localhost:7001/clockIn/' + this.workerId + '/20/' + this.page , 
				method: 'GET',
				success: (res) => {
				   this.clockDate = this.clockDate.concat(res.data.data)
				   this.clockDate.map(element =>{
					   element['info'] = '签到'
				   })
				}
			});
		}
	},
	methods:{
		getWorkerInfo(id){
			var that = this
			uni.request({
			    url: 'http://localhost:7001/worker/' + id, 
				method: 'GET',
			    success: (res) => {
			       that.workerInfo = res.data.data[0]
			    }
			});
			uni.request({
				url: 'http://localhost:7001/clockIn/' + this.workerId + '/20/' + this.page , 
				method: 'GET',
				success: (res) => {
				   this.clockDate = res.data.data
				   this.clockDate.map(element =>{
					   element['info'] = '签到'
				   })
				}
			});
			uni.request({
				url: 'http://localhost:7001/advance/' + this.workerId, 
				method: 'GET',
				success: (res) => {
				   this.advanceDate = res.data.data
				   this.advanceDate.forEach(element=>{
					   const date = new Date(element.date)
					   const year = date.getFullYear()
					   const month = date.getMonth() < 10 ? '0' + date.getMonth() : date.getMonth()
					   const day = date.getDate() < 10 ? '0' + date.getDate() : date.getDate()
					   element.stringDate = year + '-' + month + '-' + day
				   })
				}
			});
		},
		showAdvance(){
			this.$refs.advance.open('center')
		},
		closeAdvance(){
			this.$refs.advance.close()
		},
		showBill(){
			uni.navigateTo({
				url: '/pages/worker/workerBill?id=' + this.workerId,
			});
		},
		advance(value){
			if(!value) return
			const params = {
				workerId: this.workerId,
				money: value
			}
			uni.request({
			    url: 'http://localhost:7001/advance', 
				method: 'POST',
				data: params,
			    success: (res) => {
				   this.$refs.advance.close()
			    }
			});
		},
		makePhone(number){
			uni.makePhoneCall({
			    phoneNumber: number 
			});
		},
		showCalendar(){
			this.$refs.calendar.open();
		},
		tabSelect(index) {
			if (this.tabCur === index) return
			this.tabCur = index
			this.tabCur === 0 ? this.showClockIn = true : this.showClockIn = false
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
		background-image: url(../../static/bj.png);
		background-size: 100%;
		box-shadow: 0px 15px 10px -15px #000;;
		.workerInfo{
			float: left;
			padding-left: 30px;
			padding-top: 20px;
			color:#555555;
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
		.tabs {
			position: absolute;
			top: 0;
			margin-left: 10px;
			text-align: center;
			display: inline-flex;
			justify-content: space-evenly;
			font-size: 32rpx;
			background-color: #FFF;
			color: #A9A9A9;
		}
	
		.tab_item {
			width: 90px;
			height: 114rpx;
			line-height: 100rpx;
			
			&.cur {
				position: relative;
				color: #007AFF;
				
				&::after {
					content: '';
					width: 70rpx;
					height: 6rpx;
					position: absolute;
					bottom: 20rpx;
					left: 50%;
					margin-left: -36rpx;
					background-color: #007AFF;
				}
			}
		}
		
		.clockInRecord{
			position: absolute;
			margin: 0 auto;
			top: 60px;
			right: 0;
			left: 0;
			width: 84%;
			overflow-y: auto;
			height: calc(100% - 60px);
			p {
				line-height: 24px;
				color: #555555;
			}
		}
	}
}
</style>
