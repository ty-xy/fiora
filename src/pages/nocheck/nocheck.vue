<template>
  <div class="panel">
    <panel-title :title="$route.meta.title"></panel-title>
      <div class="panel-body">
      <el-table
        :data="table_data"
        v-loading="load_data"
        element-loading-text="拼命加载中"
        border
        @selection-change="on_batch_select"
        style="width: 100%;">
        <el-table-column
          type="selection"
          width="55">
        </el-table-column>
        <el-table-column
          prop="classify"
          label="分类"
          width="100"
          sortable>
        </el-table-column>
        <el-table-column
          prop="title"
          label="题目"
          sortable>
        </el-table-column>
        <el-table-column
          prop="createdAt"
          label="出题时间"
          width="240" 
          sortable>
        </el-table-column>
        <el-table-column
          label="操作"
          width="240">
          <template scope="props">
            <!-- <router-link :to="{name: 'choose', params: {id: props.row.id}}" tag="span"> -->
              <el-button 
              type="info" 
              size="middle" 
              icon="edit" 
              :disabled="props.row.status===1" 
              @click="changeData(props.row.id)"
              >通过</el-button>
              <el-dialog title="设定场次" :visible.sync="centerDialogVisible" width="30%" center=true>
                <!-- <Choose :routeId="routerId"></Choose> -->
                <div class="panel-body"
                    :model="form"
                    v-loading="load_data"
                    element-loading-text="拼命加载中">
                            <el-row>
                                <el-col :span="12">
                                <el-form ref="form" :model="form" :rules="rules" label-width="100px">
                                    <el-form-item label="分类:">
                                    <el-input v-model="form.classify" disabled ></el-input>
                                    </el-form-item>
                                    <el-form-item label="题目:"  :title="form.title">
                                    <el-input v-model="form.title" disabled  type="textarea"></el-input>
                                    </el-form-item>
                                    <el-form-item label="选择时间:" prop="time">
                                    <el-date-picker
                                        v-model="form.time"
                                        type="date"
                                        :editable="false"
                                        format="yyyy-MM-dd "
                                        @change="on_change_birthday"
                                        placeholder="选择场次"
                                        >
                                    </el-date-picker>
                                    </el-form-item>
                                    <el-form-item label="选择场次:" prop="value">
                                        <el-select v-model="form.value" placeholder="请选择" @change ="on_submit_select">
                                            <el-option
                                            v-for="item in options"
                                            :key="item.value"
                                            :label="item.label"
                                            :value="item.value">
                                            </el-option>
                                        </el-select>
                                    </el-form-item>
                                    <el-form-item>
                                    <el-button type="primary" @click="on_submit_form" :loading="on_submit_loading">立即提交</el-button>
                                    <el-button @click="centerDialogVisible=false">取消</el-button>
                                    </el-form-item>
                                </el-form>
                                </el-col>
                            </el-row>
                </div>
                 <!-- <div slot="footer" class="dialog-footer">
                <el-button @click="centerDialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="centerDialogVisible= false">确 定</el-button>
                </div> -->
            </el-dialog>
            <!-- </router-link> -->
            <el-button type="danger" size="middle" icon="delete" @click="delete_data(props.row.id)">pass</el-button>
          </template>
          
        </el-table-column>
      </el-table>
      <bottom-tool-bar>
        <el-button
          type="danger"
          icon="delete"
          size="middle"
          :disabled="batch_select.length === 0"
          @click="on_batch_del"
          slot="handler">
          <span>批量删除</span>
        </el-button>
        <div slot="page">
          <el-pagination
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-size="10"
            layout="total, prev, pager, next"
            :total="total">
          </el-pagination>
        </div>
      </bottom-tool-bar>
    </div>
  </div>
</template>
<script type="text/javascript">
  import {panelTitle, bottomToolBar} from 'components'
  import Choose from './choose'
  import {API_HOST} from '../../util/config'
  export default{
    data(){
      return {
        msg: 'home page' , 
        form: {
          title:null,
          status:"",
          classify:null,
          time: this.$dateFormat(new Date, "yyyy-MM-dd"),
          address: null,
          zip: 412300,
          value:""
        },
        load_data: false,
        on_submit_loading: false,
        rules: {
          time: [{required: true, message: '时间不能为空', trigger: 'blur'}],
          value:[{required: true, message: '场次不能为空', trigger: 'blur'}]
        },
        options: [],       
        batch_select: [],
        table_data: null,
          //当前页码
         currentPage: 1,
        //数据总条目
        total: 0,
        routerId:'',
        //每页显示多少条数据
        length: 10,
        centerDialogVisible: false,
        load_data: false,
      }
    },
    name:'user',
     created(){
      this.get_table_data()
      
      console.log(this.$dateFormat(new Date, "yyyy-MM-dd hh:mm:ss"))
    },
    methods: {
        get_form_data(){
        this.load_data = true
        const that = this
        this.$axios.get(`${API_HOST}/betopic/${that.routerId}`).then(function(res){
               console.log(res,"res") 
                 if(res.status === 200||res.status === 201){
                     that.load_data = false
                     that.form = res.data
                 }
           })
        },
        get_table_data(){
        this.load_data = true
        const that = this
			this.$axios.get(API_HOST+"/betopic",{params:{sort: {createdAt: 0 }}}).then(function(res){
                 console.log(res,res.data.length)
                 if(res.status === 200||res.status === 201){
                     that.load_data = false
                     that.total = res.data.length
                    //  alert(that.total)
                     const pages = that.currentPage -1
                     if(res.data.length-pages*10>10){
                      that.table_data = res.data.slice(pages*10,pages*10+10)
                      that.table_data.forEach((item)=>{
                          item.createdAt =  that.$dateFormat(new Date(item.createdAt), "yyyy-MM-dd hh:mm:ss") 
                          console.log(item.createdAts)
                      })
                      }else{
                         that.table_data = res.data.slice(pages*10)
                         that.table_data.forEach((item)=>{
                          item.createdAt =  that.$dateFormat(new Date(item.createdAt), "yyyy-MM-dd hh:mm:ss")
                          console.log(item.createdAts)
                      })
                      }
                      console.log(pages,that.table_data)
                     that.$message.success("获取成功")
                 }
			}).catch(function(error){
				console.log(error);
			})

      },
      on_change_birthday(val){
        this.$set(this.form, 'time', val)
      },
      on_submit_select(val){
       this.on_change_time()
      },
      on_batch_del(){
        this.$confirm('此操作将批量删除选择数据,无法恢复, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
      },
     on_batch_select(val){
        this.batch_select = val
      },
      //页码选择器
      handleCurrentChange(val) {
        const that = this;
        that.currentPage = val
        this.get_table_data()
      },
      changeData(item){
       this.centerDialogVisible = true
       this.routerId=item
       this.get_form_data()
      },
       on_change_time() {
           let time  =7
           const that = this
           let option =[]
          for(let i = 0;i<24;i++){
            if(time<10){
            //  console.log(`0${time}:13:13`)
             option.push({value : `0${time}:13:13`,label:`0${time}:13:13`})
            }else if(time<24){
             option.push({value : `${time}:13:13`,label:`${time}:13:13`})
            }else{
                time = 0
             option.push({value : `0${time}:13:13`,label:`0${time}:13:13`})
            }
             time++
          }
          this.options = option
      },
      //提交
      on_submit_form(){
          const that = this
        this.$refs.form.validate((valid) => {
          if (!valid) return false
          this.on_submit_loading = true
          let data = this.form
          data.yearmonth = data.time
          data.time  += data.value 
          data.time = new Date(data.time).getTime()
          const statusT = data.time - Date.now()
          data.status = statusT>0 ? 0 : (120 * 60 * 1000 +  statusT)>0 ? 1 : 2
          delete data.id 
          delete data.value
     	  this.$axios.post(API_HOST+'/topic',data).then(function(res){
                 if(res.status === 200||res.status === 201){
                     that.$message.success("创建成功")
                      const t = data.time
                      that.on_submit_loading = false
                      const user_data ={
                          status :1,
                          chang:new Date(t).toLocaleString()
                      }
                      console.log(user_data.status)
                      that.$axios.put(`${API_HOST}/betopic/${that.route_id}`,user_data).then(function(ress){
                          if(res.status===200||res.status===201){
                             that.centerDialogVisible = false
                          }
                      })
                 }
			}).catch(function(error){
				that.$confirm(error.response.data.message, '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then((ress) => {
                        data.type=ress
                        that.$axios.post(API_HOST+'/topic',data)
                         .then((res) => {
                            if(res.status === 200||res.status === 201){
                               that.$message.success("再次创建")
                                that.load_data = false
                            }
                        }).catch(() => {
                       })
                    }).catch(() => {
                 })
			})
        })
      },
      delete_data(item){
        const that = this
        this.$confirm('此操作将删除该数据, 无法恢复,是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        }).then(() => {
            that.load_data = true
             that.$axios.delete(`${API_HOST}/betopic/${item}`)
                .then((res) => {
                    if(res.status === 200||res.status === 201){
                        that.get_table_data()
                        that.$message.success("删除成功")
                         that.load_data = false
                    }
                })
                .catch(() => {
                })
            })
            .catch(() => {
            })
    },
    },
    mounted:function(){
	},
    components: {
      panelTitle,
      bottomToolBar,
      Choose
    }
  }
</script>
<style scoped lang="scss" type="text/scss">
   .el-button{
       padding:6px 15px;
   }
   .el-dialog__header {
    padding: 20px 20px 0;
    text-align: center;
}
</style>
