
## 项目介绍

canvas绘制海报、二维码并分享，可以预览保存并分享海报。
海报下方二维码旁边标题比较长，做了自动换行处理。

## 安装方式

插件在 HBuilderX 3.1.22 开发，只需将本组件导入项目或者下载示例项目。

## 基本用法

```vue
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
```

```js
import shareImages from '../../components/hj-placard/shareImages.vue'
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
```

