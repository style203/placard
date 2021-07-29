<template>
	<view style="position: fixed;z-index: -9999;">
		<canvas :canvas-id="canvasID" :style="{width:canvasWidth+'px',height:canvasHeight+'px'}"></canvas>
		<view v-if="qr_code == ''">
			<QRCode ref="qrcode" />
		</view>
	</view>
</template>

<script>
	import QRCode from "../qr_code/qrcode.vue"
	var _this;
	export default {
		name: 'canvas-images',
		props: {
			// canvasID 等同于 canvas-id
			canvasID: {
				Type: String,
				default: 'shareCanvas'
			},
		},
		components:{
			QRCode
		},
		data() {
			return {
				canvasWidth:375,	// 宽度
				canvasHeight:500,	// 高度
				bgImg:'../../static/bg.jpg',	// 背景图片
				qr_code:'',		// 二维码
				qr_size: 100,	// 二维码大小
				qr_url: 'https://www.baidu.com/',	// 生成二维码的链接
			}
		},
		mounted(){
			_this = this;
		},
		methods: {
			// 创建二维码
			canvasCreate(){
				_this.$refs.qrcode.make({size: _this.qr_size,text: _this.qr_url})
					.then(res => {
						// 返回的res与uni.canvasToTempFilePath返回一致
						// console.log(res)
						_this.qr_code = res.tempFilePath;
						_this.onCanvas();
					});
			},
			// 画图
			async onCanvas() {
				uni.showLoading({
					title: "分享图片生成中..."
				});
				const ctx = uni.createCanvasContext(_this.canvasID, _this);
				// 设置 canvas 背景色
				ctx.setFillStyle('#FFFFFF');
				ctx.fillRect(0, 0, _this.canvasWidth, _this.canvasHeight);
				ctx.setFillStyle('#000000');
				// 背景图片
				ctx.drawImage(_this.bgImg,50,50);
				ctx.setFontSize(18);
				ctx.setTextAlign('center');
				ctx.fillText('我是这张图片的标题', _this.canvasWidth/2, 30);
				// 左边标题
				ctx.setTextAlign('left')
				ctx.setFontSize(16)
				_this.writeTextOnCanvas(ctx,30,21,'我是这张图片的标题我是这张图片的标题我是这张图片的标',50,350);
				// 设置虚线
				ctx.setStrokeStyle('#333333');
				ctx.setLineDash([5, 10], 2);
				ctx.beginPath();
				ctx.moveTo(220,340);
				ctx.lineTo(220, 420);
				ctx.stroke();
				// 二维码
				ctx.drawImage(_this.qr_code,225,330,100,100);
				// ctx.draw();
				
				// 延迟后渲染至canvas上
				let pic = await _this.setTime(ctx)
				_this.$emit('success', pic);
			},
			/**
			 * @param {Object} ctx_2d	getContext("2d") 对象
			 * @param {int} lineheight	段落文本行高
			 * @param {int} bytelength	设置单字节文字一行内的数量
			 * @param {string} text		写入画面的段落文本
			 * @param {int} startleft	开始绘制文本的 x 坐标位置（相对于画布）
			 * @param {int} starttop	开始绘制文本的 y 坐标位置（相对于画布）
			 */
			writeTextOnCanvas(ctx_2d, lineheight, bytelength, text ,startleft, starttop){
				// 获取字符串的真实长度（字节长度）
				function getTrueLength(str){
					var len = str.length, truelen = 0;
					for(var x = 0; x < len; x++){
						if(str.charCodeAt(x) > 128){
							truelen += 2;
						}else{
							truelen += 1;
						}
					}
					return truelen;
				}
				// 按字节长度截取字符串，返回substr截取位置
				function cutString(str, leng){
					var len = str.length, tlen = len, nlen = 0;
					for(var x = 0; x < len; x++){
						if(str.charCodeAt(x) > 128){
							if(nlen + 2 < leng){
								nlen += 2;
							}else{
								tlen = x;
								break;
							}
						}else{
							if(nlen + 1 < leng){
								nlen += 1;
							}else{
								tlen = x;
								break;
							}
						}
					}
					return tlen;
				}
				for(var i = 1; getTrueLength(text) > 0; i++){
					var tl = cutString(text, bytelength);
					ctx_2d.fillText(text.substr(0, tl).replace(/^\s+|\s+$/, ""), startleft, (i-1) * lineheight + starttop);
					text = text.substr(tl);
				}
			},
			// 彻底改成同步 防止拿到的图片地址为空
			setTime(ctx) {
				return new Promise((resole, err) => {
					setTimeout(() => {
						ctx.draw(false, async () => {
							let pic = await _this.getNewPic();
							resole(pic)
						});
					}, 600)
				})
			},
			// 获取新的图片地址
			getNewPic() {
				return new Promise((resolve, errs) => {
					setTimeout(()=>{
						uni.canvasToTempFilePath({
							canvasId: _this.canvasID,
							quality: 1,
							complete: (res) => {
								// 在H5平台下，tempFilePath 为 base64
								// 关闭showLoading
								uni.hideLoading();
								//  储存海报地址  也是分享的地址
								resolve(res.tempFilePath)
							}
						}, _this);
					},200)
				})
			},
		},
		mounted() {
			_this = this;
		}
	}
</script>