<template>
	<div>
		<div class="container" :style='{"minHeight":"100vh","alignItems":"center","background":"url(http://codegen.caihongy.cn/20231128/5a94f436dc0b45ac9c63ac27350c72e9.webp)","display":"flex","width":"100%","backgroundSize":"cover","backgroundPosition":"center center","backgroundRepeat":"no-repeat","justifyContent":"center"}'>
			<el-form v-if="pageFlag=='register'" :style='{"padding":"20px","boxShadow":"0 1px 6px rgba(64, 158, 255, .8)","backdrop-filter":"blur(6px)","margin":"50px 0","borderRadius":"10px","background":"rgba(255, 255, 255, 0.3)","width":"600px","height":"auto"}' ref="rgsForm" class="rgs-form" :model="rgsForm" :rules="rules">
				<div v-if="true" :style='{"margin":"0 0 10px 0","color":"#000","textAlign":"center","width":"100%","lineHeight":"64px","fontSize":"20px","fontWeight":"600"}' class="title">荣誉证书管理系统设计与实现注册</div>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('xuehao')?'required':''">学号：</div>
					<el-input  v-model="ruleForm.xuehao"  autocomplete="off" placeholder="学号"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('mima')?'required':''">密码：</div>
					<el-input  v-model="ruleForm.mima"  autocomplete="off" placeholder="密码"  type="password"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('mima')?'required':''">确认密码：</div>
					<el-input  v-model="ruleForm.mima2" autocomplete="off" placeholder="确认密码" type="password" />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('xueshengxingming')?'required':''">学生姓名：</div>
					<el-input  v-model="ruleForm.xueshengxingming"  autocomplete="off" placeholder="学生姓名"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('touxiang')?'required':''">头像：</div>
                    <file-upload
                        tip="点击上传头像"
                        action="file/upload"
                        :limit="3"
                        :multiple="true"
                        :fileUrls="ruleForm.touxiang?ruleForm.touxiang:''"
                        @change="xueshengtouxiangUploadChange"
                    ></file-upload>
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('xingbie')?'required':''">性别：</div>
                    <el-select v-model="ruleForm.xingbie" placeholder="请选择性别" >
                        <el-option
                            v-for="(item,index) in xueshengxingbieOptions"
                            v-bind:key="index"
                            :label="item"
                            :value="item">
                        </el-option>
                    </el-select>
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('shoujihaoma')?'required':''">手机号码：</div>
					<el-input  v-model="ruleForm.shoujihaoma"  autocomplete="off" placeholder="手机号码"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='xuesheng'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('banji')?'required':''">班级：</div>
					<el-input  v-model="ruleForm.banji"  autocomplete="off" placeholder="班级"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('jiaoshigonghao')?'required':''">教师工号：</div>
					<el-input  v-model="ruleForm.jiaoshigonghao"  autocomplete="off" placeholder="教师工号"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('mima')?'required':''">密码：</div>
					<el-input  v-model="ruleForm.mima"  autocomplete="off" placeholder="密码"  type="password"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('mima')?'required':''">确认密码：</div>
					<el-input  v-model="ruleForm.mima2" autocomplete="off" placeholder="确认密码" type="password" />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('touxiang')?'required':''">头像：</div>
                    <file-upload
                        tip="点击上传头像"
                        action="file/upload"
                        :limit="3"
                        :multiple="true"
                        :fileUrls="ruleForm.touxiang?ruleForm.touxiang:''"
                        @change="jiaoshitouxiangUploadChange"
                    ></file-upload>
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('jiaoshixingming')?'required':''">教师姓名：</div>
					<el-input  v-model="ruleForm.jiaoshixingming"  autocomplete="off" placeholder="教师姓名"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('xingbie')?'required':''">性别：</div>
                    <el-select v-model="ruleForm.xingbie" placeholder="请选择性别" >
                        <el-option
                            v-for="(item,index) in jiaoshixingbieOptions"
                            v-bind:key="index"
                            :label="item"
                            :value="item">
                        </el-option>
                    </el-select>
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('lianxidianhua')?'required':''">联系电话：</div>
					<el-input  v-model="ruleForm.lianxidianhua"  autocomplete="off" placeholder="联系电话"  type="text"  />
				</el-form-item>
				<el-form-item :style='{"width":"80%","padding":"0","margin":"0 auto 5px","height":"auto"}' class="list-item" v-if="tableName=='jiaoshi'">
					<div v-if="true" :style='{"width":"100%","lineHeight":"44px","fontSize":"14px","position":"relative","color":"#222"}' class="lable" :class="changeRules('banji')?'required':''">班级：</div>
					<el-input  v-model="ruleForm.banji"  autocomplete="off" placeholder="班级"  type="text"  />
				</el-form-item>
				<button :style='{"border":"0","cursor":"pointer","padding":"0 10px","margin":"20px auto 5px","outline":"none","color":"#fff","borderRadius":"8px","background":"#385d86","display":"block","width":"80%","fontSize":"16px","height":"44px"}' type="button" class="r-btn" @click="login()">注册</button>
				<div :style='{"cursor":"pointer","padding":"0 10%","margin":"20px 0 0","color":"#385d86","textAlign":"center","display":"block","lineHeight":"2","fontSize":"16px","textDecoration":"underline"}' class="r-login" @click="close()">已有账号，直接登录</div>
			</el-form>
			
		</div>
	</div>
</template>

<script>
export default {
	data() {
		return {
			ruleForm: {
			},
			forgetForm: {},
            pageFlag : '',
			tableName:"",
			rules: {},
            xueshengxingbieOptions: [],
            jiaoshixingbieOptions: [],
		};
	},
	mounted(){
		this.pageFlag = this.$route.query.pageFlag
		if(this.$route.query.pageFlag=='register'){
			
			let table = this.$storage.get("loginTable");
			this.tableName = table;
			if(this.tableName=='xuesheng'){
				this.ruleForm = {
					xuehao: '',
					mima: '',
					xueshengxingming: '',
					touxiang: '',
					xingbie: '',
					shoujihaoma: '',
					banji: '',
				}
			}
			if(this.tableName=='jiaoshi'){
				this.ruleForm = {
					jiaoshigonghao: '',
					mima: '',
					touxiang: '',
					jiaoshixingming: '',
					xingbie: '',
					lianxidianhua: '',
					banji: '',
				}
			}
			if ('xuesheng' == this.tableName) {
				this.rules.xuehao = [{ required: true, message: '请输入学号', trigger: 'blur' }]
			}
			if ('xuesheng' == this.tableName) {
				this.rules.mima = [{ required: true, message: '请输入密码', trigger: 'blur' }]
			}
			if ('xuesheng' == this.tableName) {
				this.rules.xueshengxingming = [{ required: true, message: '请输入学生姓名', trigger: 'blur' }]
			}
			if ('jiaoshi' == this.tableName) {
				this.rules.jiaoshigonghao = [{ required: true, message: '请输入教师工号', trigger: 'blur' }]
			}
			if ('jiaoshi' == this.tableName) {
				this.rules.mima = [{ required: true, message: '请输入密码', trigger: 'blur' }]
			}
			this.xueshengxingbieOptions = "男,女".split(',')
			this.jiaoshixingbieOptions = "男,女".split(',')
		}
	},
	created() {
	},
	destroyed() {
		  	},
	methods: {
		changeRules(name){
			if(this.rules[name]){
				return true
			}
			return false
		},
		// 获取uuid
		getUUID () {
			return new Date().getTime();
		},
		close(){
			this.$router.push({ path: "/login" });
		},
        xueshengtouxiangUploadChange(fileUrls) {
            this.ruleForm.touxiang = fileUrls;
        },
        jiaoshitouxiangUploadChange(fileUrls) {
            this.ruleForm.touxiang = fileUrls;
        },

        // 多级联动参数


		// 注册
		login() {
			var url=this.tableName+"/register";
					if((!this.ruleForm.xuehao) && `xuesheng` == this.tableName){
						this.$message.error(`学号不能为空`);
						return
					}
					if((!this.ruleForm.mima) && `xuesheng` == this.tableName){
						this.$message.error(`密码不能为空`);
						return
					}
					if((this.ruleForm.mima!=this.ruleForm.mima2) && `xuesheng` == this.tableName){
						this.$message.error(`两次密码输入不一致`);
						return
					}
					if((!this.ruleForm.xueshengxingming) && `xuesheng` == this.tableName){
						this.$message.error(`学生姓名不能为空`);
						return
					}
            if(this.ruleForm.touxiang!=null) {
                this.ruleForm.touxiang = this.ruleForm.touxiang.replace(new RegExp(this.$base.url,"g"),"");
            }
					if(`xuesheng` == this.tableName && this.ruleForm.shoujihaoma &&(!this.$validate.isMobile(this.ruleForm.shoujihaoma))){
						this.$message.error(`手机号码应输入手机格式`);
						return
					}
					if((!this.ruleForm.jiaoshigonghao) && `jiaoshi` == this.tableName){
						this.$message.error(`教师工号不能为空`);
						return
					}
					if((!this.ruleForm.mima) && `jiaoshi` == this.tableName){
						this.$message.error(`密码不能为空`);
						return
					}
					if((this.ruleForm.mima!=this.ruleForm.mima2) && `jiaoshi` == this.tableName){
						this.$message.error(`两次密码输入不一致`);
						return
					}
            if(this.ruleForm.touxiang!=null) {
                this.ruleForm.touxiang = this.ruleForm.touxiang.replace(new RegExp(this.$base.url,"g"),"");
            }
					if(`jiaoshi` == this.tableName && this.ruleForm.lianxidianhua &&(!this.$validate.isMobile(this.ruleForm.lianxidianhua))){
						this.$message.error(`联系电话应输入手机格式`);
						return
					}
			this.$http({
				url: url,
				method: "post",
				data:this.ruleForm
			}).then(({ data }) => {
				if (data && data.code === 0) {
					this.$message({
						message: "注册成功",
						type: "success",
						duration: 1500,
						onClose: () => {
							this.$router.replace({ path: "/login" });
						}
					});
				} else {
					this.$message.error(data.msg);
				}
			});
		}
	}
};
</script>

<style lang="scss" scoped>
	.container {
	  position: relative;
	  background: url(http://codegen.caihongy.cn/20231128/5a94f436dc0b45ac9c63ac27350c72e9.webp);

		.el-date-editor.el-input {
		  width: 100%;
		}
		
		.rgs-form .el-input /deep/ .el-input__inner {
						border: 1;
						border-radius: 8px;
						padding: 0 10px;
						outline: none;
						color: #000;
						background: #fff;
						width: 100%;
						font-size: 14px;
						height: 44px;
					}
		
		.rgs-form .el-select /deep/ .el-input__inner {
						border: 1;
						border-radius: 8px;
						padding: 0 10px;
						outline: none;
						color: #000;
						background: #fff;
						width: 288px;
						font-size: 14px;
						height: 44px;
					}
		
		.rgs-form .el-date-editor /deep/ .el-input__inner {
						border: 1;
						border-radius: 8px;
						padding: 0 10px 0 30px;
						outline: none;
						color: #000;
						background: #fff;
						width: 288px;
						font-size: 14px;
						height: 44px;
					}
		
		.rgs-form .el-date-editor /deep/ .el-input__inner {
						border: 1;
						border-radius: 8px;
						padding: 0 10px 0 30px;
						outline: none;
						color: #000;
						background: #fff;
						width: 288px;
						font-size: 14px;
						height: 44px;
					}
		
		.rgs-form /deep/ .el-upload--picture-card {
			background: transparent;
			border: 0;
			border-radius: 0;
			width: auto;
			height: auto;
			line-height: initial;
			vertical-align: middle;
		}
		
		.rgs-form /deep/ .upload .upload-img {
		  		  border: 0px dashed #385d86;
		  		  cursor: pointer;
		  		  border-radius: 8px;
		  		  color: #385d86;
		  		  background: #fff;
		  		  width: 100px;
		  		  font-size: 32px;
		  		  line-height: 100px;
		  		  text-align: center;
		  		  height: 100px;
		  		}
		
		.rgs-form /deep/ .el-upload-list .el-upload-list__item {
		  		  border: 0px dashed #385d86;
		  		  cursor: pointer;
		  		  border-radius: 8px;
		  		  color: #385d86;
		  		  background: #fff;
		  		  width: 100px;
		  		  font-size: 32px;
		  		  line-height: 100px;
		  		  text-align: center;
		  		  height: 100px;
		  		}
		
		.rgs-form /deep/ .el-upload .el-icon-plus {
		  		  border: 0px dashed #385d86;
		  		  cursor: pointer;
		  		  border-radius: 8px;
		  		  color: #385d86;
		  		  background: #fff;
		  		  width: 100px;
		  		  font-size: 32px;
		  		  line-height: 100px;
		  		  text-align: center;
		  		  height: 100px;
		  		}
	}
	.required {
		position: relative;
	}
	.required::after{
				color: red;
				left: -10px;
				position: absolute;
				content: "*";
			}
	.editor>.avatar-uploader {
		line-height: 0;
		height: 0;
	}
</style>
