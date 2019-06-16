<template>
	<view class="page">
		<scroll-view scroll-y="true" class="full">
			<view class="level-header">
				<image mode="aspectFit" class="level-title" @click="loadSaves" src="../../static/images/main/choose.png"/>
			</view>
			<view class="horizontal-center button-list-holder level">
				<view v-for="(item, index) in levels" v-bind:key="index">
					<button class="block-button" hover-class="block-button__hover" hover-stay-time="500" v-bind:disabled="!item.enabled" @click="startGame(index)">
						<view class="horizontal-between full" >
							<view class="vertical-between full-width" >
								<image class="level-image" mode="aspectFit" v-bind:src="item.titleimg"/>
								<view v-if="item.enabled" class="block-button-text star">
									<view v-if="item.score >= 1800">
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
									</view>
									<view v-else-if="item.score >= 1600">
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favor"></text>
									</view>
									<view v-else-if="item.score >= 1400">
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
									</view>
									<view v-else-if="item.score >= 1200">
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
									</view>
									<view v-else-if="item.score >= 1000">
										<text class="cuIcon-favorfill"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
									</view>
									<view v-else>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
										<text class="cuIcon-favor"></text>
									</view>
								</view>
								<view v-else class="block-button-text"><text class="cuIcon-lock"></text></view>
							</view>
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
				levels: null
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
				levels[i].enabled = true
			}
			levels[progress.length].enabled = true
			that.levels = levels
		},
		methods: {
			startGame: function(level) {
				uni.navigateTo({
					animationType: "fade-in",
					url: "../game/game?level=" + level
				})
			},
			backlevel: function() {
				uni.navigateTo({
					animationType: "fade-in",
					url: "../index/index"
				})
			}
		}
	}
</script>

<style>
	.star {
		color: #79482C;
	}
	.title-line{
		margin-top: 90upx;
		margin-left:20upx;
		color:#FFFFFF;
	}
	.level{
		flex-wrap:wrap;
	}
	.level-image{
		height:90upx;
	}
	.level-header{
		height: 270upx;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
	}
	.level-title{
		height:150upx;
	}
</style>
