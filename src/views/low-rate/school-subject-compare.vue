<!-- <template>
	<div>
		<div class="ui-search-wrap" id="ui-search-wrap">
      <el-form :inline="true">

        <el-form-item label="学校">
          <el-select v-model="listQuery.schoolId" filterable clearable placeholder="请选择" @change="getList('school')">
            <el-option v-for="item in schoolList" :label="item.name" :value="item.id" :key="item.id">
            </el-option>
          </el-select>
        </el-form-item>

         <el-form-item label="届">
          <el-select v-model="listQuery.period" filterable clearable placeholder="请选择" @change="getList('period')">
            <el-option v-for="item in periodList" :label="item.label" :value="item.label" :key="item.value">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="年级">
          <el-select v-model="listQuery.grade" filterable clearable placeholder="请选择" @change="getList('grade')">
            <el-option v-for="item in gradeList" :label="item.label" :value="item.label" :key="item.label">
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
				<el-table v-loading.body="listLoading" :data="list.data" border style="width: 100%">
	        <el-table-column v-for='(first,index) in list.head' :label="first.name" :key='first.name' :align="first.children != undefined ? 'center' : 'left'">
	          <el-table-column v-if="first.children != undefined" v-for='(second,index) in first.children' :label="second.name" :key='second.name'>
	            <template scope="scope">
	              <div>{{scope.row[first.value][second.value]}}</div>
	            </template>
	          </el-table-column>

	          <template scope="scope" v-if="first.children == undefined">
	            <div>{{scope.row[first.value]}}</div>
	          </template>
	        
	        </el-table-column>
	    	</el-table>
				<div v-show="!listLoading" class="page-wrap fr">
		      <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page.sync="listQuery.pageNo" :page-sizes="[30, 40, 50, 60, 70, 80]"
		        :page-size="listQuery.pageSize" layout="total, sizes, prev, pager, next, jumper" :total="total">
		      </el-pagination>
		    </div>
			</div>
		</div>
	</div>
</template>
<script>
	import { mapGetters } from 'vuex';
  import { getSchoolList } from 'api/info-administration/school';
  import { periodList, gradeList } from 'utils/data';
	import { getSchoolRateBySchoolIdAndSubjectAndPeriodAndGrade } from 'api/low_rate';
	export default {
		data() {
			return {
				name: '学校各科优良率比较',
        periodList: periodList(),
        gradeList:[],
        schoolList: [],
				list: [],
				screenHeight: 0,
				total: null,
        listLoading: true,
        schoolQuery: {
          pageNo: 1,
          pageSize: 100,
          name: '',
          type: ''
        },
        listQuery: {
          pageNo: 1,
         	pageSize: 50,
         	schoolId: '',
         	period: 2017,
         	grade: '七年级'
        },
        fromData: {
					selectedSubject: '语文'
        }
			}
		},
		computed: {
      ...mapGetters([
        'schoolId'
      ])
    },
		created() {
    },
		mounted() {
      this.setForm();
			this.getSchoolList();
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
        
        this.listQuery.period = this.periodList[0].value;
        this.listQuery.grade = this.gradeList[0].label;
        this.listQuery.schoolId = this.schoolList[0].id;
        this.getList();
      },
      getSchoolList(){
        getSchoolList(this.schoolQuery).then( res => {
          this.schoolList = res.data.list;
          this.setDefault()
        })
      },
			getList() {
        this.listLoading = true;
        getSchoolRateBySchoolIdAndSubjectAndPeriodAndGrade(this.listQuery).then(res => {
          this.list['data'] = res.data.data.data;
          this.list['head'] = res.data.data.head;
          this.total = res.data.data.total;
          this.listLoading = false;
        })
      },
      handleSizeChange(val) {
        this.listQuery.pageSize = val;
        this.getList();
      },
      handleCurrentChange(val) {
        this.listQuery.page = val;
        this.getList();
      },
      formatter(val) {
      	if(val < 60 ) {
      		return 'red'
      	}else if(val == 60 ) {
      		return 'rgb(251,178,23)'
      	}else if(val>90) {
      		return 'rgb(6,128,67)'
      	}
      },
      onSearch() {
      	this.listQuery.page++;
      	if(this.listQuery.page == 6){
      		this.listQuery.page = 1;
      	}
      	this.getList();
      }
		}
	}
</script> -->