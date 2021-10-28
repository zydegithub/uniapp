<template>
    <view class="content">
		<uni-forms ref="form" :modelValue="formData" :rules="rules" label-align="center" :border="true">
			<uni-forms-item label="姓名" name="name">
				<uni-easyinput type="input" v-model="formData.name" placeholder="请输入姓名" />
			</uni-forms-item>
			<uni-forms-item label="电话" name="phone">
				<uni-easyinput v-model="formData.phone" type="text" placeholder="请输入电话" />
			</uni-forms-item>
			<uni-forms-item label="工种" name="type">
				<picker @change="bindPickerChange" :value="index" :range="array" >
					<uni-easyinput v-model="formData.type" type="text" placeholder="请输入工种" />
				</picker>
			</uni-forms-item>
			<uni-forms-item label="日薪" name="wages">
				<uni-easyinput v-model="formData.wages" type="number" placeholder="请输入日薪" />
			</uni-forms-item>
			<uni-forms-item label="天数" name="dateNum">
				<uni-easyinput v-model="formData.dateNum" type="number" placeholder="请输入天数" />
			</uni-forms-item>
			<uni-forms-item label="头像" name="imgUrl">
				<uni-file-picker 
				    v-model="formData.imgUrl"  
				    file-mediatype="image"
				    mode="grid"
			   	    file-extname="png,jpg"
				    :limit="1"
					@select="select"
				>选择头像</uni-file-picker>
			</uni-forms-item>
		</uni-forms>
		<button type="primary" class="sumbitWorkerBtn"  @click="submit">提交</button>
	</view>
</template>

<script>
export default {
    data() {
        return {
            // 表单数据
            formData: {
                name: '',
                phone: '',
				type: '',
				wages: '',
				dateNum: '',
				imgUrl: ''
            },
			index: 0,
			filePath: '',
			array:['木工','油工','瓦工','电工','水工','小工','其他'],
            rules: {
                name: {
                    rules: [{
						required: true,
						errorMessage: '请输入姓名',
					},
					{
						minLength: 2,
						maxLength: 3,
						errorMessage: '姓名长度在 {minLength} 到 {maxLength} 个字符',
					}]
                },
                phone: {
                    rules: [{
                        format: 'string',
                        errorMessage: '请输入电话',
                    }]
                },
                wages: {
                    rules: [{
						required: true,
                        format: 'number',
                        errorMessage: '请输入日薪',
                    }]
                },
                dataNum: {
                    rules: [{
						required: true,
                        format: 'number',
                        errorMessage: '请输入天数',
                    }]
                }
            }
        }
    },
    methods: {
		bindPickerChange: function(e) {
			this.formData.type = this.array[e.target.value]
		},
        submit() {
            this.$refs.form.validate().then(res=>{
				if(this.filePath){
					uni.uploadFile({
					    url: 'http://localhost:7001/worker', 
						filePath: this.filePath,
						name: 'file',
						formData: res,
					    success: (res) => {
							uni.showToast({
								title:"添加成功" 
							})
							uni.switchTab({
								url: '/pages/worker/workerList',
							});
					    }
					});
				} else {
					uni.request({
					    url: 'http://localhost:7001/worker', 
						method: 'POST',
						data: res,
					    success: (res) => {
							uni.showToast({
								title:"添加成功" 
							})
							uni.switchTab({
								url: '/pages/worker/workerList',
							});
					    }
					});
				}
            }).catch(err =>{
                console.log('表单错误信息：', err);
            })
        },
		select(e){
			this.filePath = e.tempFilePaths[0]
		}
    }
}
</script>

<style>
.content{
	/* background-color:#F5F5F5; */
}
.uni-input {
	height: 50rpx;
	padding: 15rpx 25rpx;
	line-height:50rpx;
	font-size:28rpx;
	background:#FFF;
	flex: 1;
}
.sumbitWorkerBtn{
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
