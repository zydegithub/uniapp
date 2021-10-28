<template>
	<view class="clockInContent">
		<view class="clockInList">
			<view class="uni-list">
				<checkbox-group @change="checkboxChange">
					<label class="uni-list-cell uni-list-cell-pd" v-for="item in workerList" :key="item.id">
						<view>
							<checkbox :value="item.id" :checked="item.checked" />{{ item.name}}
						</view>
					</label>
				</checkbox-group>
			</view>
		</view>
		<uni-popup ref="noSelect" type="message" >
			 <uni-popup-message type="error" message="请勾选至少一个职工!"></uni-popup-message>
		</uni-popup>
		<uni-popup ref="noSelectDate" type="message" >
			 <uni-popup-message type="error" message="请选择补签时间!"></uni-popup-message>
		</uni-popup>
		<uni-popup ref="clockIn" :mask-click="false" background-color="#fff">
		    <uni-popup-dialog title="签 到" mode="base" type="warning" :before-close="true" @close="closeClockIn" @confirm="clockIn"></uni-popup-dialog>
		</uni-popup>
		<uni-popup ref="repairClockIn" :mask-click="false" background-color="#fff">
		    <uni-popup-dialog title="签 到" mode="base" type="warning" :before-close="true" @close="closeRepairClockIn" @confirm="repairClockIn">
				日期：<uni-datetime-picker v-model="repairClockInDate" />
			</uni-popup-dialog>
		</uni-popup>
		<button type="primary" class="leftClockInBtn" @click="sureRepairclockIn">补     签</button>
		<button type="primary" class="rightClockInBtn" @click="sureClockIn">签     到</button>
	</view>
</template>

<script>
export default {
	data() {
		return {
			workerList: [],
			checkedIdArr: [],
			repairClockInDate: ''
		}
	},
	onShow(){
		this.getWorkerList()
	},
	methods: {
		getWorkerList(){
			uni.request({
			    url: 'http://localhost:7001/worker', 
				method: 'GET',
			    success: (res) => {
			       this.workerList = res.data.data
				   this.workerList.map(element=>{
					   element.id = element.id.toString()
					   this.$set(element, 'checked', false)
				   })
			    }
			});
		},
		clockIn(){
			const params = {
				workerIdArr: this.checkedIdArr
			};
			var that =this
			uni.request({
			    url: 'http://localhost:7001/clockIn', 
				method: 'POST',
				data: params,
			    success: (res) => {
					this.$refs.clockIn.close()
					uni.showToast({
					    title: "签到成功" 
					})
					that.workerList.forEach(element=>{
						this.$set(element, 'checked', false)
					})
					this.checkedIdArr = []
			    }
			});
		},
		repairClockIn(){
			if(!this.repairClockInDate){
				this.$refs.noSelectDate.open('center')
				return
			}
			const dateArr = this.repairClockInDate.split(' ')
			const params = {
				workerIdArr: this.checkedIdArr,
				date: dateArr[0],
				time: dateArr[1]
			};
			uni.request({
			    url: 'http://localhost:7001/clockIn', 
				method: 'POST',
				data: params,
			    success: (res) => {
					this.$refs.repairClockIn.close()
					uni.showToast({
					    title: "补签成功" 
					})
					this.workerList.forEach(element=>{
						this.$set(element, 'checked', false)
					})
					this.checkedIdArr = []
			    }
			});
		},
		closeClockIn(){
			this.$refs.clockIn.close()
		},
		closeRepairClockIn(){
			this.$refs.repairClockIn.close()
		},
		sureClockIn(){
			if(this.checkedIdArr.length === 0){
				this.$refs.noSelect.open('center')
				return
			}
			this.$refs.clockIn.open('center')
		},
		sureRepairclockIn(){
			if(this.checkedIdArr.length === 0){
				this.$refs.noSelect.open('center')
				return
			}
			this.$refs.repairClockIn.open('center')
		},
		checkboxChange(e) {
			this.checkedIdArr = e.detail.value
			this.workerList.forEach(element=>{
				if(this.checkedIdArr.includes(element.id)){
					this.$set(element,'checked',true)
				}else{
					this.$set(element,'checked',false)
				}
			})
		}
	}
}
</script>

<style>
	.clockInContent{
		position: absolute;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
	}
	.clockInList{
		position: absolute;
		top: 0;
		bottom: 60px;
		left: 0;
		right: 0;
		overflow-y: auto;
	}
	.leftClockInBtn{
		position:absolute ;
		width: 46%;
		bottom: 10px;
		left: 10px;
		margin: 0 auto;
	}
	.rightClockInBtn{
		position:absolute;
		width: 46%;
		bottom: 10px;
		right: 10px;
		margin: 0 auto;
	}
	uni-button{
		/* border-radius: 0px; */
	}
	/* 列表 */
	.uni-list {
		background-color: #FFFFFF;
		position: relative;
		width: 100%;
		display: flex;
		flex-direction: column;
	}
	.uni-list:after {
		position: absolute;
		z-index: 10;
		right: 0;
		bottom: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.uni-list::before {
		position: absolute;
		z-index: 10;
		right: 0;
		top: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.uni-list-cell {
		position: relative;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
	}
	.uni-list-cell-hover {
		background-color: #eee;
	}
	.uni-list-cell-pd {
		padding: 22rpx 30rpx;
	}
	.uni-list-cell-left {
	    white-space: nowrap;
		font-size:28rpx;
		padding: 0 30rpx;
	}
	.uni-list-cell-db,
	.uni-list-cell-right {
		flex: 1;
	}
	.uni-list-cell::after {
		position: absolute;
		z-index: 3;
		right: 0;
		bottom: 0;
		left: 30rpx;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.uni-list .uni-list-cell:last-child::after {
		height: 0rpx;
	}
	.uni-list-cell-last.uni-list-cell::after {
		height: 0rpx;
	}
	.uni-list-cell-divider {
		position: relative;
		display: flex;
		color: #999;
		background-color: #f7f7f7;
		padding:15rpx 20rpx;
	}
	.uni-list-cell-divider::before {
		position: absolute;
		right: 0;
		top: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.uni-list-cell-divider::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 0rpx;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}
	.uni-list-cell-navigate {
		font-size:30rpx;
		padding: 22rpx 30rpx;
		line-height: 48rpx;
		position: relative;
		display: flex;
		box-sizing: border-box;
		width: 100%;
		flex: 1;
		justify-content: space-between;
		align-items: center;
	}
	.uni-list-cell-navigate {
		padding-right: 36rpx;
	}
	.uni-navigate-badge {
		padding-right: 50rpx;
	}
	.uni-list-cell-navigate.uni-navigate-right:after {
		font-family: uniicons;
		content: '\e583';
		position: absolute;
		right: 24rpx;
		top: 50%;
		color: #bbb;
		-webkit-transform: translateY(-50%);
		transform: translateY(-50%);
	}
	.uni-list-cell-navigate.uni-navigate-bottom:after {
		font-family: uniicons;
		content: '\e581';
		position: absolute;
		right: 24rpx;
		top: 50%;
		color: #bbb;
		-webkit-transform: translateY(-50%);
		transform: translateY(-50%);
	}
	.uni-list-cell-navigate.uni-navigate-bottom.uni-active::after {
		font-family: uniicons;
		content: '\e580';
		position: absolute;
		right: 24rpx;
		top: 50%;
		color: #bbb;
		-webkit-transform: translateY(-50%);
		transform: translateY(-50%);
	}
	.uni-collapse.uni-list-cell {
		flex-direction: column;
	}
	.uni-list-cell-navigate.uni-active {
		background: #eee;
	}
	.uni-list.uni-collapse {
		box-sizing: border-box;
		height: 0;
		overflow: hidden;
	}
	.uni-collapse .uni-list-cell {
		padding-left: 20rpx;
	}
	.uni-collapse .uni-list-cell::after {
		left: 52rpx;
	}
	.uni-list.uni-active {
		height: auto;
	}

</style>
