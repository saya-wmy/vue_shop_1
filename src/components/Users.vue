<template>
	<div class="userpage">
		<div class="users">
			<p><span>首页</span><span> > </span><span>权限管理</span><span> > </span><span>角色列表</span></p>
			<form>
				<!-- <div><input type="text" /><button><img src="../../../img/search.png" /></button></div> -->
				<div @mouseenter="showOff" @mouseleave="closeOff"><input type="text" v-model="searchword" :class="{shortinput: isoffshow}" /><div class="offsearch" v-if="isoffshow" @click="turnOffSearch"></div><button @click="searchPerson"></button></div>
				<button class="add" @click="addUser">添加用户</button>
				<table cellspacing="0">
					<thead>
						<tr>
							<th> </th>
							<th>姓名</th>
							<th>邮箱</th>
							<th>电话</th>
							<th>角色</th>
							<th>状态</th>
							<th>操作</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="(user,index) in userslist">
							<td>{{index+1}}</td>
							<td>{{user.username}}</td>
							<td>{{user.email}}</td>
							<td>{{user.mobile}}</td>
							<td>{{user.role_name}}</td>
							<td>{{user.mg_state}}</td>
							<td></td>
						</tr>
					</tbody>
				</table>
				<p>
					<span>共{{total}}条</span>
					<select v-model="userparam.pagesize" @change="getTotalPages()" >
						<option value="1">1条/页</option>
						<option value="2">2条/页</option>
						<option value="5">5条/页</option>
						<option value="10">10条/页</option>
					</select>
					<!-- <span class="select1" v-for="n in (parseInt(totalpage))"><span v-if="n==1">&lt;  </span><span>  {{n}}  </span><span v-if="n==parseInt(totalpage)">  &gt;</span></span> -->
					<ul class="select1"><span class="left" :class="{disleft: leftfb}" @click="pageBack"><strong>&lt;  </strong></span><li class="selnum" v-for="n in (parseInt(totalpage))" @click="jumpToPage(n)"><strong>  {{n}}  </strong></li><span class="right" :class="{disright: rightfb}" @click="pageForward"><strong>  &gt;</strong></span></ul>
					<span class="select2">前往<input v-model="userparam.pagenum" />页</span>
				</p>
			</form>
		</div>
		<div :class="{diag: isshowdiag}" v-if="isshowdiag">{{diagmessage}}</div>
		<div class="mask" v-if="isadduser">
			<div class="card">
				<div class="title">
					<span>添加用户</span>
					<span class="closecard" @click="closeCard"><img src="../assets/close1.png" /></span>
				</div>
				<div class="inputlist">
					<div class="iptitem usrname"><div><span class="iptname">用户名</span><span class="icon">*</span></div><input v-model="addUserForm.username" @blur="usrNameCheck" /></div>
					<!-- <span v-show="isnameerr">{{usrnamewarn}}</span> -->
					<span class="namewarnword">{{usrnamewarn}}</span>
					<div class="iptitem psw"><div><span class="iptname">密码</span><span class="icon">*</span></div><input v-model="addUserForm.password" @blur="pswCheck" /></div>
					<span class="pswwarnword">{{pswwarn}}</span>
					<div class="iptitem email"><div><span class="iptname">邮箱</span><span class="icon">*</span></div><input v-model="addUserForm.email" @blur="emailCheck" /></div>
					<span class="emailwarnword">{{emailwarn}}</span>	
					<div class="iptitem tel"><div><span class="iptname">手机</span><span class="icon">*</span></div><input v-model="addUserForm.mobile" @blur="telCheck" /></div>
					<span class="telwarnword">{{telwarn}}</span>
				</div>
				<div class="btns">
					<button class="cancle" @click="closeCard">取消</button>
					<button class="commit" @click="submitUser">确认</button>
				</div>
			</div>
		</div>
	</div>
	
</template>

<script>
	export default{
		data() {
			return {
				userslist: [],
				totaluserlist: [],
				total: 0,
				totalpage: 0,
				userparam: {
					"query": "",
					"pagenum": 1,
					"pagesize": 2
				},
				leftfb: false,
				rightfb: false,
				searchword: "",
				isoffshow: false,
				isadduser: false,
				// isnameerr: false,
				// ispswerr: false,
				// isemailerr: false,
				// istelerr: false,
				usrnamewarn: "",
				pswwarn: "",
				emailwarn: "",
				telwarn: "",
				addUserForm: {
					username: "",
					password: "",
					email: "",
					mobile: ""
				},
				diagmessage: "",
				isshowdiag: false
			}
		},
		created() {
				this.getUsers();
				this.enDisLeft();
				this.enDisRight();
		},
		watch: {
			'userparam.pagenum': function(newnum) {
				if(this.userparam.pagenum > this.totalpage){
					this.userparam.pagenum = this.totalpage;
				}
				this.getUsers();
				this.enDisLeft();
				this.enDisRight();
			},
			'userparam.pagesize': function(newnum) {
				this.userparam.pagenum = 1;
				this.getUsers();
				this.enDisLeft();
				this.enDisRight();
			},
			'searchword': function() {
				if(this.searchword == ""){
					this.isoffshow = false;
				}else{
					this.isoffshow = true;
				}
			},
			deep: true,
			immediate: true
		},
		methods: {
			getUsers() {
				this.$http.get('users', {params: this.userparam}).then(res => {
					this.total = res.data.data.total;
					this.userslist = res.data.data.users;
					console.log("userslist:",res);
					this.getTotalPages();
					
				});	
			},
			getTotalPages() {
				var totalpages = parseInt(((parseInt(this.total))%(parseInt(this.userparam.pagesize)))? ((parseInt(this.total))/(parseInt(this.userparam.pagesize))+1) : ((parseInt(this.total))/(parseInt(this.userparam.pagesize))));
				//console.log("totalpages: ",totalpages)
				this.totalpage = totalpages;
			},
			pageBack() {
				this.userparam.pagenum = ((this.userparam.pagenum - 1)<1) ? 1 : (this.userparam.pagenum - 1);
			},
			pageForward() {
				this.userparam.pagenum = ((this.userparam.pagenum + 1)>this.totalpage) ? this.totalpage : (this.userparam.pagenum + 1);
			},
			enDisLeft() {
				if(this.userparam.pagenum == 1){
					this.leftfb = true;
				}else{
					this.leftfb = false;
				}
			},
			enDisRight() {
				if(this.userparam.pagenum == this.totalpage){
					this.rightfb = true;
				}else{
					this.rightfb = false;
				}
			},
			jumpToPage(n) {
				this.userparam.pagenum = n;
			},
			searchPerson() {
				if(this.searchword != ""){
					this.$http.get('users', {params: {"query": "","pagenum": 1,"pagesize": this.total}}).then(res => {
						this.totaluserlist = res.data.data.users;
						var pattern = new RegExp(this.searchword,"i");
						this.userslist = [];
						this.totaluserlist = this.totaluserlist.map((item,index,arr) => {
							var name = item["username"];
							if(pattern.test(name)){
								this.userslist.push(item)
								return item
							}
						})
					})
				}else{return;}
				
			},
			showOff() {
				console.log('searchword: ',this.searchword)
				if(this.searchword != ""){
					this.isoffshow = true;
					//console.log("showoff++++++++++")
				}else {
					this.isoffshow = false;
					//console.log("showoff--------------")
				}
			},
			closeOff() {
				this.isoffshow = false;
			},
			turnOffSearch() {
				this.searchword = "";
				this.getUsers();
			},
			addUser() {
				this.isadduser = true;
			},
			usrNameCheck() {
				if(this.addUserForm.username == ""){
					this.usrnamewarn = "请输入用户名";
					console.log(this.addUserForm.username.length)
					//this.isnameerr = true;
				}else if(this.addUserForm.username.length < 3 || this.addUserForm.username.length > 10){
					this.usrnamewarn = "长度在3到10个字符";
					//this.isnameerr = true;
				}else{
					this.usrnamewarn = "";
					//this.isnameerr = false;
				}
			},
			pswCheck() {
				if(this.addUserForm.password == ""){
					this.pswwarn = "请输入密码";
					console.log(this.addUserForm.password.length)
					//this.isnameerr = true;
				}else if(this.addUserForm.password.length < 6 || this.addUserForm.password.length > 10){
					this.pswwarn = "长度在6到10个字符";
					//this.isnameerr = true;
				}else{
					this.pswwarn = "";
					//this.isnameerr = false;
				}
			},
			emailCheck() {
				if(this.addUserForm.email == ""){
					this.emailwarn = "请输入邮箱";
					console.log("email length: ", this.addUserForm.email.length)
					//this.isnameerr = true;
				}else if(this.addUserForm.email.length < 7 || this.addUserForm.email.length > 15){
					this.emailwarn = "长度在7到15个字符";
					//this.isnameerr = true;
				}else{
					this.pswwarn = "";
					//this.isnameerr = false;
				}
			},
			telCheck() {
				if(this.addUserForm.mobile == ""){
					this.telwarn = "请输入手机";
					console.log(this.addUserForm.mobile.length)
					//this.isnameerr = true;
				}else if(this.addUserForm.mobile.length < 7 || this.addUserForm.mobile.length > 11){
					this.telwarn = "长度在7到11个字符";
					//this.isnameerr = true;
				}else{
					this.telwarn = "";
					//this.isnameerr = false;
				}
			},
			closeCard() {
				this.addUserForm.username = "";
				this.addUserForm.password = "";
				this.addUserForm.email = "";
				this.addUserForm.mobile = "";
				this.isadduser = false;
			},
			submitUser() {
				this.usrNameCheck();
				this.pswCheck();
				this.emailCheck();
				this.telCheck();
				if(this.addUserForm.username != "" && this.usrnamewarn == "" && this.addUserForm.password != "" && this.pswwarn == "" && this.addUserForm.email != "" && this.emailwarn == "" && this.addUserForm.mobile != "" && this.telwarn == ""){
					console.log("this.username:",this.addUserForm.username)
					this.$http.post('users',this.addUserForm).then(res => {
						if(res.data.meta.status == 201){
							this.closeCard();
							this.getUsers();
							this.diagmessage = "添加用户成功";
							this.isshowdiag = true;
							var that = this;
							setTimeout(function(){ 
								that.isshowdiag = false;
								console.log("add user suss")
							},5000);
							this.addUserForm.username = "";
							this.addUserForm.password = "";
							this.addUserForm.email = "";
							this.addUserForm.mobile = "";
						}
						console.log(res)
					})
				}
			}
		}
	}
</script>

<style scoped>
	.userpage {
		width: 100%;
		height: 100%;
	}
	.users {
		font-size: 15px;
		/* float: left; */
	}
	.users form {
		width: 100%;
		margin-top: 20px;
		padding: 20px;
		box-sizing: border-box;
		box-shadow: 0 0 15px gainsboro;
		border-radius: 5px;
	}
	.users form div {
		overflow: hidden;
		margin-bottom: 20px;
		display: inline-block;
		float: left;
	}
	.users form div input {
		width: 300px;
		height: 20px;
		padding: 10px;
		border: 1px solid gainsboro;
		border-right: none;
		border-left-radius: 4px;
		float: left;
	}
	.users form div .shortinput {
		width: 260px;
	}
	.users form div .offsearch {
		width: 40px;
		height: 40px;
		border: 1px solid gainsboro;
		border-left: none;
		border-right: none;
		text-align: center;
		float: left;
		background: url(../assets/close.png) no-repeat center center;
		margin: 0px;
	}
	.users form div button {
		width: 60px;
		height: 40px;
		box-sizing: content-box;
		border: 1px solid gainsboro;
		background: #F5F7FA url(../../../img/search.png) no-repeat center center;
		background-size: 20px 20px;
		float: left;
	} 
	.users form .add {
		float: left;
		width: 100px;
		height: 42px;
		margin: 0px 0px 20px 30px;
		background: #66B1FF;
		color: #FFF;
		border: none;
		border-radius: 5px;
	}
	.users form .add:focus {
		outline: none;
	}
	.users form table {
		border-collapse: collapse;
		width: 100%;
		color: #909399;
	}
	.users form table tr {
		height: 50px;
	}
	.users form table td,th {
		border: 1px solid #EBEEF5;
		width: 16%;
		padding: 10px;
		box-sizing: border-box;
		text-align: left;
	}
	.users form table td {
		color: #606266;	
	}
	.users form table th:first-child {
		width: 5%;
	}
	.users form table th:last-child {
		width: 15%;
	}
	.users form table td:first-child {
		width: 5%;
	}
	.users form table td:last-child {
		width: 15%;
	}
	.users form table tbody tr:nth-of-type(even) {
		background: #FAFAFA;
	}
	.users form table tbody tr:hover {
		background: #F8F8F8;
	}
	.users form p {
		margin-top: 10px;
	}
	.users form p select {
		width: 100px;
		htight: 20px;
		padding: 5px;
		box-sizing: border-box;
		margin: 0 30px 0 10px;
	}
	.users form p .select1 {
		display: inline-block;
		list-style: none;
		white-space: nowrap;
		font-size: 13px;
		cursor: pointer;
		color: #303133;
		/* margin: 0 20px; */
	}
	.users form p .select1 li {
		display: inline-block;
		white-space: pre-wrap;
	}
	.users form p .select1 span {
		white-space: pre-wrap;
	}
	.users form p .select1 span:hover, li:hover {
		color: #409EFF;
	}
	.users form p .select1 .disleft {
		/* cursor: url(../assets/forbid.cur); */
		cursor: help;
	}
	.users form p .select1 .disright {
		/* cursor: url(../assets/forbid.cur); */
		cursor: help;
	}
	.users form p .select2 {
		margin-left: 30px;
	}
	.users form p .select2 input {
		width: 40px;
		height: 25px;
		padding: 5px 10px;
		box-sizing: border-box;
		border: 1px solid gainsboro;
		border-radius: 10%;
		text-align: center;
		margin: 0 3px;
	}
	.mask {
		width: 100%;
		height: 100%;
		background: rgba(0,0,0,0.5);
		position: fixed;
		top: 0;
		left: 0;
	}
	.mask .card {
		margin: 150px auto;
		background: #FFF;
		width: 800px;
		height: 450px;
		padding: 20px;
		box-sizing: border-box;
		border-radius: 2px;
		position: relative;
		/* overflow: hidden; */
	}
	.mask .card .title {
		font-size: 18px;
		margin-bottom: 40px;
		color: #101010;
	}
	.mask .card .title .closecard {
		float: right;
	}
	.mask .card .title .closecard img {
		width: 20px;
		height: 20px;
	}
	.mask .card .inputlist {
		margin-left: 50px;
		padding: 0px;
	}
	.mask .card .inputlist .iptitem {
			//margin-bottom: 10px;
			display: flex;
			align-items: center;
	}
	.mask .card .inputlist .iptitem div {
			overflow: hidden;
			width: 60px;
			margin-right: 10px;
	}	
	.mask .card .inputlist .iptname {
		font-size: 13px;
		float: right;
	}
	.mask .card .inputlist .icon {
		color: red;
		float: right;
		margin-right: 5px;
	}
	.mask .card .inputlist input {
		width: 640px;
		height: 40px;
		//position: absolute;
		//left: 100px;
		border: 1px solid gainsboro;
		border-radius: 5px;
		padding-left: 20px;
	}
	.mask .card .inputlist .namewarnword, .pswwarnword, .emailwarnword, .telwarnword {
		display: block;
		height: 20px;
		line-height: 20px;
		font-size: 8px;
		color: red;
		margin-left: 70px;
	}
	.mask .card .btns {
		margin-top: 40px;
		float: right;
	}
	.mask .card .btns .cancle {
		width: 70px;
		height: 40px;
		border: 1px solid gainsboro;
		border-radius: 3px;
		background: #FFF;
		margin-right: 10px;
	}
	.mask .card .btns .commit {
		width: 70px;
		height: 40px;
		border: 1px solid #66B1FF;
		border-radius: 3px;
		background: #66B1FF;
		color: #FFF;
	}
	.diag {
		width: 400px;
		height: 50px;
		line-height: 30px;
		font-size: 16px;
		padding: 10px 20px;
		box-sizing: border-box;
		position: fixed;
		left: 50%;
		border-radius: 6px;
		animation: showdiag 5s linear;
	}
	@keyframes showdiag{
		0% {background: transparent; color: transparent; top: 0px;}
		10% {background: #F0F9EB; color: #67C23A; top: 30px;}
		90% {background: #F0F9EB; color: #67C23A; top: 30px;}
		100% {background: transparent; color: transparent; top: 0px;}
	}
</style>
