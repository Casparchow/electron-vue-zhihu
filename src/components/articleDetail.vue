<template>
	<div class="detail-container">
		<div class="author-box">
			<author-component :extra-data="totalData.extra"></author-component>
		</div>
		<div class="content-box">
			<a class="close-btn btn-floating waves-effect waves-light red" v-on:click="closeDetail()">
				<i class="iconfont icon-close"></i>
			</a>
			<div v-html="contentData">
			</div>
		</div>
	</div>
</template>
<style lang="less">
	.detail-container{
		display: flex;
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		width: auto;
		height: 100%;
		z-index: 3000;
		.author-box{
			position: fixed;
			flex: 0 350px;
			height: 100%;
			min-width: 350px;
			width: 350px;
			overflow: auto;
			background: #fafafa;
			border-right: 1px solid #eee;
			box-shadow: 5px 0 20px rgba(0,0,0,.1);
			&::-webkit-scrollbar-thumb,
			&::-webkit-scrollbar-button{
				background: transparent;
			}
			&:hover{
				&::-webkit-scrollbar-thumb{
					background: #eee;
				}
			}
		}
		.content-box{
			flex: 1 100%;
			position: relative;
			margin-left: 350px;
			min-width: 600px;
			/*max-width: 1000px;*/
			.close-btn{
				position: fixed;
				top: 20px;
				right: 100px;
			}
			h2{
				font-size: 34px;
				font-weight: bold;
				text-align: center;
			}
			p{
				text-align:left;
				text-indent: 2em;
				padding-bottom: 10px;
				font-size: 16px;
			}
			.main-wrap{
				margin: 0 auto;
				max-width: 650px;
				line-height: 24px;
				font-family: '黑体';
			}
			.meta{
					margin: 5px 0;
					font-size: 18px;
					font-weight: bold;
			}
			.content img{
				display: inline-block;
				margin: 0 auto;
				max-width: 500px;
				max-height: 350px;
			}
			.answer{
				hr{
					margin: 5px 0;
				}
				a:hover{
					text-decoration: underline;
				}
				ul,ol{
					padding: 0 0 0 2em;
					li {
						padding-left: 4px;
						list-style: decimal;
						font-size: 16px;
					}
				}
				article h2{
					font-size: 20px;
				}
			}
		}
	}
</style>
<script lang="babel">
	export default{
		data(){
			return {
				contentData: '',
				totalData: {}
			}
		},
		created(){
			//接收事件需在发送事件前面，否则接收不了
			eventHandler.$on('sendNewsData', totalData => {
				this.totalData = totalData
				this.contentData = totalData.body
				setTimeout(_ =>{
					//设置图片居中...
					$('.content-image').parent().css('text-align','center')
				},50)
			})
			eventHandler.$emit('getNewsDetail')
			//如果直接在页面点击刷新，则根据路由id重新获取内容
			if(!this.contentData) 
				this.getNewsDetail(this.$route.params.article_id)

		},
		mounted(){
			$('img').length > 1 && this.animateImg()
		},
		methods: {
			animateImg(){
				// 图片加载完成之后动画效果出现
				$('img').each(function(){
					$(this).hide()
					$(this).load(function(){
						$(this).show(500)
					})
				})
			},
			getNewsDetail(id){
				$.ajax({
					url: 'http://localhost:3333/getNewsDetail',
					method: 'get',
					data: {'id': id},
					dataType: 'json',
					success: data => {
						this.totalData = data
						this.contentData = data.body
						setTimeout(_ =>{
							//设置图片居中...
							$('.content-image').parent().css('text-align','center')
							this.animateImg()
						},50)
					}
				})
			},
			closeDetail(id){
				this.$router.push({path: '/main'})
			}
		},
		components: {
			authorComponent: require('./authorBox.vue')
		}
	}
</script>