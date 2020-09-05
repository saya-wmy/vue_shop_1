<template>
	<div class="login_container">
		<form class="login">
			<div class="input">
				<div class="user-box">
					<i class="icon-user"></i>
					<input type="text" class="user" v-model="user" @blur="checkUser" />
				</div>
				<p clsaa="user_warning">{{userWarn}}</p>
				<div class="psw-box">
					<i class="icon-lock"></i>
					<input type="password" class="psw" v-model="psw" @blur="checkPsw" />
				</div>
				<p clsaa="psw_warning">{{pswWarn}}</p>
			</div>
			<input type="submit" class="submit" value="登录" @click="login" />
			<input type="reset" class="reset" value="重置" />
		</form>
	</div>
</template>

<script>
	//import axios from '../../node_modules/axios/dist/axios.js';
	export default{
		data() {
			return {
				user: "admin",
				psw: "123456",
				userWarn: "",
				pswWarn: ""
				
			}
		},
		methods: {
			checkUser() {
				if(this.user == "") {
					this.userWarn = "请输入用户名";
				}
			},
			checkPsw() {
				if(this.psw == ""){
					this.pswWarn = "请输入密码";
				}else if(this.psw.length < 6 || this.psw.length > 10){
					this.pswWarn = "长度在 6 到 10 个字符";
				}
			},
			login() {
				this.$http.post('login', {username: this.user, password: this.psw}).then(response => {
					 if(response.data.meta.status == 200){
						 window.sessionStorage.setItem("token",response.data.data.token);
						//console.log(response.data.data);
						this.$router.push('/home');
					 }
				});
			}
		}
	}
</script>

<style scoped>
	.login_container {
		background: #2b4b6b;
		height: 100%;
	}
	.login {
		width: 550px;
		height: 350px;
		background: #fff;
		position: absolute;
		left: 50%;
		top: 50%;
		transform: translate(-50%,-50%);
	}
	.login:after {
		content: "";
		display: block;
		width: 150px;
		height: 150px;
		background-clip: content-box;
		border: 10px solid #fff;
		//padding: 9px;
		border-radius: 50%;
		background: url(../assets/logo.png) #eee no-repeat;
		background-size: 150px 150px;
		position: absolute;
		left: 50%;
		//top: 50%;
		transform: translate(-50%,-50%);
		box-shadow: 0 0 10px #ddd;
	}
	.input {
		position: absolute;
		//background: pink;
		width: 450px;
		height: 150px;
		left: 50px;
		top: 150px;
	}
	.input div {
		width: 420px;
		height: 40px;
		margin: 0px 0 0 10px; 
		border: 1px solid #ddd;
		border-radius: 5px;
		padding-left: 10px;
	}
	.input div .icon-user {
		display: block;
		background: url(../assets/icon-user.png) #fff no-repeat;
		background-size: 20px 20px;
		width: 20px;
		height:20px;
		float: left;
		margin-top: 10px;
	}
	.input div .icon-lock {
		display: block;
		background: url(../assets/icon-lock.png) #fff no-repeat;
		background-size: 20px 20px;
		width: 20px;
		height:20px;
		float: left;
		margin-top: 10px;
	}
	.input div input {
		height: 40px;
		box-sizing: border-box;
		border: none;
		float: left;
		margin-left: 10px;
		outline: none;
	}
	.input p {
		height: 10px;
		line-height: 10px;
		font-size: 2px;
		color: red;	
		margin: 5px 0px 5px 10px;
	}
	.login .submit, .reset {
		width: 70px;
		height: 40px;
		position: absolute;
		border-radius: 5px;
		border: none;
	}
	.login .submit {
		background: #66B1FF;
		bottom: 30px;
		right: 150px;
	}
	.login .reset {
		background: #A6A9AD;
		bottom: 30px;
		right: 60px;
	}
</style>
