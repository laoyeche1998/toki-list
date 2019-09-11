<template>
	<view>
		<!-- <cu-custom bgColor="bg-blue" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">导航栏</block>
		</cu-custom> -->

		<!-- <view class="flex solid-bottom padding margin-xs justify-center">
				<text class="title">{{title}}</text>
		</view> -->

		<!-- <view class="flex solid-bottom padding justify-center">
			<image class="logo  padding-sm margin-xs radius" src="/static/top.jpg"></image>
			<view class="text-area">
				<text class="title margin-xs">{{title}}</text>
			</view>
		</view> -->

		<view class="cu-list menu-avatar margin-top">
			<view class="cu-item">
				<view class="cu-avatar radius lg" style="background-image:url(/static/top.jpg);"></view>
				<view class="content">
					<view class="text-grey">ZARD</view>
					<view class="text-gray text-sm flex">
						<view class="text-cut">
							每天起床第一句，先给自己打个气~
						</view>
					</view>
				</view>
				
			</view>
		</view>



		<!-- <input class="input border" type="text" placeholder="请输入" @confirm="sure" v-model="input_text" /> -->
		<form>
			<view class="cu-form-group">
				<view class="title">要做的事</view>
				<input type="text" maxlength="18" placeholder="在这里输入" name="input" v-model="input_text"></input>
				<button class='cu-btn bg-red shadow' @click="click_btn_go">GO! <text class="cuIcon-add"></text></button>
			</view>
		</form>


		<view class="padding">
			<view class="cu-list menu flex flex-wrap " v-for="(item,index) in arr" :key="index">
				<view class="cu-item basis-xl margin-xs padding-sm  ">
					<view class="action">
						<!-- <checkbox class="bg-blue blue" v-bind:checked="arr[index].done" @click="change(index)" /> -->
						<checkbox-group class="block">
							<view class="cu-form-group">
								<checkbox :checked="arr[index].done?true:false" :class="arr[index].done?'checked':''" @click="change(index)"></checkbox>
							</view>
						</checkbox-group>
					</view>
					<view class="content " :class="[item.done ? 'done' : 'notdone']">{{index + 1}}. {{item.value}}</view>
					<view class="action">
						<button class="cu-btn bg-green shadow round" @click="del(index)"><text class="cuIcon-delete"></text> 删除</button>
					</view>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'To do list',
				arr: [],
				input_text: ""
			}
		},
		onLoad() {
			this.arr = uni.getStorageSync("my_arr");
			console.log(this.arr);
		},
		// onReady() {
		// 	uni.getStorage({
		// 	    key: 'my_arr',
		// 	    success: function (res) {
		// 	        //console.log(res.data);
		// 			console.log('ONSHOW ' + res.data);
		// 			this.$set(this.someObject,'arr',res.data)
		// 			console.log(this.arr);
		// 		}
		// 	});
		// 	
		// },
		
		methods: {
			/** 按回车确认后显示新的项
			 * @param {Object} e
			 */
			sure(e) {
				let value = e.detail.value;
				this.arr.push({
					value: value,
					done: false
				});
				this.input_text = "";
				this.save();
			},

			/**
			 * 按下button go 后，把input text 送入队列，再清空input text
			 */
			click_btn_go() {
				let value = this.input_text;
				this.arr.push({
					value: value,
					done: false
				});
				this.input_text = "";
				this.save();
			},

			/** 点击checkbox后，打钩或去掉钩
			 * @param {Object} index
			 */
			change(index) {
				this.arr[index].done = !(this.arr[index].done);
				this.save();
			},

			/** 点击“删除”后删除该项
			 * @param {Object} index
			 */
			del(index) {
				this.arr.splice(index, 1);
				this.save();
			},
			
			/** save my storage
			 *
			 */
			save(){
				uni.setStorage({
				    key: 'my_arr',
				    data: this.arr,
				    success: function () {
				        console.log('success');
				    }
				});
			},

		}
	}
</script>

<style>
	page {
		background: rgb(238, 248, 249);
	}


	/* .content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		//background: rgb(238,248,249); 
	} */

	.logo {
		height: 200upx;
		width: 150upx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	/* .title {
		font-size: 36upx;
		color: #8f8f94;
		/* margin-top: 20upx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 30upx; 
	} */

	.input {
		margin-top: 10upx;
	}

	/* .item {
		display: flex;
		
	} */

	.done {
		color: #4CD964;
	}

	.notdone {
		color: #3F536E;
	}
</style>
