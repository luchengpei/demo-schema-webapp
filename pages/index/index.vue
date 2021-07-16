<template>
	<view >
		<unicloud-db v-slot:default="{data, loading, error, options}" collection="banner" ref="udb" >
			<view v-if="error">{{error.message}}</view>
			<view v-else-if="loading">
				正在加载。。。
			</view>
			<view class="list" v-if="data.length">
				<view class="box-wrap" v-for="(item,index) in data" :key="index">
					<image :src="item.pic" mode="" @click="updateBanner(item)"></image>
					<view class="">
						<uni-dateformat :date="item.createTime" :threshold="[0,3600000]" ></uni-dateformat>
					</view>
					<icon type="clear" size="26" class="icon" @click="deleteBanner(item)"/>
				</view>
			</view>
			<view class="operate-btn">
				<button type="default" @click="addBannner">新增</button>
				<!-- <button @click="updateBanner(data)">更新</button> -->
				<button type="default" @click="deleteBanner(data)" v-if="data.length">全部删除</button>
			</view>
		</unicloud-db>
	</view>
</template>

<script>
	export default {
		data:()=>({
			ids:[]
		}),
		methods: {
			addBannner(){
				uni.chooseImage({
					success: res => {
						this.uploadPic(res)
					}
				})
			},
			uploadPic(res){
				 let filePath = res.tempFilePaths[0];
				 let cloudPath = Date.now();
				 uniCloud.uploadFile({
				 	filePath,
					cloudPath:cloudPath+'.jpg',
					success: result => {
						this.$refs.udb.add({
							pic:result.fileID
						},{
							success:res=>{
								this.$refs.udb.loadData({clear:true})
							}
						})
					}
				 })
			},
			deleteBanner(item){
				let ids = !Array.isArray(item )? item._id : item.map(item => item._id);
				this.$refs.udb.remove(ids,{
					success:res => {
						this.$refs.udb.loadData({clear:true})
					}
				})
			},
			updateBanner({_id}){
				this.$refs.udb.update(_id,{
					createTime:new Date()
				},{
					success:res => {
						this.$refs.udb.loadData({clear:true})
					}
				}
				)
			}
		}
	}
</script>

<style lang="scss" scoped>
.list{
	text-align: center;
	>image{
		// width: 100%;
		// height: 300rpx;
	}
}
.box-wrap{
	position: relative;
	margin-top: 20rpx;
	.icon{
		position: absolute;
		right: 50rpx;
		top: -10rpx;
	}
}
</style>
