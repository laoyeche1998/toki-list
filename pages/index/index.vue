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

		<view class="cu-list menu-avatar margin-top margin-bottom">
			<view class="cu-item">
				<view class="cu-avatar radius lg" style="background-image:url(/static/top.jpg);"></view>
				<view class="content">
					<view class="text-grey">我</view>
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
				<view class="title">设定截止时间</view>
				<picker mode="time" :value="input_ddl" start="00:00" end="23:59" @change="TimeChange">
					<view class="picker">
						{{input_ddl}}
					</view>
				</picker>
			</view>
			<view class="cu-form-group">
				<view class="title">要做的事</view>
				<input type="text" maxlength="18" placeholder="在这里输入" name="input" v-model="input_text"></input>
				<button class='cu-btn bg-red shadow radius' @click="click_btn_go">GO! <text class="cuIcon-add"></text></button>
			</view>
		</form>


		<view class="cu-bar bg-white solid-bottom margin-top margin-bottom">
			<view class="action">
				<text class="cuIcon-title text-orange "></text> 待完成
			</view>
		</view>
		<view class="">
			<view class="cu-list menu card-menu flex flex-wrap " v-for="(item,index) in arr" v-show="arr[index].done?false:true"
			 :key="index">
				<view class="cu-item padding-sm sm-border">
					<view class="action">
						<checkbox-group class="block">
							<view class="cu-form-group">
								<checkbox :checked="arr[index].done?true:false" :class="arr[index].done?'checked':''" @click="change(index)"></checkbox>
							</view>
						</checkbox-group>
					</view>
					<view class="cu-tag bg-red text-sm margin-right">{{item.ddl}}</view>
					<view class="content " :class="[item.done ? 'done' : 'notdone']">{{item.value}}</view>
					<view class="action">
						<button class="cu-btn bg-green shadow margin-right-sm " @tap="showModal($event);tap_edit(index)" data-target="DialogModal1">
							<text class="cuIcon-edit"></text>
						</button>
						<button class="cu-btn bg-green shadow " @click="del(index)">
							<text class="cuIcon-delete"></text>
						</button>
					</view>
				</view>
			</view>
		</view>



		<view class="cu-bar bg-white solid-bottom margin-top margin-bottom">
			<view class="action">
				<text class="cuIcon-title text-green "></text>已完成
			</view>
		</view>
		<view class="">
			<view class="cu-timeline " v-for="(item,index) in arr" v-show="arr[index].done" :key="index">
				<view class="cu-time text-blue">{{item.ddl}}</view>
				<view class="cu-item cuIcon-check text-green text-xsl padding ">
					<view class="content padding cf" >
						<text class="text-left fl">{{item.value}}</text>
						<button class="cu-btn bg-green shadow margin-xs fr" @tap="showModal($event);tap_edit(index)" data-target="DialogModal1">
							<text class="cuIcon-edit"></text>
						</button>
						<button class="cu-btn bg-green shadow margin-xs fr" @click="del(index)">
							<text class="cuIcon-delete"></text>
						</button>
						
					</view>
				</view>
			</view>
		</view>



		<!-- <view class="cu-bar bg-white margin-top">
			<view class="action">
				<button class="cu-btn bg-green shadow" @tap="showModal" data-target="DialogModal1">
					<text class="cuIcon-edit"></text>修改
				</button>
			</view>
		</view> -->
		<view class="cu-modal" :class="modalName=='DialogModal1'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content">修改内容</view>
					<view class="action" @tap="hideModal">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view class="padding-xl">
					<input type="text" maxlength="18" placeholder="在这里输入" name="edit" value="edit_text" v-model="edit_text"></input>
				</view>
				<view class="cu-bar bg-white justify-end">
					<view class="action">
						<button class='cu-btn bg-green shadow margin-right' @tap="click_btn_edit(edit_index)">确定</button>
						<button class="cu-btn line-green text-green " @tap="hideModal">取消</button>
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
				input_text: "",
				input_ddl: "12:00",
				edit_text: "",
				edit_index: "",
				modalName: null
			}
		},
		onLoad() {
			this.arr = uni.getStorageSync("my_arr");
			//console.log(this.arr);
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
				let ddl = this.input_ddl;
				this.arr.push({
					value: value,
					ddl: ddl,
					done: false,
				});
				this.arr.sort(function(a,b) {
				return Date.parse('01 Jan 1970 00:'+a.ddl+' GMT')-Date.parse('01 Jan 1970 00:'+b.ddl+' GMT');
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
			save() {
				uni.setStorage({
					key: 'my_arr',
					data: this.arr,
					success: function() {
						//console.log('save success');
					}
				});
			},

			showModal(e) {
				//console.log(e);
				this.modalName = e.currentTarget.dataset.target;
			},
			hideModal(e) {
				//console.log(e);
				this.edit_text = "";
				this.modalName = null;
			},

			tap_edit(index) {
				this.edit_index = index;
				this.edit_text = this.arr[index].value;
			},

			/**
			 * 按下修改窗口中的确认键后，修改待办事项，再清空edit text
			 */
			click_btn_edit() {
				let text = this.edit_text;
				let index = this.edit_index;
				this.arr[index].value = text;
				this.edit_text = "";
				this.modalName = null;
				this.save();
			},

			TimeChange(e) {
				this.input_ddl = e.detail.value;
			},
			

		},
		
		

		
	}
</script>

<style>
	page {
		/*background: rgb(238, 248, 249);
		*/
	   background: #ffffff;
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
