<template>
	<view class="page">
		<view class="vertical-center full">
			<view class="game-info-holder">
				<view class="game-info horizontal-between">
					<view><text class="cuIcon-time">时间</text></view>
					<view>{{time}} 秒</view>
				</view>
				<view class="game-info horizontal-between">
					<view><text class="cuIcon-footprint">步数</text></view>
					<view>{{step}} 步</view>
				</view>
			</view>
			<view class="game-board vertical-end">
				<view class="piece-holder vertical-center" v-for="(item, index) in pieces" v-bind:key="index" v-bind:style="{ 'left': item.position.x * blockSize + 'px', 'top': item.position.y * blockSize + 'px', 'width': item.width * blockSize + 'px', 'height': item.height * blockSize + 'px' }">
					<view class="piece vertical-center" @touchstart="touchStart" @touchend="touchEnd" @touchmove="touchMove" v-bind:data-piece-index="index">
						<image class="piece-fill-image" mode="aspectFill" v-bind:src="item.image" />
					</view>
				</view>
				<view class="game-target"></view>
			</view>
			<view class="controller">
				<!--button class="controller-button" hover-class="controller-button__hover" hover-stay-time="500" @click="exit"-->
					<button class="controller-button vertical-center" @click="exit">
							<image mode="aspectFit" class="return-button"  src="../../static/images/main/return.png"/>	
					</button>
					<button class="controller-button vertical-center" @click="replay">
							<image mode="aspectFit" class="return-button" src="../../static/images/main/restart.png"/>
					</button>
			</view>
		</view>
		<view v-bind:class="{ 'show':confirmation !== null || saveToast !== null, 'hidden':confirmation === null && saveToast === null, 'mask': true  }">
			<view class="panel">
				<view v-if="confirmation !== null" class="panel-title">{{confirmationTitle}}</view>
				<view v-if="saveToast !== null" class="panel-title">已保存</view>
				<scroll-view v-if="confirmation !== null" scroll-y="true" class="panel-text text-justify text-indent">{{confirmation}}</scroll-view>
				<scroll-view v-if="saveToast !== null" scroll-y="true" class="panel-text text-justify text-indent">已成功保存游戏，您可以读取存档后继续本局游戏。</scroll-view>
				<view v-if="saveToast !== null" class="full-width horizontal-between">
					<button class="panel-button" hover-class="panel-button__hover" @click="closeSaveToast">确定</button>
				</view>
				<view v-if="confirmation !== null" class="full-width horizontal-between">
					<button class="panel-button panel-button-half" hover-class="panel-button__hover" @click="confirmFunction">是</button>
					<button class="panel-button panel-button-half" hover-class="panel-button__hover" @click="cancelFunction">否</button>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				//定义单元格大小
				blockSize: uni.upx2px(150),
				//控制棋子移动的变量
				moveStarted: false,
				touchStartX: null,
				touchStartY: null,
				//弹窗相关
				saveToast: null,
				confirmation: null,
				confirmationTitle: null,
				confirmFunction: null,
				cancelFunction: null,
				//控制游戏开始的遮罩显隐
				mask: true,
				//关卡相关
				level: null,
				title: null,
				pieces: null,
				step: null,
				time: null,
				score: null,
				money:null,
				//保存相关
				saved: true,
				saveId: null,
				//计时器
				startTime: null,
				timer: null,
			}
		},
		onLoad(options) {
			let that = this
			let level = JSON.parse(JSON.stringify(require('../../static/data/levels.js').levels[options.level]))
			that.level = options.level
			that.title = level.title
			if (options.save) {
				let save = JSON.parse(options.save)
				that.pieces = save.pieces
				that.step = save.step
				that.time = save.time
				that.saveId = save.id
			} else {
				that.pieces = level.pieces
				that.step = 0
				that.time = 0
			}
			that.startTiming()
		},
		onBackPress(options) {
			let that = this
			if (options.from !== "navigateBack") {
				return true
			} else {
				return false
			}
		},
		methods: {
			quitGame: function() {
				uni.navigateBack({
					animationType: "fade-out",
					delta: 2
				})
			},
			startTiming: function() {
				let that = this
				that.startTime = (new Date().getTime()) / 1000 - that.time
				that.timer = setInterval(function() {
					that.time = Math.round((new Date().getTime()) / 1000 - that.startTime)
				}, 1000)
			},
			stopTiming: function() {
				let that = this
				clearInterval(that.timer)
				that.timer = null
			},
			start: function() {
				let that = this
				that.mask = false
				that.startTiming()
			},
			replay: function() {
				let that = this
				that.confirmationTitle = "操作确认"
				let str = "您是否确定要重新开始本局游戏？"
				if (!that.saved) {
					str += "您将丢失未保存的游戏进度。"
				}
				that.confirmation = str
				that.confirmFunction = that.replayConfirm
				that.cancelFunction = that.cancel
				that.stopTiming()
			},
			replayConfirm: function() {
				uni.redirectTo({
					animationType: "fade-in",
					url: "../game/game?level=" + this.level
				})
			},
			exit: function() {
				let that = this
				that.confirmationTitle = "操作确认"
				let str = "您是否确定要退出本局游戏返回主菜单？"
				if (!that.saved) {
					str += "您将丢失未保存的游戏进度。"
				}
				that.confirmation = str
				that.confirmFunction = that.quitGame
				that.cancelFunction = that.cancel
				that.stopTiming()
			},
			cancel: function() {
				this.confirmation = null
				this.confirmationTitle = null
				this.confirmFunction = null
				this.cancelFunction = null
				this.startTiming()
			},
			clearLevel: function() {
				let that = this
				that.stopTiming()
				let score = 2000 - Math.round((that.time / 60) * that.step)
				that.score = score > 1000 ? score : 1000
				let progress = uni.getStorageSync("progress")			
				if (progress) {
					if (progress[that.level]) {
						if (that.score > progress[that.level].score) {
							let levelInfo = {
								level: that.level,
								step: that.step,
								time: that.time,
								score: that.score,
							}
							progress[that.level] = levelInfo
						}
					} else {
						let levelInfo = {
							level: that.level,
							step: that.step,
							time: that.time,
							score: that.score,
						}
						progress.push(levelInfo)
					}
				} else {
					progress = []
					let levelInfo = {
						level: that.level,
						step: that.step,
						time: that.time,
						score: that.score,
					}
					progress.push(levelInfo)
				}
				uni.setStorageSync("progress", progress)
				that.confirmationTitle = "您已通过本关"
				that.confirmation = "您在本关用时" + that.time + "秒，经过" + that.step + "步，得分为" + that.score + "分。是否继续下一关？"
				that.confirmFunction = that.nextLevel
				that.cancelFunction = that.quitGame
			},
			nextLevel: function() {
				//that.stopTiming()
				uni.redirectTo({
					animationType: "fade-in",
					url: "../game/game?level=" + String(Number(this.level) + 1)
				})
			},
			closeDescription: function() {
				this.description = null
				this.startTiming()
			},
			save: function() {
				let that = this
				let saves = uni.getStorageSync("saves")
				if (!saves) {
					saves = []
				}
				let index = saves.length
				let currentTime = new Date
				let year = String(currentTime.getFullYear())
				let month = String(currentTime.getMonth() + 1)
				let date = String(currentTime.getDate())
				let hour = String(currentTime.getHours())
				if (hour.length < 2) {
					hour = '0' + hour
				}
				let minute = String(currentTime.getMinutes())
				if (minute.length < 2) {
					minute = '0' + minute
				}
				let fullTime = year + '年' + month + '月' + date + '日 ' + hour + ":" + minute
				let save = {
					id: index,
					level: that.level,
					title: that.title,
					pieces: that.pieces,
					step: that.step,
					time: that.time,
					saveTime: fullTime
				}
				if (that.saveId !== null) {
					save.id = that.saveId
					saves[that.saveId] = save
				} else {
					saves.push(save)
				}
				uni.setStorageSync("saves", saves)
				that.saved = true
				that.saveId = save.id
				that.saveToast = true
				that.stopTiming()
			},
			closeSaveToast: function() {
				this.saveToast = null
				this.startTiming()
			},
			moveable: function(pieceIndex, axis, direction) {
				let that = this
				let points = []
				let pieces = JSON.parse(JSON.stringify(that.pieces))
				for (let i = 0; i < pieces.length; i++) {
					let x = pieces[i].position.x
					let y = pieces[i].position.y
					let width = pieces[i].width
					let height = pieces[i].height
					if (i != pieceIndex) {
						for (let j = 0; j < width; j++) {
							for (let k = 0; k < height; k++) {
								let point = {
									x: x + j,
									y: y + k
								}
								points.push(point)
							}
						}
					}
				}
				let piece = pieces[pieceIndex]
				piece.position[axis] += direction
				let x = piece.position.x
				let y = piece.position.y
				let width = piece.width
				let height = piece.height
				let result = true
				for (let i = 0; i < width; i++) {
					if ((x + i) > 3 || (x + i) < 0) {
						result = false
						break
					}
					for (let j = 0; j < height; j++) {
						if ((y + j) > 4 || (y + j) < 0) {
							result = false
							break
						}
						for (let k = 0; k < points.length; k++) {
							if ((x + i) == points[k].x && (y + j) == points[k].y) {
								result = false
								break
							}
						}
					}
				}
				return result
			},
			touchStart: function(e) {
				let that = this
				that.touchStartX = e.changedTouches[0].pageX
				that.touchStartY = e.changedTouches[0].pageY
			},
			touchMove: function() {
				this.moveStarted = true
			},
			touchEnd: function(e) {
				let that = this
				let pieceIndex = e.currentTarget.dataset.pieceIndex
				if (that.moveStarted) {
					let movedX = e.changedTouches[0].pageX - that.touchStartX
					let movedY = e.changedTouches[0].pageY - that.touchStartY
					if (Math.abs(movedX) > Math.abs(movedY)) {
						if (movedX > 0 && that.moveable(pieceIndex, 'x', 1)) {
							that.pieces[pieceIndex].position.x += 1
							that.step += 1
							that.saved = false
						} else if (that.moveable(pieceIndex, 'x', -1)) {
							that.pieces[pieceIndex].position.x -= 1
							that.step += 1
							that.saved = false
						}
					} else if (Math.abs(movedX) < Math.abs(movedY)) {
						if (movedY > 0 && that.moveable(pieceIndex, 'y', 1)) {
							that.pieces[pieceIndex].position.y += 1
							that.step += 1
							that.saved = false
						} else if (that.moveable(pieceIndex, 'y', -1)) {
							that.pieces[pieceIndex].position.y -= 1
							that.step += 1
							that.saved = false
						}
					}
					that.moveStarted = false
				} else {
					if (that.pieces[pieceIndex].name) {
						that.stopTiming()
						that.description = pieceIndex
					}
				}
				if (pieceIndex == 0) {
					if (that.pieces[pieceIndex].position.x == 1 && that.pieces[pieceIndex].position.y == 3) {
						that.clearLevel()
					}
				}
			},
		}
	}
</script>

<style>	
	.game-info-holder {
		width: 630upx;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
	}
	
	.game-info {
		width: 250upx;
		height: 40upx;
		font-size: 30upx;
		line-height: 80upx;
		padding-left: 15upx;
		padding-right: 15upx;
		color: #ffea3f;
	}
	
	.game-board {
		position: relative;
		width: 600upx;
		height: 750upx;
		margin-bottom: 50upx;
		margin-top: 10upx;
		background-color: #440e0e;
		background-clip: border-box;
		border-color: transparent;
		border-width: 15upx;
		border-style: solid;
		box-sizing: content-box;
	}
	
	.game-target {
		width: 320upx;
		height: 320upx;
		border-radius: 40rpx;
		background-image: url("../../static/images/main/target.png");		
		background-repeat:no-repeat;
        background-size:100% 100%;
        -moz-background-size:100% 100%;
	}
	
	.piece-holder {
		z-index: 1;
		position: absolute;
		transition: 0.3s;
	}
	
	.piece {
		position: absolute;
		top: 5upx;
		bottom: 5upx;
		left: 5upx;
		right: 5upx;
		font-size: 5upx;
		background-color: #9c26b0;;
		overflow: hidden;
	}
	.piece-fill-image {
		width: 100%;
		height: 100%;
	}
	
	.controller {
		width: 630upx;
		display: flex;
		flex-direction: row;
		justify-content: center;
	}
	
	.controller-button {
		width: 150upx;
		height: 150upx;
		margin: 0 40upx;
		background-color: rgba(0, 0, 0, 0);
	}
	.return-button{
		height: 90upx;	
	}
</style>
