<template>
	<div class="main-content" :style='{"padding":"30px"}'>
		<!-- 列表页 -->
		<template v-if="showFlag">
			<el-form class="center-form-pv" :style='{"margin":"0 0 20px"}' :inline="true" :model="searchForm">
				<el-row :style='{"display":"block"}' >
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}'>
						<label :style='{"margin":"0 10px 0 0","color":"#666","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"500","height":"40px"}' class="item-label">证书名称</label>
						<el-input v-model="searchForm.zhengshumingcheng" placeholder="证书名称" @keydown.enter.native="search()" clearable></el-input>
					</div>
					<div :style='{"margin":"0 10px 0 0","display":"inline-block"}' class="select" label="证书分类" prop="zhengshufenlei">
						<label :style='{"margin":"0 10px 0 0","color":"#666","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"500","height":"40px"}' class="item-label">证书分类</label>
						<el-select clearable v-model="searchForm.zhengshufenlei" placeholder="请选择证书分类" >
							<el-option v-for="(item,index) in zhengshufenleiOptions" v-bind:key="index" :label="item" :value="item"></el-option>
						</el-select>
					</div>
					<el-button class="search" type="success" @click="search()">
						<span class="icon iconfont icon-xihuan" :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
						查询
					</el-button>
				</el-row>

				<el-row class="actions" :style='{"flexWrap":"wrap","margin":"20px 0","display":"flex"}'>
					<el-button class="add" v-if="isAuth('rongyuzhengshu','新增')" type="success" @click="addOrUpdateHandler()">
						<span class="icon iconfont icon-tianjia1" :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
						添加
					</el-button>
					<el-button class="del" v-if="isAuth('rongyuzhengshu','删除')" :disabled="dataListSelections.length?false:true" type="danger" @click="deleteHandler()">
						<span class="icon iconfont icon-shanchu9" :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
						删除
					</el-button>



					<el-button class="btn18" v-if="isAuth('rongyuzhengshu','证书分类统计')" type="success" @click="chartDialog1()">
						<span class="icon iconfont icon-xiugai13" :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
						证书分类统计
					</el-button>
					<el-button class="btn18" v-if="isAuth('rongyuzhengshu','获得证书统计')" type="success" @click="chartDialog2()">
						<span class="icon iconfont icon-xiugai13" :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
						获得证书统计
					</el-button>
				</el-row>
			</el-form>
			<div :style='{"width":"100%","padding":"10px"}'>
				<el-table class="tables"
					:stripe='true'
					:style='{"width":"100%","padding":"0","borderColor":"#eee","borderStyle":"solid","borderWidth":"1px 0 0 0","background":"#fff"}' 
					:border='true'
					v-if="isAuth('rongyuzhengshu','查看')"
					:data="dataList"
					v-loading="dataListLoading"
				@selection-change="selectionChangeHandler">
					<el-table-column :resizable='false' type="selection" align="center" width="50"></el-table-column>
					<el-table-column :resizable='false' :sortable='false' label="序号" type="index" width="50" />
					<el-table-column :resizable='false' :sortable='false'  
						prop="zhengshumingcheng"
						label="证书名称">
						<template slot-scope="scope">
							{{scope.row.zhengshumingcheng}}
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false'  
						prop="zhengshufenlei"
						label="证书分类">
						<template slot-scope="scope">
							{{scope.row.zhengshufenlei}}
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false'  
						prop="zhengshujibie"
						label="证书级别">
						<template slot-scope="scope">
							{{scope.row.zhengshujibie}}
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false' prop="zhengshufujian" label="证书附件">
						<template slot-scope="scope">
							<el-button v-if="scope.row.zhengshufujian" type="text" size="small" @click="download(scope.row.zhengshufujian)">下载</el-button>
                            <span v-else >无</span>
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false'  
						prop="beizhu"
						label="备注">
						<template slot-scope="scope">
							{{scope.row.beizhu}}
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false'  
						prop="xuehao"
						label="学号">
						<template slot-scope="scope">
							{{scope.row.xuehao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false'  
						prop="xueshengxingming"
						label="学生姓名">
						<template slot-scope="scope">
							{{scope.row.xueshengxingming}}
						</template>
					</el-table-column>
					<el-table-column :resizable='false' :sortable='false'  
						prop="banji"
						label="班级">
						<template slot-scope="scope">
							{{scope.row.banji}}
						</template>
					</el-table-column>
					<el-table-column width="300" label="操作">
						<template slot-scope="scope">
							<el-button class="view" v-if=" isAuth('rongyuzhengshu','查看')" type="success" @click="addOrUpdateHandler(scope.row.id,'info')">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								查看
							</el-button>
							<el-button class="edit" v-if=" isAuth('rongyuzhengshu','修改') " type="success" @click="addOrUpdateHandler(scope.row.id)">
								<span class="icon iconfont " :style='{"margin":"0 2px","fontSize":"14px","color":"#fff","height":"40px"}'></span>
								修改
							</el-button>




							<el-button class="del" v-if="isAuth('rongyuzhengshu','删除') " type="primary" @click="deleteHandler(scope.row.id )">
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




		<el-dialog
		  :visible.sync="chartVisiable1"
		  width="800">
			<div id="zhengshufenleiChart1" style="width:100%;height:600px;"></div>
		  <span slot="footer" class="dialog-footer">
			<el-button @click="chartDialog1">返回</el-button>
		  </span>
		</el-dialog>
		<el-dialog
		  :visible.sync="chartVisiable2"
		  width="800">
			<div id="xueshengxingmingChart2" style="width:100%;height:600px;"></div>
		  <span slot="footer" class="dialog-footer">
			<el-button @click="chartDialog2">返回</el-button>
		  </span>
		</el-dialog>

	</div>
</template>

<script>
import * as echarts from 'echarts'
import chinaJson from "@/components/echarts/china.json";
import axios from 'axios'
import AddOrUpdate from "./add-or-update";
	export default {
		data() {
			return {
				zhengshufenleiOptions: [],
				searchForm: {
					key: ""
				},
				form:{},
				dataList: [],
				pageIndex: 1,
				pageSize: 10,
				totalPage: 0,
				dataListLoading: false,
				dataListSelections: [],
				showFlag: true,
				chartVisiable1: false,
				chartVisiable2: false,
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


//统计接口
    chartDialog1() {
      this.chartVisiable1 = !this.chartVisiable1;
      this.$nextTick(()=>{
        var zhengshufenleiChart1 = echarts.init(document.getElementById("zhengshufenleiChart1"),'macarons');
        this.$http({
            url: "rongyuzhengshu/group/zhengshufenlei",
            method: "get",
        }).then(({ data }) => {
            if (data && data.code === 0) {
                let res = data.data;
                let xAxis = [];
                let yAxis = [];
                let pArray = []
                for(let i=0;i<res.length;i++){
                    xAxis.push(res[i].zhengshufenlei);
                    yAxis.push(parseFloat((res[i].total)));
                    pArray.push({
                        value: parseFloat((res[i].total)),
                        name: res[i].zhengshufenlei
                    })
                }
                var option = {};
                option = {
                        title: {
                            text: '证书分类统计',
                            left: 'center'
                        },
                        legend: {
                          orient: 'vertical',
                          left: 'left'
                        },
                        tooltip: {
                          trigger: 'item',
                          formatter: '{b} : {c} ({d}%)'
                        },
                        series: [
                            {
                                type: 'pie',
                                radius: '55%',
                                center: ['50%', '60%'],
                                data: pArray,
                                emphasis: {
                                    itemStyle: {
                                        shadowBlur: 10,
                                        shadowOffsetX: 0,
                                        shadowColor: 'rgba(0, 0, 0, 0.5)'
                                    }
                                }
                            }
                        ]
                };
                // 使用刚指定的配置项和数据显示图表。
                zhengshufenleiChart1.setOption(option);
                  //根据窗口的大小变动图表
                window.onresize = function() {
                    zhengshufenleiChart1.resize();
                };
            }
        });
      })
    },

//统计接口
    chartDialog2() {
      this.chartVisiable2 = !this.chartVisiable2;
      this.$nextTick(()=>{

        var xueshengxingmingChart2 = echarts.init(document.getElementById("xueshengxingmingChart2"),'macarons');
        this.$http({
            url: "rongyuzhengshu/group/xueshengxingming",
            method: "get",
        }).then(({ data }) => {
            if (data && data.code === 0) {
                let res = data.data;
                let xAxis = [];
                let yAxis = [];
                let pArray = []
                for(let i=0;i<res.length;i++){
                    xAxis.push(res[i].xueshengxingming);
                    yAxis.push(parseFloat((res[i].total)));
                    pArray.push({
                        value: parseFloat((res[i].total)),
                        name: res[i].xueshengxingming
                    })
                }
                var option = {};
                option = {
                    title: {
                        text: '获得证书统计',
                        left: 'center'
                    },
                    tooltip: {
                      trigger: 'item',
                      formatter: '{b} : {c}'
                    },
                    xAxis: {
                        type: 'category',
                        data: xAxis,
                        axisLabel : {
                            rotate:40
                        }
                    },
                    yAxis: {
                        type: 'value'
                    },
                    series: [{
                        data: yAxis,
                        type: 'bar'
                    }]
                };
                // 使用刚指定的配置项和数据显示图表。
                xueshengxingmingChart2.setOption(option);
                  //根据窗口的大小变动图表
                window.onresize = function() {
                    xueshengxingmingChart2.resize();
                };
            }
        });
      })
    },



    init () {
          this.$http({
            url: `option/zhengshufenlei/zhengshufenlei`,
            method: "get"
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.zhengshufenleiOptions = data.data;
            } else {
              this.$message.error(data.msg);
            }
          });
    },
    search() {
      this.pageIndex = 1;
      this.getDataList();
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      let params = {
        page: this.pageIndex,
        limit: this.pageSize,
        sort: 'id',
        order: 'desc',
      }
           if(this.searchForm.zhengshumingcheng!='' && this.searchForm.zhengshumingcheng!=undefined){
            params['zhengshumingcheng'] = '%' + this.searchForm.zhengshumingcheng + '%'
          }
          if(this.searchForm.zhengshufenlei!='' && this.searchForm.zhengshufenlei!=undefined){
            params['zhengshufenlei'] = this.searchForm.zhengshufenlei
          }
          if(this.searchForm.xuehao!='' && this.searchForm.xuehao!=undefined){
            params['xuehao'] = this.searchForm.xuehao
          }
			let user = JSON.parse(this.$storage.getObj('userForm'))
			console.log(user)
			if(user.banji){
				params['banji'] = user.banji
			}
			this.$http({
				url: "rongyuzhengshu/page",
				method: "get",
				params: params
			}).then(({ data }) => {
				if (data && data.code === 0) {
					this.dataList = data.data.list;
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
    // 下载
    download(file){
      let arr = file.replace(new RegExp('upload/', "g"), "")
      axios.get(this.$base.url + 'file/download?fileName=' + arr, {
      	headers: {
      		token: this.$storage.get('Token')
      	},
      	responseType: "blob"
      }).then(({
      	data
      }) => {
      	const binaryData = [];
      	binaryData.push(data);
      	const objectUrl = window.URL.createObjectURL(new Blob(binaryData, {
      		type: 'application/pdf;chartset=UTF-8'
      	}))
      	const a = document.createElement('a')
      	a.href = objectUrl
      	a.download = arr
      	// a.click()
      	// 下面这个写法兼容火狐
      	a.dispatchEvent(new MouseEvent('click', {
      		bubbles: true,
      		cancelable: true,
      		view: window
      	}))
      	window.URL.revokeObjectURL(data)
      },err=>{
		  axios.get((location.href.split(this.$base.name).length>1 ? location.href.split(this.$base.name)[0] :'') + this.$base.name + '/file/download?fileName=' + arr, {
		  	headers: {
		  		token: this.$storage.get('Token')
		  	},
		  	responseType: "blob"
		  }).then(({
		  	data
		  }) => {
		  	const binaryData = [];
		  	binaryData.push(data);
		  	const objectUrl = window.URL.createObjectURL(new Blob(binaryData, {
		  		type: 'application/pdf;chartset=UTF-8'
		  	}))
		  	const a = document.createElement('a')
		  	a.href = objectUrl
		  	a.download = arr
		  	// a.click()
		  	// 下面这个写法兼容火狐
		  	a.dispatchEvent(new MouseEvent('click', {
		  		bubbles: true,
		  		cancelable: true,
		  		view: window
		  	}))
		  	window.URL.revokeObjectURL(data)
		  })
	  })
    },
	rongyuzhengshustatusChange(e,row){
		if(row.status==0){
			row.passwordwrongnum = 0
		}
		this.$http({
			url:'rongyuzhengshu/update',
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
    async deleteHandler(id ) {
		var ids = id? [Number(id)]: this.dataListSelections.map(item => {
			return Number(item.id);
		});
		await this.$confirm(`确定进行[${id ? "删除" : "批量删除"}]操作?`, "提示", {
			confirmButtonText: "确定",
			cancelButtonText: "取消",
			type: "warning"
		}).then(async () => {
			await this.$http({
				url: "rongyuzhengshu/delete",
				method: "post",
				data: ids
			}).then(async ({ data }) => {
				if (data && data.code === 0) {
					this.$message({
						message: "操作成功",
						type: "success",
						duration: 1500,
						onClose: () => {
							this.search();
						}
					});
            
				} else {
					this.$message.error(data.msg);
				}
			});
		});
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
