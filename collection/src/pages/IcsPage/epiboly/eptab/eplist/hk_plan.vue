<template>
	<section ref="abc" style="overflow-y: auto;">
	<el-collapse v-model="activeNames" >
		<el-collapse-item title="浮动利率合同" name="1" v-if="isFixed==0" >
			<!--列表-->
			<!--  -->
			<el-table :data="lists" :max-height="heights" border highlight-current-row v-loading="listLoading" style="width: 100%;" stripe >
				<el-table-column :prop="col.field" :label="col.title" v-for="(col, index) in cols" :key="index" align="center" :width="col.width" sortable :fixed="col.fixed">
				</el-table-column>
			</el-table>

			<!--工具条-->
			<el-col :span="24" class="toolbar">
				
				<el-pagination layout="total, sizes, prev, pager, next, jumper" @current-change="handleCurrentChange" @size-change="handleSizeChange" :page-size="pagesize" :page-sizes="[10, 20, 50, 100]"   :total="total"   style="float:right;">
				</el-pagination>
			</el-col>
		</el-collapse-item>
        <el-collapse-item v-else title="固定利率合同" name="2">
			<!--列表-->
			 <!--  -->
			<el-table :data="liststwo" :max-height="heights" border highlight-current-row v-loading="listLoading" style="width: 100%;" stripe >
				<el-table-column :prop="col.field" :label="col.title" v-for="(col, index) in colstwo" :key="index" align="center" :width="col.width" sortable :fixed="col.fixed">
				</el-table-column>
			</el-table>

			<!--工具条-->
			<el-col :span="24" class="toolbar">				
				<el-pagination layout="total, sizes, prev, pager, next, jumper" @current-change="handleCurrentChangetwo" @size-change="handleSizeChangetwo" :page-size="pagesizetwo" :page-sizes="[10, 20, 50, 100]"   :total="totaltwo"   style="float:right;">
				</el-pagination>
			</el-col>
		</el-collapse-item>
	</el-collapse>
	</section>
</template>

<script>
import { getplan ,gettwo} from "@/api/tablist";
export default {
		data() {
			return {
				heights:0,
				activeNames: ['1','2'],
				lists: [],
				// ,fixed:"left",fixed:"right"
				cols: [
			{title:'还款期数',field:'repaymentPeriod',width:"80"},
            {title:'到期日',field:'dueDate',width:"90"},
            {title:'月应还本金',field:'monthRepay',width:"90"},
            {title:'利息额',field:'interestAmount',width:"80"},
            {title:'月息',field:'monthly',width:"70"},
            {title:'复合利息',field:'compoundAmount',width:"80"},
            {title:'分期付款额',field:'installmentAmount',width:"100"},
            {title:'结算金额',field:'settlementAmount',width:"80"},
            {title:'未结清本金',field:'outstandingAmoimt',width:"100"},
            {title:'调整身份',field:'adjustIdentity',width:"80"},
            {title:'实际还款日',field:'actualDay',width:"100"},
            {title:'逾期天数',field:'overdueDays',width:"80"},
				],
				total: 0,
				pagesize: 20,
				page: 1,
				id:this.$store.state.navTabs.tabId,
                listLoading: false,	                
                liststwo: [],
				colstwo: [
					{title:'到期日',field:'dueDate'},
            		{title:'还款期数',field:'repaymentPeriod'},
            		{title:'未偿本金',field:'noPrincipal'},
           			{title:'还款本金额',field:'repaymentAmount'},
            		{title:'还款利息额',field:'repaymentInterest'},
            		{title:'月息',field:'monthly'},
            		{title:'复合利息金额',field:'compoundAmount'},
            		{title:'净还款金额',field:'netAmount'},
            		{title:'杂费',field:'incidental'},
            		{title:'已付款总额',field:'totalPaid'},
            		{title:'未承兑/取消',field:'trueday'},
            		{title:'实际还款日',field:'actualDay'},
            		{title:'逾期天数',field:'overdueDays'},
				],
				totaltwo: 0,
				pagesizetwo: 20,
				pagetwo: 1,
				listLoadingtwo: false,
				isFixed:0,	
                
			}
		},
		methods: {
            handleSizeChangetwo(val) {
				this.pagesizetwo = val;
				this.getliststwo();
			},
			handleCurrentChangetwo(val) {
				this.pagetwo = val;
				this.getliststwo();
			},
			handleSizeChange(val) {
				this.pagesize = val;
				this.getlists();
			},
			handleCurrentChange(val) {
				this.page = val;
				this.getlists();
			},
			//获取列表
			getlists() {
				 let h=(window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight)-260;
        		this.heights=h;
				let para = {
					missionId: this.$route.params.id,
					page: this.page,
					pageSize: this.pagesize
				};
				this.listLoading = true;
				getplan(para).then((res) => {
					//  console.log(res.data.result.fixed)
					let data=res.data.result;
					this.isFixed=data.isFixed;
					
					if(data.isFixed==0){
						this.lists =data.floating.data;
						this.total=data.floating.recordsTotal;
					}
					// }else{
					// 	this.this.liststwo =[];
					// 	this.totaltwo=1;
					// }
					this.listLoading = false;
				});
            },
            getliststwo() {
				let h=(window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight)-260;
        		this.heights=h;
				let para = {
					missionId: this.$route.params.id,
					page: this.pagetwo,
					pageSize: this.pagesizetwo
				};
				this.listLoading = true;
				// NProgress.start();
				gettwo(para).then((res) => {
					let data=res.data.result;
					this.isFixed=data.isFixed;
					// this.totaltwo=data.fixed.recordsTotal;
					if(data.isFixed==1){
						this.isFixed=data.isFixed;
						this.liststwo =data.fixed.data;
						this.totaltwo=data.fixed.recordsTotal;
					}
					
				});
				this.listLoading = false;
			},
		},
		mounted() {
            this.getlists();
            this.getliststwo();
			let h = (window.innerHeight || document.documentElement.clientHeight || document.body.clientHeight)-155;
   			this.$refs.abc.style.height= h+"px"
		}
}
</script>
<style>
</style>