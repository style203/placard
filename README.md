
## 项目介绍

canvas绘制海报、二维码并分享，可以预览保存并分享海报。
海报下方二维码旁边标题比较长，做了自动换行处理。

**github 地址** [https://github.com/style203/placard](https://github.com/style203/placard)

## 安装方式

插件在 HBuilderX 3.1.22 开发，只需将本组件导入项目或者下载示例项目。

## 基本用法

```html
<template>
	<view>
		<view class="button">
			<button :disabled="canvasImages == '' ? false : true" type="primary" @click="createsShareImage">生成海报</button>
			<button :disabled="canvasImages != '' ? false : true" @click="previewHandle">预览海报</button>
			<!-- #ifdef MP-WEIXIN -->
				<button type="warn" :disabled="canvasImages != '' ? false : true" open-type="share">分享给朋友</button>
			<!-- #endif -->
		</view>
		<shareImages ref="canvas" :canvasWidth="canvasWidth" :canvasHeight="canvasHeight" :shareTitle="shareTitle" :goodsTitle="goodsTitle" :shareImage="shareImage" :qrSize="qrSize" :qrUrl="qrUrl" @success="shareSuccess"></shareImages>
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
			canvasWidth:375,	// 宽度
			canvasHeight:500,	// 高度
			shareTitle:'我是这张图片的标题',		// 分享标题
			goodsTitle:'我是这张图片的标题我是这张图片的标题我是这张图片的标',		// 商品宣传标题
			shareImage:'../../static/bg.jpg',	// 背景图片
			qrSize: 100,	// 二维码大小
			qrUrl: 'https://ext.dcloud.net.cn/plugin?id=5747',	// 生成二维码的链接
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