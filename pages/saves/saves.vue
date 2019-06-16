<template>
	<view class="page">
		<scroll-view scroll-y="true" class="full">
			<view class="header vertical-end">
				<image mode="aspectFit" class="index-logo" src="../../static/images/main/big.png"/>
			</view>
			<view v-if="levels.length !== 0" class="button-list-holder vertical-center">
				<view v-for="(item, index) in levels" v-bind:key="index">
					<button class="block-button-big" hover-class="block-button__hover" hover-stay-time="500" @tap="useSave(index)">
						<view class="horizontal-between full-width">
							<view class="block-button-text-big title-line">{{item.title}}</view>
						</view>
						<view class="horizontal-between full-width">
							<view class="block-button-text-big">最佳步数</view>
							<view v-if="item.step" class="block-button-text-big">{{item.step}} 步</view>
							<view v-else class="block-button-text-big">尚无记录</view>
						</view>
						<view class="horizontal-between full-width">
							<view class="block-button-text-big">最短时间</view>
							<view v-if="item.step" class="block-button-text-big">{{item.time}} 秒</view>
							<view v-else class="block-button-text-big">尚无记录</view>
						</view>
						<view class="horizontal-between full-width">
							<view class="block-button-text-big">最高得分</view>
							<view v-if="item.score" class="block-button-text-big-big">{{item.score}}</view>
							<view v-else class="block-button-text-big">尚无记录</view>
						</view>
					</button>
				</view>
			</view>
		</scroll-view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				saves: null,
				index: null,
				levels:null
			}
		},
		onLoad() {
			let that = this
			let levels = require('../../static/data/levels.js').levels
			let progress = uni.getStorageSync("progress")
			if (!progress) {
				progress = []
			}
			for (let i = 0; i < progress.length; i++) {
				levels[i].step = progress[i].step
				levels[i].time = progress[i].time
				levels[i].score = progress[i].score
				levels[i].title = progress[i].title
				levels[i].enabled = true
			}
			levels[progress.length].enabled = true
			that.levels = levels
		},
		methods: {
		}
	}
</script>

<style>
	.no-save {
		font-size: 400upx;
		color: rgba(255, 255, 255, 0.5);
	}
	.no-save-text {
		font-size: 40upx;
		color: #FFFFFF;
	}
	.index-logo {
		margin-top: 40upx;
		width: 100%;
		height: 400upx;
	}
	.title-line{
		color: #440E0E;
		font-weight: bold;
	}
</style>
