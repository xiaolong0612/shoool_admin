<template>
	<div>
		<div class="ui-search-wrap" id="ui-search-wrap">
      <el-form :inline="true">

        <el-form-item label="届">
          <el-select v-model="paperQuery.period" filterable clearable placeholder="请选择" @change="queryChange('period')">
            <el-option v-for="item in periodList" :label="item.label" :value="item.label" :key="item.value">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="年级">
          <el-select v-model="paperQuery.grade" filterable clearable placeholder="请选择" @change="queryChange('grade')">
            <el-option v-for="item in gradeList" :label="item.label" :value="item.label" :key="item.label">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="科目">
          <el-select v-model="paperQuery.subject" filterable clearable placeholder="请选择" @change="queryChange('subject')">
            <el-option v-for="item in subjectList" :label="item.label" :value="item.label" :key="item.label">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="→"></el-form-item>
        <el-form-item label="试卷">
          <el-select v-model="listQuery.paperId" filterable clearable placeholder="请选择" @change="getList">
            <el-option v-for="item in paperList" :label="item.name" :value="item.id" :key="item.id">
            </el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </div>
		<div class="ui-table-wrap clearfix">
			<h3 class="ui-table-title">
				<wscn-icon-svg icon-class="shuxian"/>
				{{name}}
			</h3>
			<div class="ui-table-main">
				<el-table v-if="!listLoading" v-loading.body="listLoading" :data="list.data" border style="width: 100%"  :max-height="screenHeight" :default-sort = "{prop: 'index', order: 'ascending'}">
          <el-table-column v-for='(first,index) in list.head' :label="first.name" :key='first.name' v-if="first.name != '学校Id'" :header-align="first.children != undefined ? 'center' : 'left'" :sortable="first.name == '班级'" prop="index">
            <el-table-column v-if="first.children != undefined" v-for='(second,index) in first.children' :label="second.name" :key='second.name' sortable :prop="first.value+'.'+second.value">
              <template scope="scope">
                <div v-if="second.name == '进步值' && scope.row[first.value] != undefined" :style="{color: scope.row[first.value][second.value] < 0 ? 'red' : '#333'}">{{scope.row[first.value][second.value]}}</div>
                  <div v-if="second.name != '进步值' && scope.row[first.value]!=undefined">
                    <span v-if="second.value == 'averageRate'">
                    {{(scope.row[first.value][second.value]*100).toFixed(2)}}%</span>
                    <span v-else>{{scope.row[first.value][second.value]}}</span>
                  </div>
                  <div v-if="scope.row[first.value]==undefined">-</div>
              </template>
            </el-table-column>

            <template scope="scope" v-if="first.children == undefined ">
              <div>
                {{scope.row[first.value]}}
              </div>
            </template>
          
          </el-table-column>
        </el-table>
			</div>
			<div v-show="!listLoading" class="page-wrap fr">
	      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page.sync="listQuery.pageNo" :page-sizes="[30, 40, 50, 60, 70, 80]"
	        :page-size="listQuery.pageSize" layout="total, sizes, prev, pager, next, jumper" :total="total">
	      </el-pagination>
	    </div>
	  </div>
	</div>
</template>

<script>
  import { periodList, gradeList, subjectList } from 'utils/data';
  import { getPaperList } from 'api/list';
	import { getLatestTest } from 'utils/auth';
	import { allStudentScore } from 'api/grades';
	export default{
		data() {
			return {
				name: '',
				screenHeight: 0,
        periodList: periodList(),
        gradeList:[],
        subjectList: subjectList(),
        paperList: [],
				listLoading: true,
				total: null,
				list: {
					data: [],
					head: []
				},
				paperQuery: {
          period: '2019',
          grade: '七年级',
          subject: ''
        },
				listQuery: {
					pageNo: 1,
					pageSize: 50,
					paperId: ''
				}
			}
		},
		mounted() {
      this.setForm();
      this.setDefault(this.getPaperData());

			// this.initChart();
			// this.getList();
		},
		methods: {
      setForm(){
        let grade_list = gradeList('all');
        for(let i=0; i<grade_list.length; i++){
          for(var o=0; o<grade_list[i].options.length; o++){
            this.gradeList.push(grade_list[i].options[o]);
          }
        };
      },
      setDefault(callbak){
        this.paperQuery.period = this.periodList[0].value;
        this.paperQuery.grade = this.gradeList[0].label;
        this.paperQuery.subject = this.subjectList[0].value;
        if(typeof callbak == 'function') callbak
      },
      queryChange(val){
        this.getPaperData();
      },
      getPaperData(){
        this.listQuery.paperId = '';
        getPaperList(this.paperQuery).then( res => {
          if(typeof res == 'undefined'){
            this.list.data = [];
            this.listLoading = false;
            return;
          }
          this.paperList = res.data.list;
          this.getList();
        })
      },
			getList(){
				allStudentScore(this.listQuery).then( res=> {
					
				})
			},
      handleSizeChange(val) {
        this.listQuery.pageSize = val;
        this.getList();
      },
      handleCurrentChange(val) {
        this.listQuery.pageNo = val;
        this.getList();
      }
		}
	}
</script>