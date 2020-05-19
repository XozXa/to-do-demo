<template>
	<view class="content">
		<!--头部信息-->
		<view class="todo-header" v-if="list.length!==0">
			<view class="todo-header__left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<view class="todo-header__right">
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex===0}" @click="tab(0)">全部</view>
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex===1}" @click="tab(1)">待办</view>
				<view class="todo-header__right-item" :class="{'active-tab':activeIndex===2}" @click="tab(2)">已完成</view>
			</view>
		</view>
		<!--没有代办事件时的提示信息-->
		<view v-if="list.length===0" class="default">
			<view class="default-info">
				<view class="default-info__text">你还没有创建任何代办事项</view>
				<view class="default-info__text">点击下方+号创建一个吧！</view>
			</view>
			<view class="image-default">
				<image src="../../static/default.jpg" mode="aspectFit"></image>
			</view>
		</view>
		<!--代办事件主体-->
		<view v-else class="todo-content">
			<view class="todo-list" :class="{'todo--finish':item.checked}" v-for="(item,index) in listData" :key="index" @click="finish(item.id)">
				<view class="todo-list__checkbox">
					<view class="checkbox"></view>
				</view>
				<view class="todo-list__content">
					{{item.content}}
				</view>
			</view>
		</view>
		<!--创建按钮-->
		<view class="create-todo" @click="create">
			<text class="iconfont icon-add" :class="{'create-todo-active':textShow}"></text>
		</view>
		<!--输入框-->
		<view class="create-content" v-show="active" :class="{'create-show':textShow}">
			<view class="create-content-box">
				
				<view class="create-input">
					<input type="text" v-model="value" placeholder="请输入你要创建的todo" />
				</view>
				<view class="create-button" @click="add">
					创建
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[],
				active:false,
				value:'',
				activeIndex:0,
				text:'全部',
				textShow:false
			}
		},
		onLoad() {

		},
		computed:{
			listData(){
				let list=JSON.parse(JSON.stringify(this.list));
				let newList=[];
				//all
				if(this.activeIndex===0){
					this.text='全部';
					return list;
				}
				//loading
				if(this.activeIndex===1){
					this.text='待办';
					list.forEach((item)=>{
						if(!item.checked){
							newList.push(item);
						}
					})
					return newList;
				}
				//complete
				if(this.activeIndex===2){
					this.text='已完成';
					list.forEach((item)=>{
						if(item.checked){
							newList.push(item);
						}
					})
					return newList;
				}
			}
		},
		methods: {
			create(){
				if(!this.active){
					this.open();
				}else{
					this.close();
				}
			},
			open(){
				this.active=true;
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.textShow=true;
					},50);
				})
			},
			close(){
				this.textShow=false;
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.active=false;
					},300);
				})
			},
			add(){
				if(this.value===''){
					uni.showToast({
						title:"请输入内容"
					})
					return
				}
				this.list.unshift({content:this.value,id:'id'+new Date().getTime(),checked:false});
				this.value='';
				this.close();
			},
			//点击完成待办事项
			finish(id){
				let index=this.list.findIndex((item)=>item.id===id);//获取下标
				this.list[index].checked=!this.list[index].checked;
			},
			tab(index){
				this.activeIndex=index;
				
			}
		}
	}
</script>

<style>
	/* content */
	@import '../../common/icon.css';
	.todo-header{
		position:fixed;
		top:0;
		left: 0;
		width: 100%;
		display:flex;
		align-items: center;
		font-size: 12px;
		padding:0 15px;
		color:#333;
		height:45px;
		box-sizing: border-box;
		box-shadow:-1px 1px 5px rgba(0,0,0,0.1);
		background:#FFFFFF;
		z-index: 999;
	}
	.todo-header__left{
		width:100%;
	}
	.active-text{
		font-size: 14px;
		padding-right: 10px;
		color:#279ABF;
	}
	.todo-header__right{
		display: flex;
		flex-shrink:0;
	}
	.todo-header__right-item{
		padding:0 5px;
	}
	.active-tab{
		color:#279abf;
	}
	
	.todo-content{
		position: relative;
		padding-top:50px;
		padding-bottom:100px
	}
	.todo-list{
		position: relative;
		display: flex;
		align-items: center;
		padding:15px;
		margin: 15px;
		background:#cfebfd;
		color:#0c3854;
		font-size:14px;
		box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1);
		border-radius:10px;
		overflow: hidden;
	}
	.todo-list::after{
		position: absolute;
		content: '';
		top:0;
		bottom: 0;
		left: 0;
		width: 5px;
		background-color: #91d1e8;
	}
	.todo-list__checkbox{
		padding-right: 15px;
	}
	.checkbox{
		width:20px;
		height:20px;
		border-radius: 50%;
		background:#fff;
		box-shadow: 0 0 5px 1px rgba(0,0,0,0.1),-1px 2px 1px 0 rgba(255,255,255) inset;
	}
	.todo--finish .checkbox{
		background-color: #eee;
		position: relative;
	}
	.todo--finish .checkbox::after{
		content: '';
		position: absolute;
		width: 10px;
		height: 10px;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		margin: auto;
		border-radius: 50%;
		background-color: #CFEBFD;
		box-shadow: 0 0 2px 0px rgba(0,0,0,0.2) inset;
	}
	.todo--finish .todo-list__content{
		color: #999;
	}
	.todo--finish.todo-list::before{
		content: '';
		position: absolute;
		top:0;
		bottom: 0;
		left: 40px;
		right: 10px;
		height: 2px;
		margin: auto 0;
		background-color: #bdcdd8;
	}
	.todo--finish.todo-list::after{
		background: #ccc;
	}
	.create-todo{
		position: fixed;
		display: flex;
		justify-content: center;
		align-items: center;
		bottom:20px;
		left: 0;
		right:0;
		width:50px;
		height: 50px;
		margin: 0 auto;
		border-radius: 50%;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1),-1px 1px 1px 0 rgba(255,255,255) inset;
	}
	.icon-add{
		font-size:30px;
		color:#add8e6;
		transform: rotate(0);
		transition: transform 0.3s;
	}
	.create-todo-active{
		transform: rotate(135deg);
	}
	
	.create-content{
		position: fixed;
		bottom: 95px;
		left:20px;
		right:20px;
		transition:all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%);
	}
	.create-show{
		opacity: 1;
		transform: scale(1) translateY(0);
	}
	.create-content-box{
		display: flex;
		align-items: center;
		padding:0 15px;
		padding-right: 0;
		border-radius: 50px;
		background-color: #deeff5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1),-1px 1px 1px 0 rgba(255,255,255) inset;
		z-index: 2;
	}
	.create-input{
		width:100%;
		padding-right: 15px;
		font-size: 14px;
		color: #add8e6;
	}
	.create-button{
		flex-shrink: 0;
		width: 80px;
		display: flex;
		justify-content: center;
		align-items: center;
		height: 50px;
		border-radius: 50px;
		font-size: 16px;
		color: #88d4ec;
		box-shadow: -2px 0px 2px 1px rgba(0,0,0,0.1);
	}
	.create-content::after{
		content: '';
		position: absolute;
		right:0;
		left: 0;
		bottom: -10px;
		width: 20px;
		height: 20px;
		background-color: #deeff5;
		margin:0 auto;
		transform: rotate(45deg);
		box-shadow: 1px 1px 5px 2px rgba(0,0,0,0.1);
		z-index: -1;/* 层级关系 */
	}
	.create-content-box::after{
		content: '';
		position: absolute;
		right:0;
		left: 0;
		bottom: -10px;
		width: 20px;
		height: 20px;
		background-color: #deeff5;
		margin:0 auto;
		transform: rotate(45deg);
		/* box-shadow: 1px 1px 5px 2px rgba(0,0,0,0.1); */
	}
	.default{
		padding-top: 40px;
		
	}
	.image-default{
		display: flex;
		justify-content: center;
	}
	.image-default image{
		width:100%;
	}
	.default-info{
		text-align: center;
		font-size: 14px;
		color:#ccc;
	}
	
	
</style>
