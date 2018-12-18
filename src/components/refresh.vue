<template>
	<transition name="refresh">
		<div class="refresh-wrapper" ref="refresh">
			<div class="refresh-inner">
				<div class="refresh-pull" v-show="state==='pull'">
					<span>下拉刷新-.-</span>
				</div>
				<div class="refresh-loading" v-show="state==='loading'">
					<span>刷新中...~.~</span>
				</div>
				
				<div class="refresh-end" v-show="state==='end'">
					<i class="icon mtui-icon-select-o"></i>
					<span>刷新完成!^.^</span>
				</div>
			</div>
			
		</div>
	</transition>
</template>

<script>
export default {
	name: 'refresh',
	props: {
		isFixed: {
			type: Boolean,
			default: false
		},
		maxRange: {
			type: Number,
			default: 100
		}
	},
	data(){
		return {
			startY: 0,
			allow: true,
			translate: 0,
			state: 'pull', //pull,loading, end
			currentNode: null,
			firstNode: null,
		}
	},

	computed:{

	},

	watch:{

	},

	destroyed(){
		console.log('destroyed');
		document.removeEventListener('touchstart',this.touchStart,false);
		document.removeEventListener('touchmove',this.touchMove,false);
		document.removeEventListener('touchend',this.touchEnd,false);
		document.body.style.transform = '';
	},

	methods: {
		init(){
			document.addEventListener('touchstart',this.touchStart,false);
			document.addEventListener('touchmove',this.touchMove,false);
			document.body.addEventListener('touchend',this.touchEnd,false);
			document.body.style.transition = 'transform 0.3s linear';
		},
		isIOS(){
			let u = navigator.userAgent, app = navigator.appVersion;
    		let isIOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/); //ios终端
    		return isIOS;
		},

		getTouchTarget(elm){
			let currentNode = elm;
			while(currentNode && currentNode.tagName !== "HTML" &&
				currentNode.tagName !== "BODY" && currentNode.nodeType === 1){
				let overflowY = window.getComputedStyle(currentNode).overflowY;
				if(overflowY === "scroll" || overflowY === "auto"){
					this.currentNode = currentNode;
					this.firstNode = currentNode.firstElementChild;
					break;
				}
				currentNode = currentNode.parentNode;
			}
			
		},

		touchStart(e){
			if(this.state === 'loading') return;
			this.startY = e.touches[0].clientY;
			this.getTouchTarget(e.target);
			console.log(this.allow)
		},
		touchMove(e){
			let firstTop=0,  currentTop=0;
			if(this.firstNode){ 
				firstTop = this.firstNode.getBoundingClientRect().top;
			 	currentTop = this.currentNode.getBoundingClientRect().top;	
			}
			 let range = (e.touches[0].clientY - this.startY);
			if( document.documentElement.scrollTop>0 || this.state === 'loading' || firstTop-currentTop <0 || range<0) return;
			

			range = range*0.75 > this.maxRange? this.maxRange : range;
			
			this.translate = range;
			this.changeView();

		},
		touchEnd(e){
			if(this.state === 'loading') return;
			
			if(this.translate && this.translate >= this.maxRange){
				this.translate = this.maxRange/2;
				
				this.refresh();
			}else{
				this.translate = 0;
			}
			this.rotate = 0;
			this.changeView();
		},

		refresh(){
			this.state = 'loading';
			console.log('更新中...');
			// setTimeout(this.refreshEnd,2000);
			this.$emit('refresh');

		},

		refreshEnd(){
			let _this = this;
			this.state = 'end';

			setTimeout(()=>{

				_this.translate = 0;
				_this.changeView();
			},1000);

			setTimeout(()=>{
				_this.state = 'pull';
			},1300)
			
			console.log('更新完成...');
		},

		changeView(){
			if(this.isFixed){
				this.$refs.refresh.style.transform=`translate3d(0,${this.translate}px,0)`;
			}else{
				document.body.style.transform = `translate3d(0,${this.translate}px,0)`;
			}
			
		}
	},

	mounted(){
		this.init();
	}
}

</script>

<style lang="less" scoped>
	.refresh-wrapper {
		position: fixed;
		top: -200px;
		left: 0;
		width: 100%;
		height: 200px;
		text-align: center;
		background: #f5f5f5;
		z-index: 999;
		transition: all 0.3s linear;

		.refresh-inner {
			position: relative;
			width: 100%;
			height: 100%;
		}



		.refresh-pull, .refresh-loading, .refresh-end {
			position: absolute;
			left: 0;
			bottom: 0;
			width: 100%;
			line-height: 1rem;
			color: #999;
			font-size: 0.3rem;
			text-align: center;
		}
	}
</style>