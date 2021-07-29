<template>
	<view class="qrcode">
		<canvas id="qrcode" canvas-id="qrcode" :style="{'width': `${options.size}px`, 'height': `${options.size}px`}" />
	</view>
</template>

<script>
	import qrcode from './qrcode'
	export default {
		name: 'qrcode',
		data() {
			return {
				options: {
					canvasId: 'qrcode',
					size: 354,
					margin: 10,
					text: ''
				}
			}
		},
		methods: {
			make(options) {
				return qrcode.make(Object.assign(this.options, options), this)
			}
		}
	}
</script>