<template>
  <div class="panel">
    <panel-title :title="$route.meta.title"></panel-title>
    <div class="panel-body"
         v-loading="load_data"
         element-loading-text="拼命加载中">
         <el-row>
            <h1 class="title">{{$route.params.data.title}}</h1>
            <el-button type="warning" >{{$route.params.data.status}}</el-button>
         </el-row>
          <el-row>
             <p class="answer">阅览 <span class="zhiti">{{$route.params.data.messageNum||0}}</span></p>
             <p class="answer answer-left" >回答<span class="zhiti">{{$route.params.data.toAnswer.length||0}}</span>
             </p>
         </el-row>
         <el-table
        :data="options"
        v-loading="load_data"
        element-loading-text="拼命加载中"
        border
        style="width: 100%;">
        <el-table-column
          prop="createdBy.username"
          label="用户"
          width="100"
          >
        </el-table-column>
        <el-table-column
          prop="body"
          label="回答内容"
          >
        </el-table-column>
        <el-table-column
          prop="upVotes.length"
          label="获赞"
          width="230" 
          sortable>
        </el-table-column>
        <el-table-column
          cell-style="color:red"
          prop="createdAt"
          label="时间"
          width="300"
          sortable>
        </el-table-column>
        <el-table-column
          label="操作"
          width="180">
          <template scope="props">
            <el-button type="danger"
            class="button"
             plain
             @click="delete_data(props.row.id)" 
             >删除该回答</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>
<script type="text/javascript">
  import {panelTitle} from 'components'
  import {API_HOST} from '../../util/config'
  export default{
    data(){
      return {
        form: {
          title:null,
          status:"",
          classify:null,
          time: this.$dateFormat(new Date, "yyyy-MM-dd"),
          address: null,
          zip: 412300,
          value:""
        },
        route_id: this.$route.params.data.id,
        load_data: false,
        on_submit_loading: false,
        rules: {
          time: [{required: true, message: '时间不能为空', trigger: 'blur'}],
          value:[{required: true, message: '场次不能为空', trigger: 'blur'}]
        },
        options: [],
      }
    },
    created(){
      this.route_id && this.get_form_data()
      console.log(this.route_id)
    //   this.on_change_time()
    //   console.log(already)
    },
    methods: {
      //获取数据
      get_form_data(){
        this.load_data = true
        const that = this
        this.$axios.get(`${API_HOST}/detail`,{params:{search:{topic:that.route_id}}}).then(function(res){
               console.log(res,"res") 
                 if(res.status === 200||res.status === 201){
                     that.load_data = false
                     that.options=res.data
                 }
        })
      },
      //时间选择改变时
      on_change_birthday(val){
        this.$set(this.form, 'time', val)

      },
      on_change_status(val){
        this.$set(this.form, 'status', val)
      },
      on_submit_select(val){
       this.on_change_time()

      },
      //页码选择
       handleCurrentChange(val) {
        this.currentPage = val
        // this.get_table_data()
      },
      //场次函数
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
          const data = this.form
          data.yearmonth = data.time
          data.time  += data.value 
          data.time = new Date(data.time).getTime()
          const statusT = data.time - Date.now()
          data.status = statusT>0 ? 0 : (120 * 60 * 1000 +  statusT)>0 ? 1 : 2
          console.log(data.status)
          delete data.id 
          delete data.value
          console.log(data,"data",data.time)
     	  this.$axios.post(API_HOST+'/topic',data).then(function(res){
                 if(res.status === 200||res.status === 201){
                     that.$message.success("创建成功")
                    //  console.log(res) 
                    //  already[data.time]=data.value
                      const t = data.time
                      that.on_submit_loading = false
                      const user_data ={
                          status :1,
                          chang:new Date(t).toLocaleString()
                      }
                      console.log(user_data.status)
                      that.$axios.put(`${API_HOST}/betopic/${that.route_id}`,user_data).then(function(ress){
                          if(res.status===200||res.status===201){
                               setTimeout(that.$router.back(), 500)
                          }
                      })
                 }
			}).catch(function(error){
				console.log(error);
			})
        })
      }
    },
    components: {
      panelTitle
    }
  }
</script>
<style scoped  lang="scss" type="text/scss" rel="stylesheet/scss">
    .title{
        font-family: PingFangHK-Medium;
        font-size: 24px;
        color: #333333;
        letter-spacing: 0.19px;
        margin-right:17px;
    }
    .el-row{
       display: flex;
        // flex-direction: column;
        align-items: center;
        margin-left:30px;
        height:56px;
        // justify-content: center;
    }
    .answer{
        font-family: PingFangHK-Regular;
        font-size: 16px;
        color: #999999;
        letter-spacing: 0.19px;
    }
    .answer-left{
        margin-left:16px;
    }
    .zhiti{
         color: #000;
         margin-left:2px;
    }
    .button{
        font-family: PingFangHK-Regular;
        font-size: 14px;
        color: #E35C5D;
        letter-spacing: 0;
    }
</style>