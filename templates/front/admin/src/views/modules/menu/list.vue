<template>
	<div class="main-content" :style='{"padding":"30px"}'>
		<!-- 列表页 -->
		<template v-if="showFlag">
			<el-form class="center-form-pv" :style='{"margin":"0 0 20px"}' :inline="true" :model="searchForm">
				<el-row :style='{"display":"block"}' >
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}' class="select">
						<label :style='{"margin":"0 10px 0 0","color":"#666","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"500","height":"40px"}' class="item-label">角色</label>
						<el-select v-model="roleName" placeholder="请选择角色">
							<el-option v-for="(item,index) in roleNameList" v-bind:key="index" :label="item" :value="item"></el-option>
						</el-select>
					</div>
					<el-button class="search" type="success" @click="search()">
						<span class="icon iconfont icon-xihuan" :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
						查询
					</el-button>
				</el-row>

				<el-row class="actions" :style='{"flexWrap":"wrap","margin":"20px 0","display":"flex"}'>



				</el-row>
			</el-form>
			<div :style='{"width":"100%","padding":"10px"}'>
				<el-table class="tables"
					:stripe='true'
					:style='{"width":"100%","padding":"0","borderColor":"#eee","borderStyle":"solid","borderWidth":"1px 0 0 0","background":"#fff"}' 
					:border='true'
					v-if="isAuth('menu','查看')"
					:data="backMenuList"
					:tree-props="{children: 'child'}"
					row-key="id"
					default-expand-all
					v-loading="dataListLoading"
				@selection-change="selectionChangeHandler">
					<!--<el-table-column :resizable='false' :sortable='false' prop="parentMenuName" label="一级菜单">
						<template slot-scope="scope">
							{{scope.row.parentMenuName}}
						</template>
					</el-table-column>-->
					<el-table-column :resizable='false' :sortable='false' prop="menu" header-align="center" label="菜单名称">
						<template slot-scope="scope">
							{{scope.row.menu}}
						</template>
					</el-table-column>
					<el-table-column width="300" label="操作">
						<template slot-scope="scope">
							<el-button class="edit" v-if=" isAuth('menu','修改') " type="success" @click="addOrUpdateHandler(scope.row.id)">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								修改
							</el-button>




							<el-button class="edit" v-if="isAuth('menu','编辑名称')&&scope.row.parentMenuName" type="primary" @click="updateMenuNamePage(scope.row)">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								编辑名称
							</el-button>
							<el-button class="edit" v-if="isAuth('menu','编辑权限')&&scope.row.parentMenuName" type="primary" @click="updateBtn(scope.row)">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								编辑权限
							</el-button>
							<el-button class="edit" v-if="isAuth('menu','编辑父级')&&scope.row.parentMenuName" type="primary" @click="updateMenuLevelPage(scope.row)">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								编辑父级
							</el-button>
							<el-button class="del" v-if="isAuth('menu','删除')&&!scope.row.parentMenuName" type="primary" @click="deleteMenu(scope.row)">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								删除
							</el-button>
						</template>
					</el-table-column>
				</el-table>
			</div>
			<el-pagination
				@size-change="sizeChangeHandle"
				@current-change="currentChangeHandle"
				:current-page="pageIndex"
				background
				:page-sizes="[10, 50, 100, 200]"
				:page-size="pageSize"
				:layout="layouts.join()"
				:total="totalPage"
				prev-text="< "
				next-text="> "
				:hide-on-single-page="false"
				:style='{"padding":"0","margin":"20px 0 0","whiteSpace":"nowrap","color":"#333","textAlign":"center","width":"100%","fontWeight":"500"}'
			></el-pagination>
		</template>
		
		<!-- 添加/修改页面  将父组件的search方法传递给子组件-->
		<add-or-update v-if="addOrUpdateFlag" :parent="this" ref="addOrUpdate"></add-or-update>




		<el-dialog title="修改名称" :visible.sync="updateMenuNameVisiable" width="50%">
			<el-form ref="form" :model="form" label-width="80px">
				<el-form-item label="一级菜单">
					<el-input v-model="checkParentMenuName"></el-input>
				</el-form-item>
				<el-form-item label="二级菜单">
					<el-input v-model="checkChildMenuName"></el-input>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
				<el-button @click="updateMenuNamePage">取 消</el-button>
				<el-button type="primary" @click="updateMenuName">确 定</el-button>
			</span>
		</el-dialog>

		<el-dialog title="修改父级菜单" :visible.sync="updateMenuLevelVisiable" width="50%">
			<el-form ref="form" :model="form" label-width="80px">
				<el-form-item label="一级菜单">
					<el-select v-model="checkParentMenuName" placeholder="请选择一级菜单">
						<el-option v-for="(item,index) in parentMenuNameList" v-bind:key="index" :label="item"
							:value="item">
						</el-option>
					</el-select>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
				<el-button @click="updateMenuLevelPage">取 消</el-button>
				<el-button type="primary" @click="updateMenuLevel">确 定</el-button>
			</span>
		</el-dialog>
		<el-dialog title="修改权限" :visible.sync="updateBtnVisible" width="50%">
			<el-form ref="form" :model="form" label-width="80px">
				<el-form-item :label="''">
					<el-tree ref="permissonTree" :data="allBtnList" show-checkbox node-key="menu" :default-checked-keys="btnList"
						:props="defaultProps">
					</el-tree>
				</el-form-item>
			</el-form>
			<span slot="footer" class="dialog-footer">
				<el-button @click="updateBtnVisible=false">取 消</el-button>
				<el-button type="primary" @click="updateBtnSave">确 定</el-button>
			</span>
		</el-dialog>

	</div>
</template>

<script>
import axios from 'axios'
import AddOrUpdate from "./add-or-update";
	export default {
		data() {
			return {
				searchForm: {
					key: ""
				},
				form:{},
				checkParentMenuName: '',
				oldCheckParentMenuName: '',
				checkChildMenuName: '',
				oldCheckChildMenuName: '',
				menuList: [],
				roleName: '管理员',
				roleNameList: [],
				backMenuList: [],
				backChildMenuList: [],
				parentMenuNameList: [],
				updateMenuNameVisiable: false,
				updateMenuLevelVisiable: false,
				btnList: [],
				allBtnList: [],
				defaultProps: {
					children: 'child',
					label: 'menu'
				},
				updateBtnVisible:false,
				nowId:0,
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				showFlag: true,
				addOrUpdateFlag:false,
				layouts: ["total","prev","pager","next","sizes","jumper"],
			};
		},
		created() {
			this.init();
			this.getDataList();
			this.contentStyleChange()
		},
		mounted() {
		},
		filters: {
			htmlfilter: function (val) {
				return val.replace(/<[^>]*>/g).replace(/undefined/g,'');
			}
		},
		computed: {
			tablename(){
				return this.$storage.get('sessionTable')
			},
		},
		components: {
			AddOrUpdate,
		},
		methods: {
			// 修改菜单名称页面
			updateMenuNamePage(row) {
				this.updateMenuNameVisiable = !this.updateMenuNameVisiable;
				if (row) {
					this.checkParentMenuName = row.parentMenuName;
					this.oldCheckParentMenuName = row.parentMenuName;
					this.checkChildMenuName = row.menu;
					this.oldCheckChildMenuName = row.menu;
				}
			},
	// 修改菜单名称
			updateMenuName() {
				for (var i in this.menuList) {
					if (this.menuList[i].roleName == this.roleName) {
						let oldBackMenuList = this.menuList[i].backMenu;
						let parentMenuName = '';
						let childMenuList = [];
						for (var j in oldBackMenuList) {
							parentMenuName = oldBackMenuList[j].menu;
							childMenuList = oldBackMenuList[j].child;
							if (this.oldCheckParentMenuName == parentMenuName) {
								if (parentMenuName != this.checkParentMenuName) {
									//修改一级菜单名字
									oldBackMenuList[j].menu = this.checkParentMenuName;
								}
								for (var k in childMenuList) {
									if (this.oldCheckChildMenuName == childMenuList[k].menu) {
										if (this.checkChildMenuName != childMenuList[k].menu) {
											//修改二级菜单名字
											childMenuList[k].menu = this.checkChildMenuName;
											childMenuList[k].parentMenuName = this.checkParentMenuName;
										}
									}
								}
							}
						}
					}
				}
				this.$http({
					url: "menu/update",
					method: "post",
					data: {
						"id": 1,
						"menujson": JSON.stringify(this.menuList)
					}
				}).then(({
					data
				}) => {
					if (data && data.code === 0) {
						this.$message({
							message: "操作成功",
							type: "success",
							duration: 1500,
							onClose: () => {
								this.getDataList(this.roleName);
								this.updateMenuNamePage();
							}
						});
					} else {
						this.$message.error(data.msg);
					}
				});
			},
			// 修改一级菜单页面
			updateMenuLevelPage(row) {
				this.updateMenuLevelVisiable = !this.updateMenuLevelVisiable;
				if (row) {
					this.checkParentMenuName = row.parentMenuName;
					this.oldCheckParentMenuName = row.parentMenuName;
					this.checkChildMenuName = row.menu;
				}
			},
			// 修改一级菜单
			updateMenuLevel() {
				for (var i in this.menuList) {
					if (this.menuList[i].roleName == this.roleName) {
						
						let oldBackMenuList = this.menuList[i].backMenu;
						let parentMenuName = '';
						let childMenuList = [];
						for (var j in oldBackMenuList) {
							parentMenuName = oldBackMenuList[j].menu;
							childMenuList = oldBackMenuList[j].child;
							if (this.oldCheckParentMenuName == parentMenuName) {
								//获取原父级菜单下的子菜单
								if (parentMenuName != this.checkParentMenuName) {
									let fromChildMenu;
									for (var k in childMenuList) {
										if (this.checkChildMenuName == childMenuList[k].menu) {
											fromChildMenu = childMenuList[k];
											let toChildMenuList = [];
											for (var l in oldBackMenuList) {
												if (this.checkParentMenuName == oldBackMenuList[l].menu) {
													toChildMenuList = oldBackMenuList[l].child;
													toChildMenuList.push(fromChildMenu);
													break;
												}
											}
											childMenuList.splice(k, 1)
											break;
										}
									}
								}
								break;
							}
						}
					}
				}
				this.$http({
					url: "menu/update",
					method: "post",
					data: {
						"id": 1,
						"menujson": JSON.stringify(this.menuList)
					}
				}).then(({
					data
				}) => {
					if (data && data.code === 0) {
						this.$message({
							message: "操作成功",
							type: "success",
							duration: 1500,
							onClose: () => {                                         
								this.getDataList(this.roleName);
								this.updateMenuLevelPage();
							}
						});
					} else {
						this.$message.error(data.msg);
					}
				});
			},
			updateBtn(row) {
				this.btnList = JSON.parse(JSON.stringify(row.buttons))
				this.allBtnList = JSON.parse(JSON.stringify(row.allbtns))
				this.nowId = row.id
				this.updateBtnVisible = true
			},
			updateBtnSave(){
				let checkedNodes = this.$refs.permissonTree.getCheckedNodes(false, true)
				let arr = []
				for(let i in checkedNodes){
					arr.push(checkedNodes[i].menu)
				}
				for(let x in this.backMenuList){
					for(let i in this.backMenuList[x].child){
						if(this.backMenuList[x].child[i].id== this.nowId){
							this.backMenuList[x].child[i].buttons = arr
						}
					}
				}
				for(let j in this.menuList){
					if(this.menuList[j].roleName == this.roleName){
						this.menuList[j].backMenu = this.backMenuList
					}
				}
				this.$http({
					url: "menu/update",
					method: "post",
					data: {
						"id": 1,
						"menujson": JSON.stringify(this.menuList)
					}
				}).then(({
					data
				}) => {
					if (data && data.code === 0) {
						this.$message({
							message: "操作成功",
							type: "success",
							duration: 1500,
							onClose: () => {
								this.getDataList(this.roleName);
								this.updateBtnVisible=false
							}
						});
					} else {
						this.$message.error(data.msg);
					}
				});
			},
			contentStyleChange() {
				this.contentPageStyleChange()
			},
			// 分页
			contentPageStyleChange(){
				let arr = []

				// if(this.contents.pageTotal) arr.push('total')
				// if(this.contents.pageSizes) arr.push('sizes')
				// if(this.contents.pagePrevNext){
				//   arr.push('prev')
				//   if(this.contents.pagePager) arr.push('pager')
				//   arr.push('next')
				// }
				// if(this.contents.pageJumper) arr.push('jumper')
				// this.layouts = arr.join()
				// this.contents.pageEachNum = 10
			},






    init () {
    },
    search() {
      this.pageIndex = 1;
      this.getDataList(this.roleName);
    },

	// 获取菜单列表
	getDataList(rn) {
		if (rn == null) rn = "管理员";
		this.roleNameList = [];
		this.parentMenuNameList = [];
		this.backChildMenuList = [];
		this.dataListLoading = true;
		let params = {
			page: this.pageIndex,
			limit: this.pageSize,
			sort: 'id',
		}
		this.$http({
			url: "menu/page",
			method: "get",
			params: params
		}).then(({
			data
		}) => {
			if (data && data.code === 0) {
				this.dataList = data.data.list;
				this.menuList = eval('(' + data.data.list[0].menujson + ')');
				for (var i in this.menuList) {
					this.roleNameList.push(this.menuList[i].roleName);
					if (this.menuList[i].roleName == rn) {
						this.backMenuList = this.menuList[i].backMenu;
						let parentMenuName = '';
						let childMenuList = [];
						for (var j in this.backMenuList) {
							if(!this.backMenuList[j].id){
								this.backMenuList[j].id = Number(String(Number(j) + 1) + '000')
							}
							this.parentMenuNameList.push(this.backMenuList[j].menu);
							parentMenuName = this.backMenuList[j].menu;
							childMenuList = this.backMenuList[j].child;
							if (childMenuList.length > 0) {
								for (var k in childMenuList) {
									childMenuList[k].parentMenuName = parentMenuName;
									this.backChildMenuList.push(childMenuList[k]);
								}
							} else {
								let parentNode = {};
								parentNode.parentMenuName = parentMenuName;
								this.backChildMenuList.push(parentNode);
							}
						}
						let arr = []
						let brr = []
						for (let x in this.backChildMenuList) {
							arr = []
							brr = []
							for (let n in this.backChildMenuList[x].allButtons) {
								arr.push({
									menu: this.backChildMenuList[x].allButtons[n],
									// id: Number(String(Number(x) + 1) + String(Number(n) + 1))
								})
							}
							if(!this.backChildMenuList[x].allbtns){
								this.backChildMenuList[x].allbtns = JSON.parse(JSON.stringify(arr))
							}
							if(!this.backChildMenuList[x].id){
								this.backChildMenuList[x].id = Number(String(Number(x) + 1) + '00099')
							}
						}
					}
				}
				this.totalPage = data.data.total;
			} else {
				this.dataList = [];
				this.totalPage = 0;
			}
			this.dataListLoading = false;
		});
	},
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandler(val) {
      this.dataListSelections = val;
    },
    // 添加/修改
    addOrUpdateHandler(id,type) {
      this.showFlag = false;
      this.addOrUpdateFlag = true;
      this.crossAddOrUpdateFlag = false;
      if(type!='info'){
        type = 'else';
      }
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id,type);
      });
    },
    // 查看评论
    disscussListHandler(id,type) {
	this.$router.push({path:'/discussmenu',query:{refid:id}});
    },
	menustatusChange(e,row){
		if(row.status==0){
			row.passwordwrongnum = 0
		}
		this.$http({
			url:'menu/update',
			method:'post',
			data:row
		}).then(res=>{
			if(row.status==1){
				this.$message.error('该用户已锁定')
			}else{
				this.$message.success('该用户已解除锁定')
			}
		})
	},
	// 删除
	deleteMenu(row) {
		this.$confirm(`确定进行删除操作?`, "提示", {
			confirmButtonText: "确定",
			cancelButtonText: "取消",
			type: "warning"
		}).then(() => {
			if (row) {
				this.checkParentMenuName = row.menu;
				let delFlag = false;
				for (var i in this.menuList) {
					if (this.menuList[i].roleName == this.roleName) {
						let oldBackMenuList = this.menuList[i].backMenu;
						let parentMenuName = '';
						let childMenuList = [];
						for (var j in oldBackMenuList) {
							parentMenuName = oldBackMenuList[j].menu;
							childMenuList = oldBackMenuList[j].child;
							if (this.checkParentMenuName == parentMenuName) {
								if(childMenuList.length>0) {
									this.$message.error("存在二级菜单，无法删除。");
									break;
								}
								oldBackMenuList.splice(j, 1)
								delFlag = true;
								break;
							}
						}
						break;
					}
				}
				if(delFlag) {
					this.$http({
						url: "menu/update",
						method: "post",
						data: {
							"id": 1,
							"menujson": JSON.stringify(this.menuList)
						}
					}).then(({
						data
					}) => {
						if (data && data.code === 0) {
							this.$message({
								message: "操作成功",
								type: "success",
								duration: 1500,
								onClose: () => {
									this.getDataList(this.roleName);
								}
							});
						} else {
							this.$message.error(data.msg);
						}
					});
				}
			}
		})
	},


  }

};
</script>
<style lang="scss" scoped>
	
	.center-form-pv {
	  .el-date-editor.el-input {
	    width: auto;
	  }
	}
	
	.el-input {
	  width: auto;
	}
	
	// form
	.center-form-pv .el-input /deep/ .el-input__inner {
				border: 1;
				border-radius: 4px;
				padding: 0 12px;
				outline: none;
				color: #333;
				width: 170px;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .el-select /deep/ .el-input__inner {
				border: 1;
				border-radius: 4px;
				padding: 0 10px;
				outline: none;
				color: #333;
				width: 170px;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .el-date-editor /deep/ .el-input__inner {
				border: 1;
				border-radius: 4px;
				padding: 0 10px 0 30px;
				outline: none;
				color: #333;
				width: 170px;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .search {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 24px;
				outline: none;
				color: #fff;
				background: linear-gradient(rgb(93, 129, 166), rgb(67, 102, 140));
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .search:hover {
				opacity: 0.8;
			}
	
	.center-form-pv .actions .add {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 14px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(#ADC800 0%,#829E18 100%);
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .actions .add:hover {
				opacity: 0.8;
			}
	
	.center-form-pv .actions .del {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 14px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(#D96D3A 0%,#CB2C1A 100%);
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .actions .del:hover {
				opacity: 0.8;
			}
	
	.center-form-pv .actions .statis {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 14px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(rgb(93, 129, 166), rgb(67, 102, 140));
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .actions .statis:hover {
				opacity: 0.8;
			}
	
	.center-form-pv .actions .btn18 {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 14px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(rgb(93, 129, 166), rgb(67, 102, 140));
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .actions .btn18:hover {
				opacity: 0.8;
			}
	
	// table
	.el-table /deep/ .el-table__header-wrapper thead {
				color: #fff;
				font-weight: 500;
				width: 100%;
			}
	
	.el-table /deep/ .el-table__header-wrapper thead tr {
				background: #fff;
			}
	
	.el-table /deep/ .el-table__header-wrapper thead tr th {
				padding: 12px 0;
				background: linear-gradient(rgb(93, 129, 166), rgb(67, 102, 140));
				border-color: #eee;
				border-width: 0 0 1px 0;
				border-style: solid;
				text-align: left;
			}

	.el-table /deep/ .el-table__header-wrapper thead tr th .cell {
				padding: 0 10px;
				word-wrap: normal;
				word-break: break-all;
				white-space: normal;
				font-weight: bold;
				display: inline-block;
				vertical-align: middle;
				width: 100%;
				line-height: 24px;
				position: relative;
				text-overflow: ellipsis;
			}

	
	.el-table /deep/ .el-table__body-wrapper tbody {
				width: 100%;
			}

	.el-table /deep/ .el-table__body-wrapper tbody tr {
				background: #fff;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td {
				padding: 12px 0;
				color: #333;
				background: #fff;
				border-color: #eee;
				border-width: 0 0 1px 0;
				border-style: solid;
				text-align: left;
			}
	
		.el-table /deep/ .el-table__body-wrapper tbody tr.el-table__row--striped td {
		background: #F9F9F9;
	}
		
	.el-table /deep/ .el-table__body-wrapper tbody tr:hover td {
				padding: 12px 0;
				color: #385d86;
				background: rgba(93, 129, 166,0.3);
				border-color: #eee;
				border-width: 0;
				border-style: solid;
				text-align: left;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td {
				padding: 12px 0;
				color: #333;
				background: #fff;
				border-color: #eee;
				border-width: 0 0 1px 0;
				border-style: solid;
				text-align: left;
			}

	.el-table /deep/ .el-table__body-wrapper tbody tr td .cell {
				padding: 0 10px;
				overflow: hidden;
				word-break: break-all;
				white-space: normal;
				line-height: 24px;
				text-overflow: ellipsis;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .view {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 24px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(#ADC800 0%,#829E18 100%);
				width: auto;
				font-size: 14px;
				height: 32px;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .view:hover {
				opacity: 0.8;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .add {
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .add:hover {
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .edit {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 24px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(rgb(93, 129, 166), rgb(67, 102, 140));
				width: auto;
				font-size: 14px;
				height: 32px;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .edit:hover {
				opacity: 0.8;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .del {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 24px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(#D96D3A 0%,#CB2C1A 100%);
				width: auto;
				font-size: 14px;
				height: 32px;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .del:hover {
				opacity: 0.8;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .btn8 {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 24px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: linear-gradient(rgb(93, 129, 166), rgb(67, 102, 140));
				width: auto;
				font-size: 14px;
				height: 32px;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td .btn8:hover {
				opacity: 0.8;
			}
	
	// pagination
	.main-content .el-pagination /deep/ .el-pagination__total {
				margin: 0 10px 0 0;
				color: #666;
				font-weight: 400;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-prev {
				border: none;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #666;
				background: #fff;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				min-width: 35px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-next {
				border: none;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #666;
				background: #fff;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				min-width: 35px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-prev:disabled {
				border: none;
				cursor: not-allowed;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #C0C4CC;
				background: #fff;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-next:disabled {
				border: none;
				cursor: not-allowed;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #C0C4CC;
				background: #fff;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}

	.main-content .el-pagination /deep/ .el-pager {
				padding: 0;
				margin: 0;
				display: inline-block;
				vertical-align: top;
			}

	.main-content .el-pagination /deep/ .el-pager .number {
				cursor: pointer;
				padding: 0 4px;
				margin: 0 5px;
				color: #666;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: #fff;
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pager .number:hover {
				cursor: pointer;
				padding: 0 4px;
				margin: 0 5px;
				color: #fff;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: #385d86;
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pager .number.active {
				cursor: default;
				padding: 0 4px;
				margin: 0 5px;
				color: #FFF;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: #385d86;
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes {
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input {
				margin: 0 5px;
				width: 100px;
				position: relative;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input .el-input__inner {
				border: 1px solid #DCDFE6;
				cursor: pointer;
				padding: 0 25px 0 8px;
				color: #606266;
				display: inline-block;
				font-size: 13px;
				line-height: 28px;
				border-radius: 3px;
				outline: 0;
				background: #FFF;
				width: 100%;
				text-align: center;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input span.el-input__suffix {
				top: 0;
				position: absolute;
				right: 0;
				height: 100%;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input .el-input__suffix .el-select__caret {
				cursor: pointer;
				color: #C0C4CC;
				width: 25px;
				font-size: 14px;
				line-height: 28px;
				text-align: center;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump {
				margin: 0 0 0 24px;
				color: #606266;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump .el-input {
				border-radius: 3px;
				padding: 0 2px;
				margin: 0 2px;
				display: inline-block;
				width: 50px;
				font-size: 14px;
				line-height: 18px;
				position: relative;
				text-align: center;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump .el-input .el-input__inner {
				border: 1px solid #DCDFE6;
				cursor: pointer;
				padding: 0 3px;
				color: #606266;
				display: inline-block;
				font-size: 14px;
				line-height: 28px;
				border-radius: 3px;
				outline: 0;
				background: #FFF;
				width: 100%;
				text-align: center;
				height: 28px;
			}
	
	// list one
	.one .list1-view {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 15px;
				margin: 0;
				outline: none;
				color: #fff;
				background: rgb(103, 194, 58);
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.one .list1-view:hover {
				opacity: 0.8;
			}
	
	.one .list1-edit {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 15px;
				margin: 0;
				outline: none;
				color: #fff;
				background: #409eff;
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.one .list1-edit:hover {
				opacity: 0.8;
			}
	
	.one .list1-del {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 15px;
				margin: 0;
				outline: none;
				color: #fff;
				background: rgba(255, 0, 0, 1);
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.one .list1-del:hover {
				opacity: 0.8;
			}
	
	.one .list1-btn8 {
				border: 0;
				cursor: pointer;
				border-radius: 4px;
				padding: 0 24px;
				margin: 4px;
				outline: none;
				color: #fff;
				background: rgba(255, 128, 0, 1);
				width: auto;
				font-size: 14px;
				height: 40px;
			}
	
	.one .list1-btn8:hover {
				opacity: 0.8;
			}
	
	.main-content .el-table .el-switch {
				display: inline-flex;
				vertical-align: middle;
				line-height: 30px;
				position: relative;
				align-items: center;
				height: 30px;
			}
	.main-content .el-table .el-switch /deep/ .el-switch__label--left {
				cursor: pointer;
				margin: 0 10px 0 0;
				color: #333;
				font-weight: 500;
				display: inline-block;
				vertical-align: middle;
				font-size: 16px;
				transition: .2s;
				height: 30px;
			}
	.main-content .el-table .el-switch /deep/ .el-switch__label--right {
				cursor: pointer;
				margin: 0 0 0 10px;
				color: #333;
				font-weight: 500;
				display: inline-block;
				vertical-align: middle;
				font-size: 16px;
				transition: .2s;
				height: 30px;
			}
	.main-content .el-table .el-switch /deep/ .el-switch__core {
				border: 1px solid #0000ff;
				cursor: pointer;
				border-radius: 15px;
				margin: 0;
				outline: 0;
				background: #0000ff;
				display: inline-block;
				width: 60px;
				box-sizing: border-box;
				transition: border-color .3s,background-color .3s;
				height: 30px;
			}
	.main-content .el-table .el-switch /deep/ .el-switch__core::after {
				border-radius: 100%;
				top: 1px;
				left: 1px;
				background: #FFF;
				width: 26px;
				position: absolute;
				transition: all .3s;
				height: 26px;
			}
	.main-content .el-table .el-switch.is-checked /deep/ .el-switch__core::after {
				margin: 0 0 0 -27px;
				left: 100%;
			}
	
	.main-content .el-table .el-rate /deep/ .el-rate__item {
				cursor: pointer;
				display: inline-block;
				vertical-align: middle;
				font-size: 0;
				position: relative;
			}
	.main-content .el-table .el-rate /deep/ .el-rate__item .el-rate__icon {
				margin: 0 3px;
				display: inline-block;
				font-size: 18px;
				position: relative;
				transition: .3s;
			}

</style>
