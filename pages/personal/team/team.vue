<template>
	<view class="body">
		<block v-if="!colonelInfo.nickname">
			<!-- 个人信息 -->
			<view class="myInfo">
				<image :src="userInfo.avatar_url" class="head" mode="aspectFill"></image>
				<view>
					<view class="font30Rpx">
						<text class="nameText">{{userInfo.nickname}}</text>
						<view class="grade" v-if="userInfo.type!=1">
							<image src="../../../static/images/indexs/level1.png" mode="aspectFit"></image>{{userInfo.type==2?'初级团长':'超级团长'}}
						</view>
					</view>
					<view class="p">ID:{{userInfo.code}}</view>
				</view>
			</view>
			<scroll-view scroll-y bindscrolltolower="getMemberList">
				<!-- 称号说明 -->
				<view class="shadowBox" v-if="userInfo.type==2">
					<view class="title">初级团长权益</view>
					<view class="font24Rpx">可享受团员订单返利商品金额{{primaryNum}}%的分佣</view>
				</view>
				<!-- 称号任务 -->
				<view class="shadowBox" v-if="userInfo.type!=1">
					<block v-if="userInfo.type==2">
						<view class="title">如何晋升为超级团长：（任选其一）</view>
						<view class="font24Rpx">团员订单金额累计达{{superMoney}}元以上</view>
						<view class="speed">
							<view>
								<view :style="{'transform': 'translateX('+userInfo.totalCommissionPro+'%)'}"></view>
							</view>
							<text>{{userInfo.order_money}}/{{superMoney}}元</text>
						</view>
						<view class="font24Rpx">团员中已购买用户累计{{superNum}}人以上</view>
						<view class="speed">
							<view>
								<view :style="{'transform': 'translateX('+userInfo.memberPro+'%)'}"></view>
							</view>
							<text>{{userInfo.order_num}}/{{superNum}}人</text>
						</view>
					</block>
					<view class="tips" v-if="userInfo.totalCommissionPro===0 || userInfo.memberPro===0">已达成，待系统审核</view>
					<view class="title">超级团长权益：</view>
					<view class="font24Rpx">可享受团员订单返利商品金额{{superNums}}%的分佣</view>
				</view>
				<!-- 成员列表 -->
				<view class="groupList">
					<view class="title">我的团员</view>
					<block v-for="item in memberList" :key="item.id">
						<view class="groupItem" :data-num="item.order_num" :data-id="item.id" @click="seeMygroup">
							<image :src="item.avatar_url" mode="aspectFill"></image>
							<view class="left">
								<view class="font30Rpx nameText">{{item.nickname}}</view>
								<view>ID:{{item.code}}</view>
							</view>
							<view class="right">
								<view class="font30Rpx">获利佣金：{{item.commission}}元</view>
								<view>订单数：{{item.order_num}}</view>
							</view>
						</view>
					</block>
					<!-- <load-more :loadMoreType="loadMoreType" textList="['暂无数据','拼命加载中...','我是有底线的啦~','30rpx','green']"></load-more> -->
				</view>
			</scroll-view>
			<view class="btn-box">
				<view class="btn" click="inviteGroup">邀请更多团员</view>	
			</view>

		</block>
		<!-- 邀请成员弹窗 -->
		<view class="mask" v-if="isShowShareBox" @click="hideMask">
			<view class="shareBox fadeShow">
				<image class="head" :src="userInfo.avatar_url" mode="aspectFill"></image>
				<view class="nameText">{{userInfo.nickname}}</view>
				<image class="QRcode" :src="userInfo.qr_code" mode="aspectFill"></image>
				<view class="tip">扫描二维码，赶快加入我吧~</view>
				<view class="popupBtn" @click="synthesisPoster">分享我的二维码</view>
			</view>
		</view>
		<!-- 接收邀请 -->
		<view class="mask" v-if="isShowInviteBox">
			<view class="inviteBox fadeShow" @click="preventBubbling">
				<image class="head" :src="colonelInfo.avatar_url" mode="aspectFill"></image>
				<view class="nameText">{{colonelInfo.nickname || '未知'}}</view>
				<view>邀请你体验健康元小程序，成为他的团员</view>
				<view class="popupBtn" @click="acceptInvite">确认接受邀请</view>
			</view>
		</view>
		<!-- 无法接受邀请 -->
		<view class="mask" v-if="isShowNoInviteBox" @click="retHome">
			<view class="noInviteBox fadeShow">
				<image src="../../../static/images/indexs/noInviteBox.png" mode="aspectFill"></image>
				<view>您不是新用户，无法接受邀请成为团员，快去邀请好友成为你的团员吧！</view>
				<view class="popupBtn" @click="retHome">确认</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isShowShareBox: false, // 是否显示邀请成员弹窗
				isShowInviteBox: false, // 是否显示接收邀请弹窗
				isShowNoInviteBox: false, // 是否显示无法接受邀请弹窗
				memberList: [{id:1,avatar_url:'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2796144188,439704386&fm=26&gp=0.jpg',nickname:'Lisa',code:'xjbbj01',commission:'100',order_num:'20'},
				{id:2,avatar_url:'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2796144188,439704386&fm=26&gp=0.jpg',nickname:'Bob',code:'fbui09s',commission:'1000',order_num:'382'},
				{id:3,avatar_url:'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2796144188,439704386&fm=26&gp=0.jpg',nickname:'Jhon',code:'fyyn9s',commission:'3869',order_num:'2873'},
				{id:4,avatar_url:'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2796144188,439704386&fm=26&gp=0.jpg',nickname:'Larra',code:'fjbduf0',commission:'3762',order_num:'3721'},
				{id:5,avatar_url:'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2796144188,439704386&fm=26&gp=0.jpg',nickname:'Dawy',code:'duubn2u',commission:'277',order_num:'100'}], // 成员列表
				userInfo: {}, // 个人信息
				loadMoreType: 1,
				showAddressOption: {
					isShow: false,
					type: 2,
					title: "获取用户授权",
					test: "小程序将获取你的用户信息",
					cancelText: "取消",
					confirmText: "授权",
					color_confirm: '#A3271F'
				},
				colonelInfo: {}, //邀请人信息
				superNum: 50,
				superMoney: 15000,
				loadMoreType: 0,
				userInfo: {
					nickName: 'xLi',
					type : 2,
					code : '12u3bjn',
					avatar_url : 'https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2796144188,439704386&fm=26&gp=0.jpg',
					order_money:1000,
					order_num:10
				},
				superNum: 0,
				superMoney: 1000,
				primaryNum: 100,
				superNums: 10
			};
		}
	}
</script>

<style lang="less">
	page,
	.body {
		height: 100%;
		overflow: hidden;
		background-color: #f5f5f5;
	}

	.nameText {
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.nameText:empty::before {
		content: "未知";
	}

	.title {
		padding-left: 16rpx;
		position: relative;
		margin-bottom: 30rpx;
		font-size: 28rpx;
		line-height: 40rpx;
		color: #333;
	}

	.title::before {
		content: ' ';
		position: absolute;
		top: 50%;
		left: 0;
		width: 6rpx;
		height: 28rpx;
		border-radius: 6rpx;
		background-color: #fa436a;
		transform: translateY(-50%);
	}

	/* 个人信息 */
	.myInfo {
		padding-left: 36rpx;
		display: flex;
		align-items: center;
		height: 200rpx;
		// background-color: #165d4c;
		background-color: #fa436a;
		color: #fff;
	}

	.myInfo>image {
		flex-shrink: 0;
		margin-right: 6rpx;
	}

	.myInfo>view {
		flex-grow: 1;
	}

	.myInfo>.p {
		font-size: 26rpx;
	}

	.myInfo .font30Rpx {
		line-height: 50rpx;
		max-width: 580rpx;
	}

	.myInfo .nameText {
		display: inline-block;
		max-width: calc(100% - 160rpx);
		vertical-align: middle;
	}

	.myInfo .grade {
		display: inline-block;
		margin-left: 20rpx;
		padding: 0 10rpx;
		line-height: 50rpx;
		border-radius: 50rpx;
		background-color: #f6f6f6;
		font-size: 22rpx;
		color: #666;
		vertical-align: middle;
	}

	.myInfo .grade image {
		margin-right: 6rpx;
		width: 25rpx;
		height: 25rpx;
		vertical-align: top;
		transform: translateY(12.5rpx);
	}

	/* 称号说明 */
	.shadowBox {
		margin: 0 32rpx 20rpx;
		padding: 20rpx 0 32rpx 20rpx;
		background-color: #fff;
		box-shadow: 4rpx 4rpx 20rpx rgba(0, 0, 0, 0.1);
		border-radius: 16rpx;
	}

	.shadowBox>view {
		padding-left: 16rpx;
	}

	.shadowBox .tips {
		margin: 10rpx 0 30rpx;
		color: #999;
	}

	.font24Rpx {
		font-size: 24rpx;
		color: #333;
		line-height: 34rpx;
	}

	.speed {
		margin: 10rpx 0 30rpx;
		font-size: 20rpx;
		color: #999;
	}

	.speed>view {
		position: relative;
		display: inline-block;
		width: 443rpx;
		height: 20rpx;
		background-color: #cdefe7;
		border-radius: 9rpx;
		overflow: hidden;
	}

	.speed>view>view {
		position: absolute;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		border-radius: 9rpx;
		background-color: #fa436a;
		transform: translateX(-100%);
		transition: transform .5s ease-out;
	}

	.speed>text {
		display: inline-block;
		transform: translate(10rpx, 14rpx);
	}

	/* 成员列表 */
	scroll-view {
		box-sizing: border-box;
		padding-top: 20rpx;
		width: 100%;
		height: calc(100% - 200rpx);
	}

	.groupList {
		padding: 0 30rpx 180rpx;
	}

	.groupItem {
		padding: 30rpx 0;
		display: flex;
		align-items: center;
		border-bottom: 2rpx solid #ddd;
		color: #333;
	}

	.font30Rpx {
		margin-bottom: 18rpx;
		font-size: 30rpx;
		line-height: 34rpx;
	}

	.groupItem image {
		margin-right: 6rpx;
		flex-shrink: 0;
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
	}

	.groupItem .left {
		flex-grow: 1;
		max-width: 280rpx;
	}

	.groupItem .left view:last-of-type {
		font-size: 26rpx;
		line-height: 34rpx;
	}

	.groupItem .right {
		flex-shrink: 0;
		text-align: right;
	}

	.groupItem .right view:last-of-type {
		font-size: 22rpx;
		line-height: 40rpx;
		color: #999;
	}
	.btn-box {
		position: fixed;
		bottom: 0;
		padding:20px 0;
		width: 100%;
		background-color: #fff;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.btn {
		// left: 50%;
		width: 680rpx;
		line-height: 80rpx;
		background-color: #fa436a;
		border-radius: 40rpx;
		// transform: translateX(-50%);
		text-align: center;
		color: #fff;
		font-size: 30rpx;
	}

	/* 邀请成员弹窗 */
	.mask {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		text-align: center;
		background-color: rgba(34, 34, 34, .53);
		z-index: 10;
	}

	.mask .popupBtn {
		margin: 0 auto;
		background-color: #fa436a;
		height: 70rpx;
		border-radius: 35rpx;
		color: #fff;
		font-size: 30rpx;
	}

	.head {
		width: 120rpx;
		height: 120rpx;
		border-radius: 50%;
	}

	.mask .QRcode {
		width: 222rpx;
		height: 222rpx;
	}

	.mask>view {
		position: absolute;
		top: 50%;
		left: 50%;
		background-color: #fff;
		transform: translate(-50%, -50%);
		border-radius: 16rpx;
		overflow: hidden;
	}

	.shareBox {
		width: 540rpx;
		height: 731rpx;
	}

	.shareBox image:first-of-type {
		margin: 27rpx 0 13rpx;
	}

	.shareBox view:first-of-type {
		line-height: 34rpx;
		font-size: 32rpx;
		color: #333;
	}

	.shareBox view:first-of-type:empty::before {
		content: '未知';
	}

	.shareBox image:last-of-type {
		margin: 71rpx 0 18rpx;
	}

	.shareBox .tip {
		font-size: 22rpx;
		color: #666;
	}

	.shareBox .popupBtn {
		margin-top: 66rpx;
		width: 300rpx;
		line-height: 70rpx;
	}

	/* 接受邀请 */
	.inviteBox {
		width: 540rpx;
		height: 580rpx;
	}

	.inviteBox .head {
		margin: 66rpx 0 13rpx;
	}

	.inviteBox .nameText {
		font-size: 30rpx;
		color: #333;
		line-height: 34rpx;
	}

	.inviteBox view:not([class]) {
		margin: 60rpx auto;
		width: 427rpx;
		font-size: 34rpx;
		font-weight: bold;
		line-height: 42rpx;
		color: #333;
	}

	.inviteBox .popupBtn {
		width: 300rpx;
		line-height: 70rpx;
	}

	/* 无法接受邀请弹窗 */
	.noInviteBox {
		width: 540rpx;
		height: 580rpx;
	}

	.noInviteBox image {
		margin: 41rpx 0 60rpx;
		width: 200rpx;
		height: 177rpx;
	}

	.noInviteBox view:first-of-type {
		margin: 0 auto 60rpx;
		width: 427rpx;
		font-size: 34rpx;
		font-weight: bold;
		line-height: 42rpx;
		color: #333;
	}

	.noInviteBox .popupBtn {
		width: 300rpx;
		line-height: 70rpx;
	}

	#myCanvas {
		position: fixed;
		left: 100%;
		top: 100%;
		width: 750rpx;
		height: 1140rpx;
	}

	/* 弹窗动画 */
	@keyframes fadeShow {
		0% {
			transform: scale(.1) translate(-50%, -50%);
			opacity: 0;
		}

		90% {
			transform: scale(1.1) translate(-50%, -50%);
			opacity: 1;
		}

		100% {
			transform: scale(1) translate(-50%, -50%);
			opacity: 1;
		}
	}

	.fadeShow {
		animation: fadeShow .5s forwards;
		transform-origin: 0 0;
	}
</style>
