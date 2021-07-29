<template>
	<view>
		<view class="button">
			<button :disabled="canvasImages == '' ? false : true" type="primary" @click="createsShareImage">生成海报</button>
			<button :disabled="canvasImages != '' ? false : true" @click="previewHandle">预览海报</button>
			<button type="warn" :disabled="canvasImages != '' ? false : true" open-type="share">分享给朋友</button>
		</view>
		<shareImages ref="canvas" @success="shareSuccess"></shareImages>
	</view>
</template>

<script>
	import shareImages from '../../components/hj-placard/hj-shareImages.vue'
	export default {
		components:{
			shareImages
		},
		data() {
			return {
				canvasImages:'',
			}
		},
		methods: {
			// 生成分享图片
			createsShareImage(){
				// console.log(this.$refs.canvas)
				this.$refs.canvas.canvasCreate();
			},
			// 预览图片
			previewHandle(){
				uni.previewImage({
					urls: [this.canvasImages],
				});
			},
			// 回调图片地址
			shareSuccess(e){
				// console.log('地址',e)
				this.canvasImages = e
			},
			// 分享
			onShareAppMessage(res) {
				// if (res.from === 'button') {
				// 	console.log(res.target)
				// }
				return {
					title: 'canvas图片分享',
					path: this.canvasImages
				}
			}
		}
	}
</script>

<style scoped>
.button{
	display: flex;
}
</style>