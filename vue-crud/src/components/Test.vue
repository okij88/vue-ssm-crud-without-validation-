<template>
	<div>
		<div class="title">CRUD</div>
    	<hr>
      <div style="margin-bottom:10px">
        <a-button @click="handleAddClicked">Add</a-button>
        <a-button @click="handleChoseDeleted">Delete</a-button>
      </div>
    	<a-table :rowSelection="rowSelection" :columns="columns" :dataSource="data" bordered :pagination="false">
		    <template v-for="col in ['empId', 'empName', 'gender','email','deptName']" :slot="col" slot-scope="text, record, index">
		      {{text}}
		    </template>
		    <template slot="operation" slot-scope="text, record, index" width="100px">
		      <div class='editable-row-operations'>
		        <span>
		          <a @click="() => edit(record.key)" href="javascript:;">Edit</a>
              <a-popconfirm
                title="Sure to delete?"
                @confirm="() => onDelete(record.key)">
                <a href="javascript:;">Delete</a>
              </a-popconfirm>
		        </span>
		      </div>
		    </template>
		</a-table>
		<div style="margin-top:20px" align="right">
  			<a-pagination v-model="current" :total="page.total" :pageSize="page.pageSize"
          @change="pageChange"/>
  	</div>
    <a-modal 
        title="Add"
        v-model="visibleAdd"
        @ok="handleAddOk"
      >
        <a-form style="margin-right:15%">
          <a-form-item
            v-bind="formItemLayout"
            label="empName"
          >
            <a-input placeholder="empName" v-model="empAdd.empName"/>
          </a-form-item>
          <a-form-item
            v-bind="formItemLayout"
            label="E-mail"
          >
            <a-input placeholder="empName@atguigu.com" v-model="empAdd.email"/>
          </a-form-item>
          <a-form-item
            v-bind="formItemLayout"
            label="gender"
          >
            <a-radio-group v-model="empAdd.gender">
              <a-radio value="M">
                男
              </a-radio>
              <a-radio value="W">
                女
              </a-radio>
            </a-radio-group>
          </a-form-item>
          <a-form-item
            v-bind="formItemLayout"
            label="deptName"
          >
            <a-select v-model="empAdd.dId"> 
              <a-select-option :key="dept.deptId" v-for="dept in depts">
                {{dept.deptName}}
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-form>
      <template slot="footer">
        <a-button key="submit" type="primary" @click="handleAddOk">提交</a-button>
        <a-button key="back" @click="handleAddCancel">关闭</a-button>
      </template>
    </a-modal>
    <a-modal 
        title="编辑"
        v-model="visibleUpdate"
        @ok="handleUpdateOk"
      >
        <a-form style="margin-right:15%">
          <a-form-item
            v-bind="formItemLayout"
            label="empName"
          >
            <span class="ant-form-text" >
              {{emp.empName}}
            </span>
          </a-form-item>
          <a-form-item
            v-bind="formItemLayout"
            label="E-mail"
          >
            <a-input v-model="emp.email"/>
          </a-form-item>
          <a-form-item
            v-bind="formItemLayout"
            label="gender"
          >
            <a-radio-group v-model="emp.gender">
              <a-radio value="M">
                男
              </a-radio>
              <a-radio value="W">
                女
              </a-radio>
            </a-radio-group>
          </a-form-item>
          <a-form-item
            v-bind="formItemLayout"
            label="deptName"
          >
            <a-select v-model="emp.dId">
              <a-select-option :key="dept.deptId" v-for="dept in depts">
                {{dept.deptName}}
              </a-select-option>
            </a-select>
          </a-form-item>
        </a-form>
      <template slot="footer">
        <a-button key="submit" type="primary" @click="handleUpdateOk">提交</a-button>
        <a-button key="back" @click="handleUpdateCancel">关闭</a-button>
      </template>
    </a-modal>
	</div>
</template>

<script>
import resources from '@/resources'
const columns = [{
  title: 'empId',
  dataIndex: 'empId',
  scopedSlots: { customRender: 'empId' },
}, {
  title: 'empName',
  dataIndex: 'empName',
  scopedSlots: { customRender: 'empName' },
}, {
  title: 'gender',
  dataIndex: 'gender',
  scopedSlots: { customRender: 'gender' },
}, {
  title: 'email',
  dataIndex: 'email',
  scopedSlots: { customRender: 'email' },
}, {
  title: 'deptName',
  dataIndex: 'deptName',
  scopedSlots: { customRender: 'deptName' },
}, {
  title: 'operation',
  dataIndex: 'operation',
  width: '10%',
  scopedSlots: { customRender: 'operation' },
}]

export default {
  data () {
    return {
      data:[],
      columns,
      page:{},
      current:1,
      depts:[],
      emp:{},
      visibleUpdate:false,
      visibleAdd:false,
      selectedRowKeys: [],
      formItemLayout: {
        labelCol: {
          xs: { span: 24 },
          sm: { span: 8 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },
      },
      empAdd:{
        empName:'',
        gender:'',
        email:'',
        dId:''
      }
    }
  },
  computed: {
    rowSelection() {
      const { selectedRowKeys } = this;
      return {
        selectedRowKeys,
        onChange: this.onSelectChange,
        hideDefaultSelections: true,
        selections: [{
          key: 'all-data',
          text: 'Select All Data',
          onSelect: () => {
            this.selectedRowKeys = [...Array(46).keys()]; // 0...45
          },
        }, {
          key: 'odd',
          text: 'Select Odd Row',
          onSelect: (changableRowKeys) => {
            let newSelectedRowKeys = [];
            newSelectedRowKeys = changableRowKeys.filter((key, index) => {
              if (index % 2 !== 0) {
                return false;
              }
              return true;
            });
            this.selectedRowKeys = newSelectedRowKeys;
          },
        }, {
          key: 'even',
          text: 'Select Even Row',
          onSelect: (changableRowKeys) => {
            let newSelectedRowKeys = [];
            newSelectedRowKeys = changableRowKeys.filter((key, index) => {
              if (index % 2 !== 0) {
                return true;
              }
              return false;
            });
            this.selectedRowKeys = newSelectedRowKeys;
          },
        }],
        onSelection: this.onSelection,
      }
    }
  },
  methods: {
    handleAddClicked(){
      this.visibleAdd = true
    },
    handleAddOk(e) {
      console.log(this.empAdd)
      this.addEmp(this.empAdd)
      this.empAdd = {}
    },
    handleAddCancel(e) {
      console.log('Clicked cancel button');
      this.visibleUpdate = false
    },
    handleChoseDeleted(){
      this.deleteEmps(this.selectedRowKeys);
    },
    handleUpdateOk() {
      this.updateEmp(this.emp);
    },
    handleUpdateCancel(e) {
      console.log('Clicked cancel button');
      this.visibleUpdate = false
    },
    edit (key) {
      console.log("员工Id："+key)
      this.visibleUpdate = true
      this.getEmp(key)
    },
    onDelete(key){
      console.log("员工Id："+key)
      this.deleteEmp(key)
      this.getEmps(this.current)
    },
    pageChange(e){
      console.log(e)
      this.getEmps(e)
    },
    addEmp(empAdd){
      let that = this;
      let url = resources.addEmp();
      this.$ajax
      .post(url,empAdd)
      .then(res => {
        this.visibleAdd = false
        this.getEmps(Math.ceil(this.page.total/this.page.pageSize))
        this.current = Math.ceil(this.page.total/this.page.pageSize)
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    updateEmp(emp){
      let that = this;
      let url = resources.updateEmp();
      this.$ajax
      .post(url,emp)
      .then(res => {
        this.visibleUpdate = false
        this.getEmps(this.current)
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    deleteEmp(empId){
      let that = this;
      let url = resources.deleteEmp();
      this.$ajax
      .get(url,{
        params:{
          empId:empId
        }
      })
      .then(res => {
        console.log("delete success")
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    deleteEmps(selectedRowKeys){
      console.log(selectedRowKeys)
      let that = this;
      let url = resources.deleteEmps();
      this.$ajax
      .get(url,{
        params:{
          empIdList:selectedRowKeys
        }
      })
      .then(res => {
        console.log("delete success")
        this.getEmps(this.current)
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    getEmp(empId){
      let that = this;
      let url = resources.getEmp();
      this.$ajax
      .get(url,{
        params:{
          empId:empId
        }
      })
      .then(res => {
        that.emp = res.data.extend.emp
        console.log(that.emp)
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    getEmps(current){
      let that = this;
      let url = resources.getEmps();
      this.$ajax
      .get(url,{
        params:{
          pn:current
        }
      })
      .then(res => {
        that.page = res.data.extend.pageInfo
        // that.emps = {}
        this.data = []
        var size = res.data.extend.pageInfo.size
        console.log(that.page,size)
        for(var i=0;i<size;i++){
          var gender = that.page.list[i].gender =='M'?"男":"女"
          this.data.push({key:that.page.list[i].empId,empId:that.page.list[i].empId,
          empName:that.page.list[i].empName,gender:gender,email:that.page.list[i].email,
          deptName:that.page.list[i].department.deptName})
        }
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    getDepts(){
      let that = this;
      let url = resources.getDepts();
      this.$ajax
      .get(url)
      .then(res => {
       that.depts = res.data.extend.depts;
       console.log(that.depts)
      })
      .catch(function(error) {
        alert("网络请求出错啦，请联系管理员！");
        console.log(error);
      });
    },
    onSelectChange (selectedRowKeys) {
      console.log('selectedRowKeys changed: ', selectedRowKeys);
      this.selectedRowKeys = selectedRowKeys
    },
  },
  mounted:function() {
      this.getEmps(1);
      this.getDepts();
  }
}
</script>
</script>
<style scoped>
.editable-row-operations a {
  margin-right: 8px;
}
</style>