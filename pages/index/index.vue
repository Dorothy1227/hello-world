<template>
	<view class="page">
		<view class="title-line">
			<view >
				<image mode="aspectFit" class="shop-logo" @click="exit" src="../../static/images/main/que.png"/>		
			</view>
			<!--view @click="turnShop">
				<image mode="aspectFit" class="shop-logo" src="../../static/images/main/shop-fill.png"/>		
			</view-->
		</view>
		<view class="vertical-center full">
			<image mode="aspectFit" class="index-logo" src="../../static/images/main/big.png"/>
			<view class="index-button-holder">
				<image mode="aspectFit" class="new-game" @click="chooseLevel" src="../../static/images/main/new.png"/>
				<!--
				<button class="bar-button white-button full-width" hover-class="white-button__hover" hover-stay-time="500" @click="chooseLevel">选择关卡</button>
				<button class="bar-button white-button full-width" hover-class="white-button__hover" hover-stay-time="500" @click="loadSaves">读取存档</button>
				<button class="bar-button white-button full-width" hover-class="white-button__hover" hover-stay-time="500" ">前往商城</button>
				<button class="bar-button white-button full-width" hover-class="white-button__hover" hover-stay-time="500" @click="openMenu">打开菜单</button>
				<button class="bar-button white-button full-width" hover-class="white-button__hover" hover-stay-time="500" @click="exit">退出游戏</button>-->
				<image mode="aspectFit" class="new-game" @click="loadSaves" src="../../static/images/main/old.png"/>
			</view>
		</view>
		<!--view v-bind:class="{ 'show': confirmation !== null , 'hidden': confirmation === null, 'mask': true }">
			<view v-bind:class="{ 'drawer-show': drawer ,'drawer': true }"></view>
			<view v-bind:class="{ 'show': confirmation, 'hidden': !confirmation, 'panel': true }">
				<view class="panel-title">操作确认</view>
				<view class="panel-text text-center">是否确定退出游戏？</view>
				<view class="full-width horizontal-between">
					<button class="panel-button panel-button-half" hover-class="panel-button__hover" @click="quit">是</button>
					<button class="panel-button panel-button-half" hover-class="panel-button__hover" @click="closeConfirmation">否</button>
				</view>
			</view>
		</view-->
	</view>
</template>

<script>
	export default {
		data() {
			return {
			}
		},
		onLoad() {
			let that = this
		},
		methods: {
			chooseLevel: function() {
				uni.navigateTo({
					animationType: "fade-in",
					url: "../levels/levels"
				})
			},
			loadSaves: function() {
				uni.navigateTo({
					animationType: "fade-in",
					url: "../saves/saves"
				})
			},
			turnShop: function() {
				console.log("shop is ok")
				uni.navigateTo({
					animationType: "fade-in",
					url: "../shop/shop"
				})
			},
			exit: function() {
				uni.showModal({
					title: '新手提示',
					content: '本游戏基于华容道改编，将杰瑞送回老鼠洞即可过关.',
					success: function (res) {
						if (res.confirm) {
							console.log('用户点击确定')
						} else if (res.cancel) {
							console.log('用户点击取消')
						}
					}
				})
			},
			closeConfirmation: function() {
				this.confirmation = null
			},
		}
	}
</script>

<style>	
	.story-holder {
		z-index: 10;
		position: fixed;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		width: 100%;
		height: 100%;
		background: linear-gradient(45deg, #ec008c, #6739b6);
	}
	
	.story {
		padding-left: 75upx;
		padding-right: 75upx;
		box-sizing: border-box;
		margin-bottom: 60upx;
	}
	
	.story-title {
		font-size: 50upx;
		color: #FFFFFF;
	}
	
	.story-text {
		font-size: 40upx;
		color: #FFFFFF;
		margin-top: 40upx;
		margin-bottom: 40upx;
	}
	
	.hint {
		height: 200upx;
		color: #FFFFFF;
		font-size: 100upx;
	}
	
	.hint-avatar {
		height: 200upx;
		width: 200upx;
		border-radius: 40upx;
	}
	
	.hint-image {
		height: 200upx;
		width: 162upx;
	}
	.index-logo {
		width: 100%;
		height: 500upx;
	}

	.index-title {
		width: 100%;
		height: 100upx;
		margin-top: 80upx;
		margin-bottom: 80upx;
	}
	
	.index-button-holder {
		margin-top:200upx;
		width: 80%;
		height: 300upx;
		display: flex;
		flex-direction: column;
		justify-content: space-around;
	}
	.new-game{
		height:120upx;
		width:100%;
	}
	.title-line{
		width:95%;
		height:60upx;
		margin-top:5upx;
		display: flex;
		flex-direction: row;
		justify-content: flex-end;
	}
	.shop-logo{
		width:60upx;
		margin-right: 20upx;
	}
</style>
