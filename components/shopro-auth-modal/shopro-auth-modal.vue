<template>
	<view class="">
		<u-popup v-model="showPhone" mode="center" width="80%" custom-style="border-radius:16rpx">
			<!-- <view class="phoneBtn">
				 <button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">
					 <image
						class="auto-login-img"
						:src="$IMG_URL + '/imgs/auto_login_wx.png'"
						mode=""
					 ></image>
				 </button> 
			</view> -->
			<view class="" style="padding: 20rpx;">
				<view style="text-align: center;font-size: 32rpx;font-weight: 600;">微信手机号授权</view>
				<view style="padding: 20rpx 0;">
					<!-- <view style="width: 80rpx;height: 80rpx;border-radius: 50%;overflow: hidden;margin: 0 auto;">
						<image style="width: 100%;height: 100%;" src="../../static/images/right.png" mode=""></image>
					</view> -->
					<view style="text-align: center;padding-top: 10rpx;">北京同城享惠</view>
				</view>
				<view style="text-align: center; padding-bottom: 20rpx;">申请获取你微信绑定的手机号</view>
				<view style="display: flex;    justify-content: space-around;">
					<button style="padding: 0;margin: 0;width: 40%;" @click="changeShowPhone">取消</button>
					<button style="padding: 0;margin: 0;width: 40%;" open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">确定</button> 
				</view>
			</view>
		</u-popup>
		<u-popup v-model="showAuth" safe-area-inset-bottom mode="bottom" border-radius="20" :closeable="true" close-icon-pos="top-right" @close="closeAuthModal">
			<view class="login-wrap">
				<!-- 1.账号密码登录 -->
				<u-form v-if="authType === 'accountLogin'" :model="form['accountLogin'].data" :rules="form['accountLogin'].rules" ref="accountLogin" :errorType="errorType">
					<!-- 标题 -->
					<view class="head-box u-m-b-60">
						<view class="u-flex u-m-b-20">
							<view class="head-title u-m-r-40 head-title-animation">账号登录</view>
							<view class="head-title-active head-title-line" @tap="showAuthModal('smsLogin')">短信登录</view>
						</view>
						<view class="head-subtitle">如果未设置过密码，请点击忘记密码</view>
					</view>
					<u-form-item :labelStyle="labelStyle" label-width="150" label-position="left" label="账号" prop="account">
						<u-input placeholder="请输入账号" :placeholderStyle="placeholderStyle" v-model="form['accountLogin'].data.account" type="text"></u-input>
						<button class="u-reset-button forgot-btn" slot="right" @tap="showAuthModal('forgotPwd')">忘记密码</button>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label=" 密码" prop="password" label-width="150">
						<input placeholder="请输入密码" :placeholderStyle="placeholderStyle" v-model="form['accountLogin'].data.password" type="number" password="true"></input>
						<button slot="right" class="u-reset-button login-btn-start" @tap="accountLoginSubmit">登录</button>
					</u-form-item>
					<button class="u-reset-button type-btn" @tap="showAuthModal('register')">立即注册</button>
				</u-form>
		
				<!-- 2.短信登录 -->
				<u-form v-if="authType === 'smsLogin'" :model="form['smsLogin'].data" :rules="form.smsLogin.rules" ref="smsLogin" :errorType="errorType">
					<view class="head-box u-m-b-60">
						<view class="u-flex u-m-b-20">
							<view class="head-title-active u-m-r-40" @tap="showAuthModal('accountLogin')">账号登录</view>
							<view class="head-title head-title-line head-title-animation">短信登录</view>
						</view>
						<view class="head-subtitle">未注册手机号请先点击下方立即注册</view>
					</view>
					<u-form-item :labelStyle="labelStyle" label-width="150" label-position="left" label="手机号" prop="mobile">
						<u-input placeholder="请输入手机号" @input="mobileInput" :placeholderStyle="placeholderStyle" v-model="form['smsLogin'].data.mobile" type="number"></u-input>
						<button
							class="u-reset-button code-btn code-btn-start"
							:disabled="!form['smsLogin'].data.isMobileEnd"
							:class="{ 'code-btn-end': form['smsLogin'].data.isMobileEnd }"
							slot="right"
							@tap="getSmsCode('mobilelogin')"
						>
							{{ codeText }}
						</button>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label="验证码" prop="code" label-width="150">
						<u-input placeholder="请输入验证码" :placeholderStyle="placeholderStyle" v-model="form['smsLogin'].data.code" type="number"></u-input>
						<button slot="right" class="u-reset-button login-btn-start" @tap="smsLoginSubmit">登录</button>
					</u-form-item>
					<button class="u-reset-button type-btn" @tap="showAuthModal('register')">立即注册</button>
				</u-form>
		
				<!-- 3.注册 -->
				<u-form v-if="authType === 'register'" :model="form['register'].data" :rules="form.register.rules" ref="register" :errorType="errorType">
					<view class="head-box u-m-b-60">
						<view class="head-title u-m-b-20">注册</view>
						<view class="head-subtitle">请使用本人手机号完成注册</view>
					</view>
					<u-form-item :labelStyle="labelStyle" label-width="150" label-position="left" label="手机号" prop="mobile">
						<u-input placeholder="请输入手机号" @input="mobileInput" :placeholderStyle="placeholderStyle" v-model="form['register'].data.mobile" type="number"></u-input>
						<button
							class="u-reset-button code-btn code-btn-start"
							:disabled="!form['register'].data.isMobileEnd"
							:class="{ 'code-btn-end': form['register'].data.isMobileEnd }"
							slot="right"
							@tap="getSmsCode('register')"
						>
							{{ codeText }}
						</button>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label=" 密码" prop="password" label-width="150">
						<input placeholder="请输入密码" :placeholderStyle="placeholderStyle" v-model="form['register'].data.password" type="password"></input>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label="验证码" prop="code" label-width="150">
						<u-input placeholder="请输入验证码" :placeholderStyle="placeholderStyle" v-model="form['register'].data.code" type="number"></u-input>
						<button slot="right" class="u-reset-button login-btn-start" @tap="registerSubmit">注册</button>
					</u-form-item>
					<button v-if="!isLogin" class="u-reset-button type-btn" @tap="showAuthModal('accountLogin')">返回登录</button>
				</u-form>
		
				<!-- 4.忘记密码 -->
				<u-form v-if="authType === 'forgotPwd'" :model="form['forgotPwd'].data" :rules="form['forgotPwd'].rules" ref="forgotPwd" :errorType="errorType">
					<view class="head-box u-m-b-60">
						<view class="head-title u-m-b-20">忘记密码</view>
						<view class="head-subtitle">为了您的账号安全，修改密码前请先进行安全验证</view>
					</view>
					<u-form-item :labelStyle="labelStyle" label-width="150" label-position="left" label="手机号" prop="mobile">
						<u-input placeholder="请输入手机号" @input="mobileInput" :placeholderStyle="placeholderStyle" v-model="form['forgotPwd'].data.mobile" type="number"></u-input>
						<button
							class="u-reset-button code-btn code-btn-start"
							:disabled="!form['forgotPwd'].data.isMobileEnd"
							:class="{ 'code-btn-end': form['forgotPwd'].data.isMobileEnd }"
							slot="right"
							@tap="getSmsCode('resetpwd')"
						>
							{{ codeText }}
						</button>
					</u-form-item>
		
					<u-form-item :labelStyle="labelStyle" label-position="left" label="验证码" prop="code" label-width="150">
						<u-input placeholder="请输入验证码" :placeholderStyle="placeholderStyle" v-model="form['forgotPwd'].data.code" type="number"></u-input>
					</u-form-item>
		
					<u-form-item :labelStyle="labelStyle" label-position="left" label="新密码" prop="password" label-width="150">
						<u-input placeholder="请输入密码" :placeholderStyle="placeholderStyle" v-model="form['forgotPwd'].data.password" type="number" password="true"></u-input>
						<button slot="right" class="u-reset-button login-btn-start" @tap="forgotPwdSubmit">确认</button>
					</u-form-item>
					<button v-if="!isLogin" class="u-reset-button type-btn" @tap="showAuthModal('accountLogin')">返回登录</button>
				</u-form>
		
				<!-- 5.绑定手机号 -->
				<u-form v-if="authType === 'bindMobile'" :model="form['bindMobile'].data" :rules="form['bindMobile'].rules" ref="bindMobile" :errorType="errorType">
					<view class="head-box u-m-b-60">
						<view class="head-title u-m-b-20">绑定手机号</view>
						<view class="head-subtitle">为了您的账号安全，请绑定手机号</view>
					</view>
					<u-form-item :labelStyle="labelStyle" label-width="150" label-position="left" label="手机号" prop="mobile">
						<u-input placeholder="请输入手机号" @input="mobileInput" :placeholderStyle="placeholderStyle" v-model="form['bindMobile'].data.mobile" type="number"></u-input>
						<button
							class="u-reset-button code-btn code-btn-start"
							:disabled="!form['bindMobile'].data.isMobileEnd"
							:class="{ 'code-btn-end': form['bindMobile'].data.isMobileEnd }"
							slot="right"
							@tap="getSmsCode('changemobile')"
						>
							{{ codeText }}
						</button>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label="验证码" prop="code" label-width="150">
						<u-input placeholder="请输入验证码" :placeholderStyle="placeholderStyle" v-model="form['bindMobile'].data.code" type="number"></u-input>
						<button slot="right" class="u-reset-button login-btn-start" @tap="bindMobileSubmit">立即绑定</button>
					</u-form-item>
				</u-form>
		
				<!-- 6.修改密码 -->
				<u-form v-if="authType === 'changePwd'" :model="form['changePwd'].data" :rules="form['changePwd'].rules" ref="changePwd" :errorType="errorType">
					<view class="head-box u-m-b-60">
						<view class="head-title u-m-b-20">修改密码</view>
						<view class="head-subtitle"></view>
					</view>
					<u-form-item :labelStyle="labelStyle" label-position="left" label="旧密码" prop="oldPassword" label-width="150">
						<input placeholder="请输入旧密码" :placeholderStyle="placeholderStyle" v-model="form['changePwd'].data.oldPassword" type="number" password="true"></input>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label="新密码" prop="newPassword" label-width="150">
						<input placeholder="请输入新密码" :placeholderStyle="placeholderStyle" v-model="form['changePwd'].data.newPassword" type="number" password="true"></input>
					</u-form-item>
					<u-form-item :labelStyle="labelStyle" label-position="left" label="确认密码" prop="reNewPassword" label-width="150">
						<input placeholder="再次输入新密码" :placeholderStyle="placeholderStyle" v-model="form['changePwd'].data.reNewPassword" type="number" password="true"></input>
					</u-form-item>
					<view class="editPwd-btn-box u-m-t-80">
						<button class="u-reset-button save-btn" @tap="changePwdSubmit">保存</button>
						<button class="u-reset-button forgot-btn" @tap="showAuthModal('forgotPwd')">忘记密码</button>
					</view>
				</u-form>
		
				<!-- 第三方登录 -->
				<view v-if="authType === 'accountLogin' || authType === 'smsLogin'" class="auto-login-box u-flex u-row-center u-col-center">
					<!-- 微信 -->
					<image
						v-if="['App', 'wxOfficialAccount', 'wxMiniProgram'].includes(platform)"
						class="auto-login-img"
						@tap="thirdLogin('wechat')"
						:src="$IMG_URL + '/imgs/auto_login_wx.png'"
						mode=""
					></image>
					<view class="wx-title" @tap="thirdLogin('wechat')">微信授权登录</view>
					<!-- <view class="phoneBtn">
						 <button open-type="getPhoneNumber" @getphonenumber="getPhoneNumber">
							 <image
								class="auto-login-img"
								:src="$IMG_URL + '/imgs/auto_login_wx.png'"
								mode=""
							 ></image>
						 </button> 
					</view> -->
					<!-- 支付宝 -->
					<!-- <image
						v-if="['App', 'alipayMiniProgram', 'H5'].includes(platform)"
						class="auto-login-img"
						@tap="thirdLogin('alipay')"
						:src="$IMG_URL + '/imgs/auto_login_ali.png'"
						mode=""
					></image> -->
					<!-- 苹果 -->
					<!-- #ifdef APP-PLUS -->
					<image v-if="device === 'ios'" class="auto-login-img" @tap="thirdLogin('apple')" :src="$IMG_URL + '/imgs/auto_login_iphone.png'" mode=""></image>
					<!-- #endif -->
				</view>
		
				<!-- 协议 -->
				<view v-if="['accountLogin', 'smsLogin', 'register'].includes(authType)" class="agreement-box u-flex u-row-center">
					<u-checkbox v-model="protocol" shape="circle" active-color="#ff9c00">
						<view class="agreement-text tcp-text u-flex u-col-center">
							我已阅读并遵守
							<view class="tcp-text u-flex u-col-center">
								<view @tap.stop="$Router.push({ path: '/pages/public/richtext', query: { id: config.shop.user_protocol || 0 } })">《用户协议》</view>
								与
								<view @tap.stop="$Router.push({ path: '/pages/public/richtext', query: { id: config.shop.privacy_protocol || 0 } })">《隐私协议》</view>
							</view>
						</view>
					</u-checkbox>
				</view>
		
				<!-- 公用验证码倒计时，防止恶意请求短信验证 -->
				<u-verification-code changeText="Xs" seconds="10" ref="code" @change="codeChange"></u-verification-code>
			</view>
		</u-popup>
	</view>
</template>

<script>
/**
 * 登录提示页
 */
import FormValidate from '@/shopro/validate/form';
import wechat from '@/shopro/wechat/wechat';
import { mapMutations, mapActions, mapState } from 'vuex';
// #ifdef APP-PLUS
import apple from '@/shopro/apple';
// #endif
export default {
	name: 'shoproAuthModal',
	data() {
		return {
			showPhone:false,
			platform: this.$platform.get(),
			device: this.$platform.device(),
			form: {
				// 1.账号密码登录
				accountLogin: {
					data: {
						account: '', // 账号
						password: '' // 密码
					},
					rules: {
						account: FormValidate.account,
						password: FormValidate.password
					}
				},

				// 2.短信登录
				smsLogin: {
					data: {
						mobile: '', // 手机号
						code: '', // 验证码
						isMobileEnd: false // 手机号输入完毕
					},
					rules: {
						code: FormValidate.code,
						mobile: FormValidate.mobile
					}
				},
				// 3.注册
				register: {
					data: {
						mobile: '', // 手机号
						code: '', // 验证码
						password: '', // 密码
						isMobileEnd: false // 手机号输入完毕
					},
					rules: {
						code: FormValidate.code,
						mobile: FormValidate.mobile,
						password: FormValidate.password
					}
				},

				// 4.忘记密码
				forgotPwd: {
					data: {
						mobile: '', //手机号
						code: '', //验证码
						password: '', //密码
						isMobileEnd: false // 手机号输入完毕
					},
					rules: {
						mobile: FormValidate.mobile,
						code: FormValidate.code,
						password: FormValidate.password
					}
				},
				// 5.绑定手机号
				bindMobile: {
					data: {
						mobile: '', //手机号
						code: '', //验证码
						isMobileEnd: false // 手机号输入完毕
					},
					rules: {
						code: FormValidate.code,
						mobile: FormValidate.mobile
					}
				},
				// 6.修改密码
				changePwd: {
					data: {
						oldPassword: '', //旧密码
						newPassword: '', //新密码
						reNewPassword: '' //确认密码
					},
					rules: {
						oldPassword: FormValidate.password,
						newPassword: FormValidate.password,
						reNewPassword: [
							{
								required: true,
								message: '请重新输入密码',
								trigger: ['change', 'blur']
							},
							{
								validator: (rule, value, callback) => {
									return value === this.form.changePwd.data.newPassword;
								},
								message: '两次输入的密码不一致',
								trigger: ['change', 'blur']
							}
						]
					}
				}
			},
			codeText: '获取验证码',
			protocol: false, //是否同意隐私协议
			// 表单样式
			errorType: ['message'],
			labelStyle: {
				'font-size': '30rpx',
				'font-weight': '600',
				color: '#333'
			},
			placeholderStyle: 'font-size: 30rpx; font-weight: 500;color:#C2C7CF;'
		};
	},

	computed: {
		...mapState({
			authType: ({ user }) => user.authType,
			config: ({ shopro }) => shopro.config,
			isLogin: ({ user }) => user.isLogin
		}),
		showAuth: {
			get() {
				return !!this.authType;
			},
			set(value) {
				value ? uni.hideTabBar() : uni.showTabBar();
			}
		}
	},
	mounted() {
		this.switchModalType(this.authType);
	},
	watch: {
		authType(newValue, oldValue) {
			delete this.$refs[oldValue];
			this.switchModalType(newValue);
		}
	},
	methods: {
		getPhoneNumber(e) {
			this.showPhone = false
			var that = this
			if (!this.protocol) {
				this.$u.toast('请同意用户协议');
				return false;
			}
		  if (!e.target.iv) {
		    uni.showModal({
		      content: '获取手机号失败！',
		      showCancel: false
		    })
		    return;
		  }
		  uni.checkSession({
		    success: res => {
				wx.login({
					success(loginRes) {
						that.$http('user.getWxMiniProgramSessionKey',{code:loginRes.code,autoLogin:true}).then(res=>{
							that.$http('user.wxMiniProgramRefresh',{encryptedData:e.detail.encryptedData,iv:e.detail.iv,session_key:res.data.session_key}).then(ress=>{
								console.log(ress)
								// that.getUserInfo(res.data.token);
							})
						})
					}
				})
		      // bindMobileApi({
		      //   ivdata: e.target.iv,
		      //   encrypdata: e.target.encryptedData,
		      // }).then(res => {
		      //   this.$emit('callback',true);
		      //   store.dispatch('getInfo', false)
		      //   uni.showModal({
		      //     content: '登录成功',
		      //     showCancel: false
		      //   })
		      // }).catch(err => {
		      //   store.dispatch('getInfo', false)
		      // })
		    },
		    fail: res => {
		      // store.dispatch('getInfo', false)
		      uni.showModal({
		        content: '获取手机号失败，请再次尝试!',
		        showCancel: false
		      })
		    }
		  })
		},
		...mapActions(['getUserInfo', 'showAuthModal']),

		// 手机号是否输入完毕
		mobileInput() {
			this.form[this.authType].data.isMobileEnd = this.$u.test.mobile(this.form[this.authType].data.mobile);
		},

		// 切换表单模式
		switchModalType(type) {
			type &&
				this.$nextTick(() => {
					this.$refs[type].resetFields();
					this.$refs[type].setRules(this.form[type].rules);
				});
		},
		closeAuthModal() {
			this.showAuth = true;
			// this.$store.dispatch('showAuthModal', '');
		},

		// 获取短信验证码
		getSmsCode(type) {
			const that = this;
			if (that.form[this.authType].data.isMobileEnd && that.$refs['code'].canGetCode) {
				that.$http(
					'common.smsSend',
					{
						mobile: that.form[this.authType].data.mobile,
						event: type
					},
					'获取验证码中...'
				).then(res => {
					if (res.code === 1) {
						that.$refs['code'].start();
						that.$u.toast('验证码已发送，请注意查收短信');
					} else {
						that.$u.toast(res.msg);
					}
				});
			} else {
				that.$u.toast('请稍后再试');
			}
		},

		// 倒计时
		codeChange(e, type) {
			this.codeText = e;
		},

		// 规则校验
		validateSubmit() {
			if (['accountLogin', 'smsLogin', 'register'].includes(this.authType) && !this.protocol) {
				this.$u.toast('请同意用户协议');
				return false;
			}
			return this.$refs[this.authType].validate();
		},

		// 第三方登录
		async thirdLogin(provider) {
			if (!this.protocol) {
				this.$u.toast('请同意用户协议');
				return false;
			}
			const that = this;
			let token = '';
			switch (provider) {
				case 'wechat':
					token = await wechat.login();
					break;
				case 'alipay':
					break;
				case 'apple':
					token = await apple.appleIdOauth();
					break;
				default:
					break;
			}
			if(token) {
				that.closeAuthModal();
				that.getUserInfo(token);
				that.showPhone = true 
				that.$forceUpdate()
				console.log(that.showPhone)
				setTimeout(()=>{
					that.$http('user.info',{}).then(res=>{
						var user = res.data
						if(wx.getStorageSync("fxid")){
							that.$http('user.buindUser',{pid:wx.getStorageSync("fxid"),uid:user.id}).then(res=>{
							})
						}
						if(wx.getStorageSync("pid")){
							var createDate = new Date(user.createtime*1000);
							var nowDate = new Date();
							// 格式化日期
							var createTime = createDate.toLocaleString();
							var nowTime = nowDate.toLocaleString();
							if(createTime.split(" ")[0] == nowTime.split(" ")[0]){
								console.log("今天注册")
								that.$http('user.newUser',{pid:wx.getStorageSync("pid")}).then(res=>{
									
								})
							}else{
								console.log("注册时间: " + createTime.split(" ")[0])
							}
						}
					})
				},1000)
			}
		},

		// 1.账号登录
		async accountLoginSubmit() {
			let that = this;
			(await that.validateSubmit()) &&
				that
					.$http(
						'user.accountLogin',
						{
							account: that.form['accountLogin'].data.account,
							password: that.form['accountLogin'].data.password
						},
						'登录中...'
					)
					.then(res => {
						if (res.code === 1) {
							that.closeAuthModal();
							that.getUserInfo(res.data.token);
							that.$u.toast(res.msg);
							setTimeout(()=>{
								that.$http('user.info',{}).then(res=>{
									var user = res.data
									if(wx.getStorageSync("fxid")){
										that.$http('user.buindUser',{pid:wx.getStorageSync("fxid"),uid:user.id}).then(res=>{
										})
									}
									if(wx.getStorageSync("pid")){
										var createDate = new Date(user.createtime*1000);
										var nowDate = new Date();
										// 格式化日期
										var createTime = createDate.toLocaleString();
										var nowTime = nowDate.toLocaleString();
										if(createTime.split(" ")[0] == nowTime.split(" ")[0]){
											console.log("今天注册")
											that.$http('user.newUser',{pid:wx.getStorageSync("pid")}).then(res=>{
												
											})
										}else{
											console.log("注册时间: " + createTime.split(" ")[0])
										}
									}
								})
							},1000)
						}
					});
		},

		// 2.短信登录
		async smsLoginSubmit() {
			let that = this;
			(await that.validateSubmit()) &&
				that
					.$http(
						'user.smsLogin',
						{
							mobile: that.form['smsLogin'].data.mobile,
							code: that.form['smsLogin'].data.code
						},
						'登录中...'
					)
					.then(res => {
						if (res.code === 1) {
							that.closeAuthModal();
							that.getUserInfo(res.data.token);
							that.$u.toast(res.msg);
							setTimeout(()=>{
								that.$http('user.info',{}).then(res=>{
									var user = res.data
									if(wx.getStorageSync("fxid")){
										that.$http('user.buindUser',{pid:wx.getStorageSync("fxid"),uid:user.id}).then(res=>{
										})
									}
									if(wx.getStorageSync("pid")){
										var createDate = new Date(user.createtime*1000);
										var nowDate = new Date();
										// 格式化日期
										var createTime = createDate.toLocaleString();
										var nowTime = nowDate.toLocaleString();
										if(createTime.split(" ")[0] == nowTime.split(" ")[0]){
											console.log("今天注册")
											that.$http('user.newUser',{pid:wx.getStorageSync("pid")}).then(res=>{
												
											})
										}else{
											console.log("注册时间: " + createTime.split(" ")[0])
										}
									}
								})
							},1000)
						}
					});
		},

		// 3.注册
		async registerSubmit() {
			let that = this;
			(await that.validateSubmit()) &&
				that
					.$http(
						'user.register',
						{
							mobile: that.form['register'].data.mobile,
							code: that.form['register'].data.code,
							password: that.form['register'].data.password
						},
						'注册中...'
					)
					.then(res => {
						if (res.code === 1) {
							that.$u.toast(res.msg);
							that.closeAuthModal();
							that.getUserInfo(res.data.token);
						}
					});
		},

		// 4.忘记密码
		async forgotPwdSubmit() {
			let that = this;
			(await that.validateSubmit()) &&
				that
					.$http(
						'user.forgotPwd',
						{
							mobile: that.form['forgotPwd'].data.mobile,
							code: that.form['forgotPwd'].data.code,
							password: that.form['forgotPwd'].data.password
						},
						'修改中...'
					)
					.then(res => {
						if (res.code === 1) {
							that.$u.toast(res.msg);
							if (that.isLogin) {
								that.closeAuthModal();
							} else {
								that.showAuthModal('accountLogin');
							}
						}
					});
		},

		// 5.绑定手机
		async bindMobileSubmit() {
			let that = this;
			(await that.validateSubmit()) &&
				that
					.$http(
						'user.bindMobile',
						{
							mobile: that.form['bindMobile'].data.mobile,
							code: that.form['bindMobile'].data.code,
							password: that.form['bindMobile'].data.password
						},
						'绑定中...'
					)
					.then(res => {
						if (res.code === 1) {
							that.$u.toast(res.msg);
							that.closeAuthModal();
							that.getUserInfo();
						}
					});
		},

		// 6.修改密码
		async changePwdSubmit() {
			let that = this;
			(await that.validateSubmit()) &&
				that
					.$http(
						'user.changePwd',
						{
							oldpassword: that.form['changePwd'].data.oldPassword,
							newpassword: that.form['changePwd'].data.newPassword
						},
						'修改中...'
					)
					.then(res => {
						if (res.code === 1) {
							that.closeAuthModal();
							that.$u.toast(res.msg);
							that.getUserInfo(res.data.userinfo.token);
						}
					});
		},
		//点击取消时，隐藏
		changeShowPhone(){
			this.showPhone = false
		}
	}
};
</script>

<style lang="scss" scoped>
@keyframes title {
	0% {
		font-size: 32rpx;
	}

	100% {
		font-size: 36rpx;
	}
}

.login-wrap {
	padding: 50rpx 34rpx;
	min-height: 700rpx;

	.head-box {
		.head-title {
			min-width: 160rpx;
			font-size: 36rpx;
			font-weight: bold;
			color: #333333;
			line-height: 36rpx;
		}
		.head-title-active {
			width: 160rpx;
			font-size: 32rpx;
			font-weight: 600;
			color: #999;
			line-height: 36rpx;
		}
		.head-title-animation {
			animation-name: title;
			animation-duration: 0.1s;
			animation-timing-function: ease-out;
			animation-fill-mode: forwards;
		}
		.head-title-line {
			position: relative;
			&::before {
				content: '';
				width: 1rpx;
				height: 34rpx;
				background-color: #e4e7ed;
				position: absolute;
				left: -30rpx;
				top: 50%;
				transform: translateY(-50%);
			}
		}

		.head-subtitle {
			font-size: 26rpx;
			font-weight: 400;
			color: #c2c7cf;
		}
	}
	.code-btn[disabled] {
		background-color: #fff;
	}

	.code-btn-start {
		width: 160rpx;
		line-height: 56rpx;
		border: 1rpx solid #e9b766;
		border-radius: 28rpx;
		font-size: 26rpx;
		font-weight: 400;
		color: #e9b766;
		opacity: 0.5;
	}

	.forgot-btn {
		width: 160rpx;
		line-height: 56rpx;
		font-size: 30rpx;
		font-weight: 500;
		color: #999;
	}

	.code-btn-end {
		opacity: 1 !important;
	}

	.login-btn-start {
		width: 158rpx;
		line-height: 56rpx;
		background: linear-gradient(90deg, #ff9c00, #eecc89);
		border-radius: 28rpx;
		font-size: 26rpx;
		font-weight: 500;
		color: #ffffff;
	}

	.type-btn {
		padding: 20rpx;
		margin: 40rpx auto;
		width: 200rpx;
		font-size: 30rpx;
		font-weight: 500;
		color: #999999;
	}

	.auto-login-box {
		width: 100%;
		display: flex;
		flex-direction: column;
		.auto-login-img {
			width: 85rpx;
			height: 85rpx;
			border-radius: 50%;
			margin: 0 15rpx;
		}
		.wx-title{
			margin-top: 10rpx;
			color: #999;
		}
	}

	.agreement-box {
		margin: 80rpx auto 0;

		.agreement-text {
			font-size: 26rpx;
			font-weight: 500;
			color: #999999;

			.tcp-text {
				color: #e9b562;
			}
		}
	}
}

// 修改密码
.editPwd-btn-box {
	.save-btn {
		width: 690rpx;
		line-height: 70rpx;
		background: linear-gradient(90deg, #ff9c00, #eecc89);
		border-radius: 35rpx;
		font-size: 28rpx;
		font-weight: 500;
		color: #ffffff;
	}
	.forgot-btn {
		width: 690rpx;
		line-height: 70rpx;
		font-size: 28rpx;
		font-weight: 500;
		color: #999999;
	}
}
.phoneBtn{
	button::after{
		border-color: #FFFFFF !important;
	}
	button{
		background: #fff !important;
	}
}
</style>
