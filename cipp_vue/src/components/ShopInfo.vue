<template>
	<div name="InfoContent">
		<div class="Limit" style="margin-top: 30px;">
			<el-row>
			  <el-col :span="18">
				  <el-card class="box-card">
				    <div class="cardTitle">
						{{list.title}}
					</div>
					<div class="cardShow">
						 <span class="iconfont">&#xe612; {{list.show}}</span>
						 <span class="iconfont">&#xe629; {{list.good}}</span>
					</div>
					<div class="block">
					    <el-carousel height="480px">
					      <el-carousel-item v-if="list.img" v-for="(item,index) in (list.img)" :key="index">
								<img :src="item" alt="" style="width: 100%;height: 100%;">
					      </el-carousel-item>
					    </el-carousel>
					</div>
					 <mavon-editor v-model="list.content" :boxShadow="false" previewBackground="#fff" :toolbarsFlag="false" :subfield="false" defaultOpen="preview"/>
					<div class="cardGood">
						<el-row>
							<el-col :span="8">
							  <div class="Center">
								<el-button icon="el-icon-warning"> 举报</el-button>
							  </div>
							</el-col>
							<el-col :span="8">
							  <div class="Center isGood">
								<el-button @click="sendLike" type="primary"><span class="iconfont">&#xe629; 点赞</span></el-button>
							  </div>
							</el-col>
							<el-col :span="8">
								<div class="Center">
								  <el-button  icon="el-icon-share"> 分享</el-button>
								</div>
							</el-col>
						  
						</el-row>
					</div>
					<div class="comment">
						<el-form ref="form" :model="form">
						  <el-form-item>
						    <el-input v-model="form.commentContent" placeholder="说说你的看法"></el-input>
						  </el-form-item>
						  <el-form-item>
							<div class="sendBtn">
								<el-button type="primary" @click="onSubmit">立即发送</el-button>
							</div>
						  </el-form-item>
						</el-form>
						<div class="commentList">
							<div class="commentListHeader">
								<span>全部评论</span>
								<span>{{commitLen}}条</span>
							</div>
							<ul>
								<li class="commentLi" v-for="(i,j) in list.commentList" :key="j">
									<el-row>
									  <el-col :span="2">
										  <div class="commentLeft">
											  <img :src="i.img" alt="">
										  </div>
									  </el-col>
									  <el-col :span="22">
										  <div class="commentRight">
												<p>{{i.name}}</p>
												<span>{{i.content}}</span>
										  </div>
									  </el-col>
									</el-row>
								</li>
								
							</ul>
						</div>
					</div>
				  </el-card>
				</el-col>
				<el-col :span="6">
				  <el-card>
				    <div class="userInfo">
						
							<img :src="list.useImg" alt="">
							<h3>{{list.name}}</h3>
							<div class="userLable">
								<el-tag type="success">{{list.rz}}</el-tag>
							</div>
							<div>
								<el-button type="primary" size="small" :data-id="list.id">关注</el-button>
							</div>
						
					</div>
				  </el-card>
				  <div style="height: 10px;"></div>
				  <el-card>
				    <div class="activity">
				  		<h2>商品参数</h2>
						<ul>
							<li>
								<el-row>
								  <el-col :span="8">
									  <p>商品标题：</p>
								  </el-col>
								  <el-col :span="16">
									 <p> {{list.title}}</p>
								  </el-col>
								</el-row>
							</li>
							<li>
								<el-row>
								  <el-col :span="8">
									  <p>商品简介：</p>
								  </el-col>
								  <el-col :span="16">
									 <p> {{list.info}}</p>
								  </el-col>
								</el-row>
							</li>
							<li>
								<el-row>
								  <el-col :span="8">
									  <p>商品价格：</p>
								  </el-col>
								  <el-col :span="16">
									 <p> {{list.price}}元</p>
								  </el-col>
								</el-row>
							</li>
							<li>
								<el-row>
								  <el-col :span="8">
									  <p>商品地址：</p>
								  </el-col>
								  <el-col :span="16">
									 <p> {{list.trading}}</p>
								  </el-col>
								</el-row>
							</li>
						</ul>
				  	</div>
				  </el-card>
				</el-col>
			</el-row>
		</div>
	</div>
</template>

<script>
	export default{
		name:"InfoContent",
		data(){
			return {
				banner:[],
				value:"",
				list:{
					
				},
				userInfo:{},
				form:{
					commentContent:""
				}
			}
		},
		methods:{
			//点赞
			sendLike(e){
				var that =this;
				this.$http.get("/api/likeid?id="+this.list.id+"&session="+this.userInfo.session).then(function(e){
					if(e.data.code==1){
						that.$message.success("点赞成功")
					}else{
						that.$message.success("取消赞成功")
					}
				})
			},
			onSubmit() {
				console.log(this.userInfo)
				var data= {
					id:this.list.id,
					session:this.userInfo.session,
					comment:this.form.commentContent
				}
				console.log(data)
				var that = this;
				this.$http.post("/api/insertMarketComment",data).then(function(e){
					if(e.data.code==1){
						that.$message.success("评论成功");
						
						var com={
							name:that.userInfo.name,
							img:that.userInfo.img,
							content:that.form.commentContent
						}
						that.list.commentList.push(com)
						that.$forceUpdate()
						that.form.commentContent="";
					
					}
				})
			}
		},
		components:{
			
		},
		computed:{
			 commitLen() {
				 if(this.list.commentList!=undefined){
					 return this.list.commentList.length
				 }else{
					 return 0
				 }
			    
			  }
		},
		beforeCreate() {
			
		},
		mounted(){
			var that = this;
			var userInfo=JSON.parse(localStorage.getItem("userInfo"));
			this.userInfo=userInfo;
			console.log(this.userInfo)
			this.$http.get("/api/marketInfo?id="+that.$route.query.id).then(function(res){
				that.list=res.data.info;
				that.list.commitLen=that.list.commentList.length;
				console.log(that.list)
				that.$forceUpdate()
			})
			
		}
	}
</script>

<style lang="stylus" scoped>
	.userInfo
		margin-bottom 20px
		text-align center
		.userLable
			height 50px
		h3
			line-height 40px
		img
			width 60px
			height 60px
			border-radius 50%
	.activity
		li
			height 30px
			color #818181;
		p
			font-size 14px
			overflow: hidden; text-overflow:ellipsis; white-space: nowrap;
		h2
			margin-bottom 20px
	.commentList
		.commentLi
			border-bottom 1px solid #f1f1f1
			margin-bottom 15px
		.commentListHeader
			width 100%
			height 60px
			font-size 16px
			color #aaa
			
		.commentLeft
			width 100%
			height 75px
			img
				width 60px
				height 60px
				border-radius 50%
		.commentRight
			margin-left 7px
			p
				line-height 30px
				color #444
				font-size 16px
			span
				color #888
	.sendBtn button
		float right
	.cardShow
		text-align right
		line-height 30px
		font-size 14px
		color #aaa
		margin-bottom 10px
		span
			padding 0 20px
	.box-card
		width 900px
		min-height 1000px
	.cardTitle
		font-size 20px
	.cardGood
		width 100%
		height 60px
		margin 20px
</style>
