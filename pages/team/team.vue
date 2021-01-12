<template>
	<view>
		<view class="body">
			<view v-if="!colonelInfo.nickname">
				<!-- 个人信息 -->
				<view class="myInfo">
					<image :src="userInfo.avatar_url" class="head" mode="aspectFill"></image>
					<view>
						<view class="font30Rpx">
							<text class="nameText">{{userInfo.nickname}}</text>
							<view class="grade" v-if="userInfo.type!=1">
								<image :src="'../../../static/images/indexs/level'+userInfo.type==2?1:2+'.png'" mode="aspectFit"></image>{{userInfo.type==2?'初级团长':'超级团长'}}
							</view>
						</view>
						<view class="p">ID:{{userInfo.code}}</view>
					</view>
				</view>
				<!-- <scroll-view> -->
					<!-- 称号说明 -->
					<view class="shadowBox" v-if="userInfo.type==2">
						<view class="title">初级团长权益</view>
						<view class="font24Rpx">可享受团员订单返利商品金额{{primaryNum}}%的分佣</view>
					</view>
					<!-- 称号任务 -->
					<view class="shadowBox" v-if="userInfo.type!=1">
						<view v-if="userInfo.type==2">
							<view class="title">如何晋升为超级团长：（任选其一）</view>
							<view class="font24Rpx">团员订单金额累计达{{superMoney}}元以上</view>
							<view class="speed">
								<view>
									<view :style="{'v-if':"translateX("+userInfo.totalCommissionPro+"+%)"}"></view>
								</view>
								<text>{{userInfo.order_money}}/{{superMoney}}元</text>
							</view>
							<view class="font24Rpx">团员中已购买用户累计{{superNum}}人以上</view>
							<view class="speed">
								<view>
									<view :style="{'v-if':"translateX("+userInfo.memberPro+"+%)"}"></view>
								</view>
								<text>{{userInfo.order_num}}/{{superNum}}人</text>
							</view>
						</view>
						<view class="tips" v-if="userInfo.totalCommissionPro===0 || userInfo.memberPro===0">已达成，待系统审核</view>
						<view class="title">超级团长权益：</view>
						<view class="font24Rpx">可享受团员订单返利商品金额{{superNums}}%的分佣</view>
					</view>
					<!-- 成员列表 -->
					<view class="groupList">
						<view class="title">我的团员</view>
						<view v-for="item in memberList" wx:key="item.id">
							<view class="groupItem" :data-num="item.order_num" :data-id="item.id" :bindtap="seeMygroup">
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
						</view>
					</view>
				<!-- </scroll-view> -->
				<view class="btn" bindtap="inviteGroup">邀请更多团员</view>
			</view>
			<!-- 邀请成员弹窗 -->
	<!-- 		<view class="mask" v-if="isShowShareBox" :bindtap="hideMask">
				<view class="shareBox fadeShow">
					<image class="head" :src="userInfo.avatar_url" mode="aspectFill"></image>
					<view class="nameText">{{userInfo.nickname}}</view>
					<image class="QRcode" :src="userInfo.qr_code" mode="aspectFill"></image>
					<view class="tip">扫描二维码，赶快加入我吧~</view>
					<view class="popupBtn" bindtap="synthesisPoster">分享我的二维码</view>
				</view>
			</view> -->
			<!-- 接收邀请 -->
	<!-- 		<view class="mask" v-if="isShowInviteBox">
				<view class="inviteBox fadeShow" catchtap="preventBubbling">
					<image class="head" :src="colonelInfo.avatar_url" mode="aspectFill"></image>
					<view class="nameText">{{colonelInfo.nickname || '未知'}}</view>
					<view>邀请你体验健康元小程序，成为他的团员</view>
					<view class="popupBtn" catchtap="acceptInvite">确认接受邀请</view>
				</view>
			</view> -->
			<!-- 无法接受邀请 -->
<!-- 			<view class="mask" v-if="isShowNoInviteBox" bindtap="retHome">
				<view class="noInviteBox fadeShow">
					<image src="../../../static/images/indexs/noInviteBox.png" mode="aspectFill"></image>
					<view>您不是新用户，无法接受邀请成为团员，快去邀请好友成为你的团员吧！</view>
					<view class="popupBtn" catchtap="retHome">确认</view>
				</view>
			</view> -->
			<!-- <canvas canvas-id="myCanvas" id="myCanvas"></canvas> -->
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
				memberList: [], // 成员列表
				userInfo: {
					
				}, // 个人信息
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
				colonelInfo: {
					nickname:'张三',
					
				}, //邀请人信息
				superNum: 50,
				superMoney: 15000,
			}
		},
		methods: {
			
		},
		onLoad(){
			
		}
	}
</script>

<style>

</style>
