<template>
    <section id="colone">
        <el-col  :span="24" class="toolbar" style="padding-bottom: 0px;">
            <el-form :inline="true" :model="filters">
				<el-form-item>
					<el-input  v-model="filters.name" placeholder="当事人" style="width:140px" clearable></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="filters.applicationNumber" placeholder="合同号" style="width:140px"  clearable>></el-input>
				</el-form-item>
				<el-form-item>
					<el-input v-model="filters.overdueDays" placeholder="逾期天数" style="width:140px"  clearable></el-input>
				</el-form-item>
				<el-form-item>
					<el-date-picker v-model="value6" 
					type="daterange" 
					range-separator="至" 				
					placeholder="请选择约会时间区域" 				
					@change="dataChange"
					style="font-size:13px"
					>
					</el-date-picker>
				</el-form-item>
				<el-form-item>
					<el-button type="primary" @click="getUsers" size="mini" style="padding:10px 15px">查询</el-button>
				</el-form-item>				
			</el-form>  
        </el-col>  
    <!--列表-->
		<el-table :data="datas" :max-height="heights" highlight-current-row  v-loading="listLoading"   style="width: 100%;"  :row-class-name="tableRowClassName"  id="ClOne" @current-change="handleCurrentChanges" ref="singleTable" border>
			<el-table-column label="操作" width="60" fixed="left"  align="center" show-overflow-tooltip  >
				<template slot-scope="scope">
					 <router-link class="a-href" :to="{path:'/IcsPage/tab/tabview/'+scope.row.id}"><span @click="setCurrent(scope.$index,scope.row.id)">处理</span></router-link>
				</template>
				
			</el-table-column>
			<el-table-column sortable :prop="cols.field" :label="cols.title" :width="cols.width" :show-overflow-tooltip='true'   v-for="(cols, index) in cols" :key="index" align="center" >
			</el-table-column>
		 
		</el-table>
        
		<!--工具条-->
		<el-col :span="24" class="toolbar">			
			<el-pagination layout="total, sizes,prev, pager, next,jumper" @current-change="handleCurrentChange" @size-change="handleSizeChange"  :current-page.sync="page" :page-size="pagesize" :total="total" :page-sizes='[200,500,800,1000]' style="float:right;">
			</el-pagination>
		</el-col>
    </section>
</template>
<script>
	import { listRestSortByCo} from '@/api/collmanage';
	export default {

		
		data() {			
			return {
			heights:0,
			value6:"",
			times1:"",
			times2:"",
			   userid:localStorage.getItem('UserId'),
               formInline:{
                    user:"",
                    region:""
                },
				filters: {					
					name: '',
					applicationNumber:"",
					overdueDays:"",
					startTime:"",
					endTime:"",
					phone:""

				},
				datas: [],
				// cols:[],
				cols: [
					{ title: '姓名', field: 'name', width: "60" },
					{ title: '合同号', field: 'applicationNumber', width: "80" },
					{ title: '逾期天数', field: 'overdueDays', width: "75" },
					{ title: '逾期金额', field: 'overdueTotal', width: "80" },  
					{ title: '约会日期', field: 'appointmentTime', width: "80" },
					{ title: '最近行动代码', field: 'actSign', width: "120" },
					{ title: '最近行动时间', field: 'inputTime', width: "150" },					
           			{ title: '贷款车型', field: 'car', width: "150" },
					{ title: '省份', field: 'province', width: "50" },
					{ title: '城市', field: 'city', width: "50" },
					{ title: '未偿本金', field: 'residualAmount', width: "90" },
					{ title: '贷款金额', field: 'loanAmount', width: "90" },
					{ title: '职业', field: 'occupation', width: "90" },
					{ title: '贷款产品', field: 'loanProducts', width: "150" },
					{ title: '派单时间', field: 'createTime', width: "120" },					
					// { title: '区域', field: 'Region', width: "80" },		
					{ title: '核销状态', field: 'stateVerification', width: "100" },
					{ title: '收车状态', field: 'receiveCarStatus', width: "100" },
					],
				total: 0,
				page: 1,
				pagesize:1000,
				currentPage:1,
				SelectOption:"",
				listLoading: true,
				sels: [],//列表选中列
				tpageSize:20,
				NowFormatDate:"",
				currentRow: null,
				// rowsIndes:""
            }           
		},
		watch:{
			pageSize:"changeSize"
		},
		methods: {
			setCurrent(row,id) {
				this.$refs.singleTable.setCurrentRow(row);
				localStorage.setItem("nextNum","0");
				localStorage.setItem("currentRow",parseInt(this.currentRow)+1);
				localStorage.setItem("total",this.total);
				localStorage.setItem(id,id)				
			  },
			handleCurrentChanges(val) {
				this.currentRow = val;
      		},
			dataChange(val){			
				this.times2=val.split("至").pop();
				this.times1=val.split("至").shift();
     		 },
			changeSize(){
				this._data.tpageSize=this.pageSize;
			},
			handleCurrentChange(val) {
                this.pages = val;
				this.getUsers();
            },
            handleSizeChange(val) {
			this.pagesize = val  
			this.getUsers();     
			},
			handleOptionChange(val) {
				 this.SelectOption=val;
				 this.getUsers();
            },
			//获取用户列表
			getUsers() {
				let h=(window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight)-195;
				this.heights=h;
				
				let para = {
					page: this.page,
					pageSize:this.pagesize,
					userId:this.userid,
					queueIds:"117",
					name:this.filters.name,
					applicationNumber:this.filters.applicationNumber,
					overdueDays:this.filters.overdueDays,
					startTime:this.times1,
					endTime:this.times2
				};
				this.listLoading = true;				//NProgress.start();
				listRestSortByCo(para).then((res) => {
					let data=res.data.result;										 this.datas=data.data;	
					
					 this.datas.forEach(element => {
						if(element.overdueDays&&element.overdueDays!=null){
                        	element.overdueDays=Number(element.overdueDays)                    
                    	}
						});				 
					 this.total=data.recordsTotal;
					  this.listLoading = false;
				});
			},			
			getNowFormatDate() {
				var date = new Date();
				var seperator1 = "-";
				var month = date.getMonth() + 1;
				var strDate = date.getDate();
				if (month >= 1 && month <= 9) {
					month = "0" + month;
				}
				if (strDate >= 0 && strDate <= 9) {
					strDate = "0" + strDate;
				}
				var currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate;						
				this.NowFormatDate=currentdate;
			},	
			tableRowClassName(row, rowIndex) {
				if (row.appointmentTime <=this.NowFormatDate) {
				
				return 'warning-row';
				} 
				return '';
			}	
		},
		mounted() {
			this.getUsers();
			this.getNowFormatDate()
		}
	}
</script>
<style>	
	#colone .cell{
		padding:0!important;
		line-height:24px!important;	
		white-space: nowrap!important	
	}; 
	.cell{white-space: nowrap!important}
	.el-table__fixed{height:100%!important}
	.el-table#ClOne .warning-row {
    background: rgb(218, 149, 21)!important;
  }
.el-table#ClOne .warning-row td:nth-of-type(6){
	 background: rgb(233, 12, 12)!important;
	 color: #fff;
}
  .el-table#ClOne .success-row {
    background: #f0f9eb;
  }

</style>

