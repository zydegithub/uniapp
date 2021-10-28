<template>
	<view class="record">
		<uni-search-bar placeholder="搜索日志" @confirm="search" @input="input" ></uni-search-bar>
		<view class="editor">
			<editor placeholder="" id="editor" class="ql-container" @input="editorChange" @ready="onEditorReady"></editor>
			<button type="primary" class="sendBtn" size="mini">发送</button>
		</view>
	</view>
</template>

<script>
	export default {
		data(){
			return{
				
			}
		},
		methods:{
			onEditorReady() {
				// #ifdef MP-BAIDU
				this.editorCtx = requireDynamicLib('editorLib').createEditorContext('editorId');
				// #endif

				// #ifdef APP-PLUS || H5 ||MP-WEIXIN
				uni.createSelectorQuery().select('#editor').context((res) => {
				  this.editorCtx = res.context
				}).exec()
				// #endif
			},
			undo() {
				this.editorCtx.undo()
			},
			editorChange(e){
				// this.gongsijieshao = e.detail.html
			}
		},
	}
</script>

<style>
	.record{
		position: absolute;
		top: 0;
		bottom: 0;
		right: 0;
		left: 0;
		background-color: #FAFAFA;
	}
	.editor{
		position: relative;
		border: 2px solid #E8E8E8;
		border-radius: 8px;
		margin: 8px;
		width: calc(100% - 20px);
		background-color: #FFFFFF;
	}
	.sendBtn{
		position: absolute;
		right: 10px;
		bottom: 10px;
		width: 66px;
	}
	.ql-container{
		height: 140px !important;
		min-height: 140px !important;
	}
</style>
