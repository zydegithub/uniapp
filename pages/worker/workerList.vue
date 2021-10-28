<template>
	<view>
		<uni-list :border="true">
			<uni-list-item v-for="(item,index) in workerList" :title="item.name" @click="routeToWorker(item.id)" :note="item.type" :thumb="'http://localhost:7001/'+item.imgUrl" thumb-size="lg"  link></uni-list-item>
		</uni-list>
		<button type="primary" class="addWorkerBtn" @click="addWorker">添加人员</button>
	</view>
</template>

<script>
export default {
	components: {},
	data() {
		return {
			workerList: []
		}
	},
	onShow(){
		this.getWorkerList()
	},
	methods:{
		routeToWorker(id){
			uni.navigateTo({
				url: '/pages/worker/worker?id=' + id,
			});
		},
		getWorkerList(){
			uni.request({
			    url: 'http://localhost:7001/worker', 
				method: 'GET',
			    success: (res) => {
			       this.workerList = res.data.data
			    }
			});
		},
		addWorker(){
			uni.navigateTo({
				url: '/pages/worker/addWorker',
			});
		}
	}
}
</script>

<style>
.chat-custom-right {
	flex: 1;
	/* #ifndef APP-NVUE */
	display: flex;
	/* #endif */
	flex-direction: column;
	justify-content: space-between;
	align-items: flex-end;
}

.chat-custom-text {
	font-size: 12px;
	color: #999;
}

.addWorkerBtn{
	position:absolute ;
	bottom: 0px;
	right: 0px;
	left: 0px;
	margin: 0 auto;
}
uni-button{
	border-radius: 0px;
}
</style>
