<!-- 转盘抽奖  支持 5~9等分 -->
<template>
	<div class="draw-components">
		<div class="draw-bg">
			<!-- 奖品背景 -->
			<div :class="setbgClass" :style="isShowbg ? '' : 'transform: rotate(' + bgRotate + 'deg);transition-timing-function:'+timingFunction+';transition-duration:' + durationTime + 'ms'"></div>
			<img style="display:none" :src="setbgSrc()" />
			<!-- 奖品列表 -->
			<div class="draw-item " :class="'item-' + drawList.length" :style="'transform: rotate(' + rotate + 'deg);transition-timing-function:'+timingFunction+';transition-duration:' + durationTime + 'ms'">
				<template v-for="(itm, idx) in drawList">
					<div class="draw-content">
						<span class="title">{{ itm.awardName }}</span>
						<img class="draw-img" src="../images/draw/thanks.png" />
					</div>
				</template>
			</div>
			<div class="draw-item-btn " :class="canDraw?'':'draw-item-btn-nobg'" @click="drawClick()"><span class="draw-jifen">{{drawScore}}积分</span></div>
		</div>
	</div>
</template>

<script>
	export default{
		props:{
			drawList:{//奖项列表 5-9份
				type:Array,
				default:[],
			},
			drawScore:{//抽奖花费积分
				type:Number,
				default:0,
			},
			haveTime:{//剩余抽奖次数
				type:Number,
			},
			haveScore:{//剩余积分
				type:Number,
			}
		},
		data(){
			return{
				rotate: 0,//奖项旋转角度
				bgRotate: 0,//背景旋转角度
				durationTime: 0,//抽奖转盘时间
				isShowbg: false, //是否显示中奖背景
				isDraw: false, //抽奖是否结束
				canDraw:true,
				timingFunction:'',
			}
		},
		watch:{
			timeAndScore:{
				immediate: true,
				handler:function(val){
					console.log('监听到的',val);
					if(val.haveTime<=0||val.haveScore-this.drawScore<0){
						this.canDraw=false;
					}else{
						this.canDraw=true;
					}
				}
			
			}
		},
		computed: {
			timeAndScore(){
				return {haveTime:this.haveTime,haveScore:this.haveScore};
			},
			setbgClass() {//设置抽奖背景图片
				let id = this.drawList.length;
				return this.isShowbg ? 'draw-item-bg-' + id + ' draw-item-ok-' + id : 'draw-item-bg-' + id;
			},
			
		},
		methods:{
			setbgSrc() {//设置中奖背景图片
				return require('../images/draw/zj-' + this.drawList.length + '.png');
			},
			drawClick() {
				if (this.isDraw) {
					console.log('正在抽奖中');
					return;
				}
				this.isShowbg = false;
				this.isDraw = !this.isShowbg;
				this.$emit("draw")

			},
			drawRotate(index,isend) {
				let that = this;
				if(isend){
					that.isDraw = false;
					return ;
				}
				that.durationTime = 0;
				that.bgRotate = that.rotate;
				that.timingFunction='ease';
				let temp_durationTime = Math.floor(Math.random() * 3000 + 6000);
				// let temp_durationTime =5000;
				let temp_startRotate, temp_stopRotate;
				temp_startRotate = 360 * 8 + 360 - ((index - 1) * 360) / that.drawList.length;
				temp_stopRotate = 360 - ((index - 1) * 360) / that.drawList.length;
				setTimeout(() => {
					console.log('开始了','持续时间：',temp_durationTime,'ms 旋转角度：',temp_startRotate);
					that.rotate = that.bgRotate = temp_startRotate;
					that.durationTime = temp_durationTime;
					setTimeout(() => {
						that.timingFunction='linear';
						temp_durationTime =that.durationTime*0.6;
						console.log('减速了');
					}, that.durationTime*0.4);
					setTimeout(() => {
						that.durationTime = 0;
						that.rotate = temp_stopRotate;
						that.isShowbg = true;
						that.isDraw = !that.isShowbg;
						that.bgRotate = 0;
						console.log('结束了');
						that.$emit('lotteryResult')
					}, that.durationTime + 500);
				}, 100);
			}
		}
	}
</script>

<style scoped lang="scss">
	.draw-components {
		width: 345px;
		height: 345px;
		margin: 0 auto;
		background-image: url(../images/draw/backcircle.png);
		background-size: 345px 345px;
		position: relative;
		z-index: 1;
		.draw-bg {
			width: 345px;
			height: 345px;
			border-radius: 50%;
		}
		.draw-item {
			width: 300px;
			height: 300px;
			position: relative;
			top: -277.5px;
			margin: 0 auto;
			// transition: transform  cubic-bezier(1,1,1,1) 0ms;
			transition: transform ease 0s;
			
		}
	
		@for $i from 5 through 9 {
			.draw-item-bg-#{$i} {
				position: relative;
				top: 22.5px;
				left: 22.5px;
				width: 300px;
				height: 300px;
				background-image: url('../images/draw/zp-#{$i}.png');
				background-size: 300px 300px;
				// transition: transform  cubic-bezier(1,1,1,1) 0ms;
				transition: transform ease 0s;
			}
			.draw-item-ok-#{$i} {
				background-image: url('../images/draw/zj-#{$i}.png');
			}

			
		}
		
		@for $i from 5 through 9 {
			.item-#{$i} {
				.draw-content {
					position: absolute;
					width: 125px - ($i - 5) * 10px;
					height: 150px;
					top: 0;
					left: 0;
					right: 0;
					margin: 0 auto;
					text-align: center;
					transform-origin: 50% 100%;
					// border: 1px solid black;
					.title {
						font-size: 14px;
						color: #ffffff;
						display: block;
						text-align: center;
						height: 43px;
						line-height: 48px;
						width: 100%;
						padding: 0 4px;
						overflow: hidden;
						text-overflow: ellipsis;
						white-space: nowrap;
					}
					.draw-img {
						width: 30px;
						height: 30px;
						position: relative;
						top: 15px;
					}
				}
	
				@for $m from 1 through $i {
					div:nth-child(#{$m}) {
						transform: rotate(0deg+ (360 / $i) * ($m - 1));
					}
				}
			}
		}
	
		.draw-item-btn {
			width: 86px;
			height: 99px;
			background-image: url(../images/draw/go.png);
			background-size: 86px 99px;
			position: absolute;
			z-index: 2;
			top: 115px;
			left: 0;
			right: 0;
			margin: 0 auto;
			.draw-jifen {
				font-size: 10px;
				color: #fee863;
				position: relative;
				bottom: -64px;
				text-align: center;
				display: block;
			}
		}
		.draw-item-btn-nobg{
			background-image: url(../images/draw/nogo.png) !important;
			.draw-jifen{
				color: #BBBBBB  !important;
			}
		}
	}
</style>
