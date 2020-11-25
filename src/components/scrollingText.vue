<template>
	<!-- 无缝滚动效果 -->
	<div class="marquee-wrap">
		<ul class="marquee-list" :class="{ 'animate-up': animateUp }">
			<li v-for="(item, index) in scrollList" :key="index">
				<span>{{ item.name }}</span>
				<span>{{ item.mobile }}</span>
				<span>{{ item.prize }}</span>
			</li>
		</ul>
		<div class="marquee-list-nodata" v-if="scrollList.length==0">暂无获奖名单</div>
	</div>
</template>

<script>
export default {
	props: {
		listData: {
			type: Array,
			default: []
		}
	},
	data() {
		return {
			scrollList: [],
			animateUp: true,
			timer: null
		};
	},
	watch: {
		listData: {
			handler(newVal, oldVal) {
				// console.log(newVal,oldVal);
				this.scrollList = JSON.parse(JSON.stringify(newVal));
				clearInterval(this.timer);
				if (newVal.length >= 6) {
					this.timer = setInterval(this.scrollAnimate, 2500);
				}
			},
			immediate: true
		}
	},
	methods: {
		scrollAnimate() {
			this.animateUp = true;
			setTimeout(() => {
				this.scrollList.push(this.scrollList[0]);
				this.scrollList.shift();
				this.animateUp = false;
			}, 1800);
		}
	},
	destroyed() {
		clearInterval(this.timer);
	}
};
</script>

<style scoped lang="scss">
.marquee-wrap {
	height: 100px;
	margin-left: 23px;
	margin-right: 5px;
	margin-top: 10px;
	overflow: hidden;
	.marquee-list-nodata{
		    margin-top: 40px;
		    font-size: 14px;
		    color: #fbfdff59;
	}
	.marquee-list {
		width: 100%;
		padding-left: 0px;;
		padding-inline-start: 0px;
		margin-block-start: 0px;
		li {
			width: 100%;
			height: 20px;
			list-style: none;
			line-height: 20px;
			color: #fff;
			font-size: 13px;
			font-weight: 400;
			display: flex;
			justify-content: space-between;
			text-align: left;
			span {
				display: block;
			}
			span:nth-child(1) {
				width: 50px;
			}
			span:nth-child(2) {
				width: 80px;
			}
			span:nth-child(3) {
				width: 95px;
				overflow: hidden;
			}
		}
	}
	.animate-up {
		// transition: all 1s ease-in-out;
		transition:all 1s cubic-bezier(0.4, 0, 0.2, 1);
		transform: translateY(-20px);
	}
}
.hidetext {
	text-overflow: ellipsis;
	overflow: hidden;
	white-space: nowrap;
}
</style>
