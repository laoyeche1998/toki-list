<template>
	<view>
		
		<view class="cu-list menu-avatar margin-top margin-bottom">
			<view class="cu-item">
				<view class="cu-avatar radius lg" style="background-image:url(/static/nezha.jpg);"></view>
				<view class="content">
					<input type="text" class="text-lg text-shadow margin-bottom-xs" maxlength="18" placeholder="昵称" v-model="nickname"
					 @blur="save"></input>
					<input type="text" class="text-gray text-sm" maxlength="18" placeholder="给自己打个气" v-model="title_text" 
					 @blur="save"></input>
				</view>
			</view>
		</view>



		<view class="cu-bar bg-white solid-bottom">
			<view class="action">
				<text class="cuIcon-title text-yellow "></text> 添加新事项
			</view>
		</view>
		
		<view>
			<form>
				<view class="cu-form-group">
					<view class="title">设定截止时间</view>
					<picker mode="time" :value="input_ddl" start="00:00" end="23:59" @change="ddl_Change">
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
		</view>



		<view class="cu-bar bg-white solid-bottom margin-top margin-bottom">
			<view class="action">
				<text class="cuIcon-title text-orange "></text> 待完成
			</view>
		</view>
		
		<view class="cu-list menu card-menu flex flex-wrap " v-for="(item,index) in arr" v-show="arr[index].done?false:true"
		 :key="index+'done'">
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



		<view class="cu-bar bg-white solid-bottom margin-top margin-bottom">
			<view class="action">
				<text class="cuIcon-title text-green "></text>已完成
			</view>
		</view>
		
		<view class="cu-timeline " v-for="(item,index) in arr" v-show="arr[index].done" :key='index'>
				<view class="cu-time text-blue">{{item.ddl}}</view>
				<view class="cu-item cuIcon-check text-green text-xsl padding ">
					<view class="content padding cf">
						<text class="text-left fl">{{item.value}}</text>
						<button class="cu-btn bg-green shadow margin-xs fr" @click="del(index)">
							<text class="cuIcon-delete"></text>
						</button>
						<button class="cu-btn bg-green shadow margin-xs fr" @tap="showModal($event);tap_edit(index)" data-target="DialogModal1">
							<text class="cuIcon-edit"></text>
						</button>
					</view>
				</view>
			</view>



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
						<button class='cu-btn bg-green shadow margin-right' @tap="confirm_edit(edit_index)">确定</button>
						<button class="cu-btn line-green text-green " @tap="hideModal">取消</button>
					</view>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		/**
		 * @description 数据
		 * @property {String} title 标题
		 * @property {String} nickname 昵称
		 * @property {String} title_text 加油口号，放在头像栏
		 * @property {Array} arr 待办事项的数组
		 * 		@property {String} value 待办事项内容
		 * 		@property {String} ddl 截止时间   
		 * 		@property {Boolean} done 完成状态
		 * @property {String} input_text 输入事项的字符串
		 * @property {String} input_ddl 输入的截止时间
		 * @property {String} edit_text 修改事项时输入的字符串
		 * @property {Number} edit_index 被修改事项的下标
		 * @property {String} modalName 模态对话框的名字，为null时隐藏
		 */
		data() {
			return {
				title: 'To do list',
				nickname: '昵称',
				title_text: '每天起床第一句，先给自己打个气~',
				arr: [],
				input_text: "",
				input_ddl: "12:00",
				edit_text: "",
				edit_index: "",
				modalName: null
			}
		},
		
		/**
		 * @description 在页面加载时读取缓存中的数据
		 */
		onLoad() {
			try {
			    let my_arr = uni.getStorageSync('my_arr');
			    if (my_arr) {
					this.arr = my_arr;
			    }
				let my_nickname = uni.getStorageSync('my_nickname');
				if (my_nickname) {
					this.nickname = my_nickname;
				}
				let my_title_text = uni.getStorageSync('my_title_text');
				if (my_title_text) {
					this.title_text = my_title_text;
				}
			} catch (e) {
			    // error
			}
			// this.arr = uni.getStorageSync("my_arr");
			// this.nickname = uni.getStorageSync("my_nickname");
			// this.title_text = uni.getStorageSync("my_title_text");
			// console.log(this.arr);
		},

		methods: {
			/** 
			 * @description 按回车确认后显示新的项
			 * @param {Object} e 事件
			 * @deprecated 已经弃用，因为回车键不适用于移动端，用click_btn_go()代替。
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
			 * @description 按下GO按钮后，把新事项送入数组，并按截止时间排序，再清空input text，并保存 
			 */
			click_btn_go() {
				let value = this.input_text;
				let ddl = this.input_ddl;
				this.arr.push({
					value: value,
					ddl: ddl,
					done: false,
				});
				this.arr.sort(function(a, b) {
					return Date.parse('01 Jan 1970 00:' + a.ddl + ' GMT') - Date.parse('01 Jan 1970 00:' + b.ddl + ' GMT');
				});
				this.input_text = "";
				this.save();
			},

			/** 
			 * @description 点击checkbox后，打钩或去掉钩，修改对应事项的done属性
			 * @param {Number} index 事项的下标
			 */
			change(index) {
				this.arr[index].done = !(this.arr[index].done);
				this.save();
			},
			
			/** 
			 * @description 点击“删除”按钮后删除该项
			 * @param {Number} index 事项的下标
			 */
			del(index) {
				this.arr.splice(index, 1);
				this.save();
			},

			/** 
			 * @description 把数据保存到缓存中
			 */
			save() {
				uni.setStorage({
					key: 'my_arr',
					data: this.arr,
					success: function() { // 成功后回调的函数
						//console.log('save success');
					}
				});
				uni.setStorage({
					key: 'my_nickname',
					data: this.nickname,
				});
				uni.setStorage({
					key: 'my_title_text',
					data: this.title_text,
				});
			},

			/**
			 * @description 显示模态对话框
			 * @param {Object} e 点击事件
			 */
			showModal(e) {
				this.modalName = e.currentTarget.dataset.target;
			},
			
			/**
			 * @description 隐藏模态对话框
			 * @param {Object} e 点击事件
			 */
			hideModal(e) {
				this.edit_text = "";
				this.modalName = null;
			},

			/**
			 * @description 点击修改按钮后，为对话框传递参数
			 * @param {Object} index 被修改项的下标
			 */
			tap_edit(index) {
				this.edit_index = index;
				this.edit_text = this.arr[index].value;
			},

			/**
			 * @description 按下修改对话框中的确认键后，修改待办事项，再清空edit text，隐藏对话框，并保存
			 */
			confirm_edit() {
				let text = this.edit_text;
				let index = this.edit_index;
				this.arr[index].value = text;
				this.edit_text = "";
				this.modalName = null;
				this.save();
			},

			/**
			 * @description 从输入表单得到新事项的截止时间并传值
			 * @param {Object} e
			 */
			ddl_Change(e) {
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


	.logo {
		height: 200upx;
		width: 150upx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.input {
		margin-top: 10upx;
	}

	.done {
		color: #4CD964;
	}

	.notdone {
		color: #3F536E;
	}
</style>
