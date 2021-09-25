<template>
	<uni-list>
	    <uni-list :border="true">
			<uni-list-item v-for="(item,index) in workerList" :title="item.name" :note="item.workType" to="/pages/worker/worker"  :thumb="item.imgUrl" thumb-size="lg" rightText="详情>"></uni-list-item>
	    </uni-list>
	</uni-list>
</template>

<script>
export default {
	components: {},
	data() {
		return {
			workerList: [{
				name:'张三',
				workType:'木工',
				imgUrl:'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png'
			}, {
				name:'李四',
				workType:'油工',
				imgUrl:'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png'
			}, {
				name:'王五',
				workType:'瓦工',
				imgUrl:'https://vkceyugu.cdn.bspapp.com/VKCEYUGU-dc-site/460d46d0-4fcc-11eb-8ff1-d5dcf8779628.png'
			}]
		}
	},
	methods:{
		routeToWorker(){
			uni.navigateTo({
				url: '/pages/worker/worker',
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
</style>
