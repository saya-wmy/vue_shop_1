<template>
	<div class="homebox">
		<header>
			<img src="../assets/ajax.png" />
			<span class="title">电商后台管理系统</span>
			<button @click="backToLogin">返回</button>
		</header>
		<section>
			<aside :class="{closelist: isClose}" >
				<div class="switch" @click="turnOnOffList" >| | |</div>
				<ul>
					<li v-for="(item,index) in menulist"  @mouseenter="showAsideChildList(index)" @mouseleave="closeAsideChildList(index)" >
							<div @click="arrowChange(index)">
								<img src="../assets/icon-user.png" >
								<span v-if="!isClose" class="name" >{{item.authName}}</span>
								<span  v-if="!isClose" :class="{arrowUp: isArrowUp[index], arrowDown: isArrowDown[index] }" ></span>
							</div>
							<ul v-if="isArrowUp[index]" :class="{asidechildlist: isasidechildlist, childlist: ischildlist}" >
									<li v-for="(child, i) in item.children">
										<router-link :to="'/'+child.path" :class="{clikeditem:cliked[index][i]}" @click.native="changeToCliked(index,i)" >
											<img src="../assets/icon-user.png" ><span class="childName">{{child.authName}}</span>	
										</router-link>	
									</li>
							</ul>
					</li>
				</ul>
			</aside>
			<article>
				<router-view></router-view>
			</article>
		</section>
	</div>
</template>

<script>
	export default{
		data() {
			return {
				menulist: [],
				isArrowDown: [],
				isArrowUp: [],
				isOpen: true,
				isClose: false,
				isasidechildlist: false,
				ischildlist: true,
				childrenLen: [],
				cliked: []
			}
		},
		created() {
			this.$http.get('menus').then(res => {
				this.menulist = res.data.data;
				var menulength = this.menulist.length;
				
				for(let i=0; i < menulength; i++){
					var childlength = this.menulist[i].children.length;
					this.childrenLen.push(childlength);
					this.isArrowDown[i] = true;
					this.isArrowUp[i] = false;
					this.cliked.push([])
					for(let j=0; j < childlength; j++){
						this.cliked[i].push(false);
					}	
				}
				console.log("clikedBefore: ",this.cliked)
				console.log(this.menulist)
				
			})
		},
		methods: {
			backToLogin() {
				this.$router.push('/login');
			},
			arrowChange(index) {				
				this.$forceUpdate();
				this.$set(this.isArrowDown,index,!this.isArrowDown[index]);
				this.$set(this.isArrowUp,index,!this.isArrowUp[index]);
				
				// this.isArrowDown[index] = !this.isArrowDown[index];
				// this.isArrowUp[index] = !this.isArrowUp[index];
				// console.log("this.isArrowDown[ " + index + "]: " + this.isArrowDown[index]);
				// console.log("this.isArrowUp[ " + index + "]: " + this.isArrowUp[index])
			},
			turnOnOffList() {
				// this.isOpen = !this.isOpen;
				this.isClose = !this.isClose;
				if(this.isClose == false){
					this.isasidechildlist = false;
					this.ischildlist = true;
				}
				if(this.isClose == true){
					this.isArrowDown = this.isArrowDown.map(val => {
						val = true;
						return val
					});
					this.isArrowUp = this.isArrowUp.map(val => {
						val = false;
						return val
					})
				}	
			},
			showAsideChildList(index) {
				if(this.isClose == true){
					this.isasidechildlist = true;
					this.ischildlist = false;
					this.$forceUpdate();
					this.$set(this.isArrowUp,index,true);
				}
			},
			closeAsideChildList(index) {
				if(this.isClose == true){
					this.$forceUpdate();
					this.$set(this.isArrowUp,index,false);
				}
			},
			changeToCliked(index,i) {
				this.cliked = this.cliked.map((item,num,arr) => {
					return arr.map((item1,num1,arr1) => {
						if(num == index && num1 == i){
							return true;
						}else{
							return false;
						}
					});
				});
				//console.log("clikedAfter: ",this.cliked)
			}
		}
	}
</script>

<style scoped="">
	.homebox {
		height: 100%;
		width: 100%;
	}
	header {
		height: 6%;
		width: 100%;
		background: #373C41;
		color: #fff;
		font-size: 1.5rem;
		position: relative;
		margin-bottom: 0;
	}
	header img, .title, button {
		position: absolute;
		top: 50%;
		transform: translateY(-50%);
	}
	header .title{
		margin-left: 40px;
	}
	header button {
		width: 70px;
		height: 40px;
		border-radius: 3px;
		border: none;
		background: #A6A9AD;
		color: #fff;
		right: 20px;
	}
	section {
		height: 94%;
	}
	aside, .closelist {
		float: left;
		width: 4%;
		height: 100%;
		background: #323744;
		color: #fff;
	}
	aside {
		float: left;
		width: 13%;
		height: 100%;
		background: #323744;
		color: #fff;
	}
	aside .switch {
		height: 3%;
		box-sizing: border-box;
		background: #485164;
		text-align: center;
		font-size: 0.5rem;
		padding: 5px;
	}

	aside ul {
		list-style: none;
		padding: 0px;
		margin: 0px;
	}
	aside li {
		/* height: 3.5rem; */
		font-size: 0.9rem;
	}
	aside ul li img {
		width: 20px;
		height: 20px;
		margin-right: 15px;
		background: #fff;
	}
	aside ul li div {
		width: 100%;
		height: 3.5rem;
		box-sizing: border-box;
		display: flex;
		padding-left: 20px;
		align-items: center;
	}
	aside ul li div:hover {
		background: #282C36;
	}
	aside ul li div .arrowDown {
		border-top: 10px solid #fff;
		border-left: 5px solid transparent;
		border-right: 5px solid transparent;
		margin-left: 55px;
	}
	aside ul li div .arrowUp {
		border-bottom: 10px solid #fff;
		border-left: 5px solid transparent;
		border-right: 5px solid transparent;
		margin-left: 55px;
	}
	
	aside ul li .childlist li {
		height: 3.5rem;	
	}
	aside ul li ul li:hover {
		background: #282C36;
	}
	aside ul li .childlist li a {
		width: 100%;
		height: 100%;
		padding-left: 45px;
		display: flex;
		align-items: center;
		text-decoration: none;
		color: #fff;
	}
	aside ul li .asidechildlist {
		position: absolute;
		left: 4%;
		transform: translateY(-3.5rem);
		width: 13%;
		padding-left: 5px;
		background: #fff;
		color: #fff;
	}
	aside ul li .asidechildlist li {
		height: 4rem;
		background: #323744;
	}
	aside ul li .asidechildlist li a {
		height: 100%;
		width: 100%;
		padding-left: 25px;
		display: flex;
		align-items: center;
		text-decoration: none;
		color: #fff;
	}
	aside ul li ul li .clikeditem {
		color: #0074D9;
	}
	aside ul li ul li .clikeditem img {
		background: #0074D9;
	}
	article {
		float: left;
		width: 87%;
		height: 100%;
	}
</style>
