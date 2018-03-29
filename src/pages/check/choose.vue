<template>
  <div class="panel">
    <panel-title :title="$route.meta.title"></panel-title>
    <div class="panel-body"
         v-loading="load_data"
         element-loading-text="拼命加载中">
      <el-row>
        <el-col :span="8">
          <el-form ref="form" :model="form" :rules="rules" label-width="100px">
            <el-form-item label="分类:">
               <el-input v-model="form.classify" disabled style="width:250px"></el-input>
            </el-form-item>
            <el-form-item label="题目:"  :title="form.title">
              <el-input v-model="form.title" disabled style="width:250px" type="textarea"></el-input>
            </el-form-item>
            <el-form-item label="选择时间:" prop="time">
              <el-date-picker
                v-model="form.time"
                type="date"
                :editable="false"
                format="yyyy-MM-dd "
                @change="on_change_birthday"
                placeholder="选择场次"
                style="width: 250px;">
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
              <el-button @click="$router.back()">取消</el-button>
            </el-form-item>
          </el-form>
        </el-col>
      </el-row>
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
        route_id: this.$route.params.id,
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
        // this.$axios.get(`${API_HOST}/betopic/${that.route_id}`).then(function(res){
        //        console.log(res,"res") 
        //          if(res.status === 200||res.status === 201){
        //              that.load_data = false
        //              that.form = res.data
        //          }
        // })
        this.$axios.get(`http://localhost:1337/betopic/${that.route_id}`).then(function(res){
               console.log(res,"res") 
                 if(res.status === 200||res.status === 201){
                     that.load_data = false
                    that.form = res.data
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
          data.status = 1;
          data.time  += data.value 
          data.time = new Date(data.time).getTime()
          delete data.id 
          delete data.value
          console.log(data,"data",data.time)
     	//   this.$axios.post(API_HOST+'/topic',data).then(function(res){
        //          if(res.status === 200||res.status === 201){
        //              that.$message.success("创建成功")
        //             //  console.log(res) 
        //             //  already[data.time]=data.value
        //               that.on_submit_loading = false
        //               const user_data ={
        //                   status :1,
        //                   chang:data.time
        //               }
        //               that.$axios.put(`${API_HOST}/betopic/${that.route_id}`,user_data).then(function(ress){
        //                   if(res.status===200||res.status===201){
        //                        setTimeout(that.$router.back(), 500)
        //                   }
        //               })
        //          }
		// 	}).catch(function(error){
		// 		console.log(error);
		// 	})
            this.$axios.post("http://localhost:1337/topic",data).then(function(res){
                if(res.status === 200||res.status === 201){
                    that.$message.success("创建成功")
                    //  console.log(res) 
                    //  already[data.time]=data.value
                    that.on_submit_loading = false
                    const t = data.time
                    const user_data ={
                        status :1,
                        chang:new Date(t).toLocaleString(), 
                        title:data.title,
                        classify:data.classify
                    }
                    that.$axios.put(`http://localhost:1337/betopic/${that.route_id}`,user_data).then(function(ress){
                        if(res.status===200||res.status===201){
                            console.log(ress)
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
    .el-row{
       display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
</style>