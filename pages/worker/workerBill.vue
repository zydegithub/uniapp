<template>
	<view>
		<uni-list :border="true">
			<uni-list-item  title="工作天数"  thumb-size="lg" :rightText="billList.dateNum + billList.clockInNum + ' 天'"></uni-list-item>
			<uni-list-item  title="每日薪资"  thumb-size="lg" :rightText="billList.wages + ' 元'"></uni-list-item>
			<uni-list-item  title="总共预支"  thumb-size="lg" :rightText="billList.advanceMoney ? billList.advanceMoney: 0 + ' 元'"></uni-list-item>
			<uni-list-item  title="起始日期"  thumb-size="lg" v-show="billList.minDate" :rightText="billList.minDate"></uni-list-item>
			<uni-list-item  title="结束日期"  thumb-size="lg" v-show="billList.maxDate" :rightText="billList.maxDate"></uni-list-item>
			<uni-list-item  title="合计工资"  thumb-size="lg" :rightText="sumMoney + ' 元'"></uni-list-item>
		</uni-list>
		<uni-popup ref="bill" type="message">
		    <uni-popup-dialog title="清空账单" mode="base" type="error" :before-close="true" @close="closeClearBill" @confirm="sureClearBill" >
				<!-- <p>确认清空后，清空之前打卡预支记录。</p> -->
			</uni-popup-dialog>
		</uni-popup>
		<button type="primary" class="clearBillBtn" @click="clearBill">已结清空</button>
	</view>
</template>

<script>
	export default {
		components: {},
		data() {
			return {
				billList: {},
				workerId: [],
				sumMoney: 0
			}
		},
		onLoad(option) { 
			this.workerId = option.id
			this.getWorkerBill()
		},
		methods:{
			getWorkerBill(){
				uni.request({
				    url: 'http://localhost:7001/bill/' + this.workerId, 
					method: 'GET',
				    success: (res) => {
				       this.billList = res.data.data[0]
					   this.sumMoney = Number(this.billList.dateNum + this.billList.clockInNum) * Number(this.billList.wages) - Number(this.billList.advanceMoney)
				    }
				});
			},
			sureClearBill(){
				const params = {
					workerId: this.workerId,
					money: this.sumMoney
				}
				uni.request({
				    url: 'http://localhost:7001/bill',
					method: 'POST',
					data: params,
				    success: (res) => {
						this.getWorkerBill()
						this.closeClearBill()
						uni.showToast({
						    title: "清空成功" 
						})
				    }
				});
			},
			clearBill(){
				this.$refs.bill.open('center')
			},
			closeClearBill(){
				this.$refs.bill.close()
			}
		}
	}
</script>

<style>
	.clearBillBtn{
		position:absolute ;
		bottom: 0px;
		right: 0px;
		left: 0px;
		margin: 0 auto;
	}
</style>
