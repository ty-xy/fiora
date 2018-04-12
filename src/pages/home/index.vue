<template>
  <div class="panel">
    <panel-title :title="$route.meta.title"></panel-title>
    <div class="panel-body panel-b">
        <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="题目">
                <el-input v-model="form.title" class="inner_input" type="textarea"></el-input>
            </el-form-item>
            <el-form-item label="分类">
                 <el-input v-model="form.selectedOptions2"  type="text" class="inner_input"></el-input>
                    <!-- <el-cascader
                        expand-trigger="hover"
                        :options="options"
                        v-model="form.selectedOptions2"
                        @change="handleChange">
                    </el-cascader> -->
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="onSubmit">立即创建</el-button>
                <el-button>取消</el-button>
            </el-form-item>
        </el-form>
    </div>
  </div>
</template>
<script type="text/javascript">
  import {panelTitle} from 'components'
  import {API_HOST} from '../../util/config'
  export default{
    data(){
      return {
        msg: 'home page',
        form: {
            title: '',
            date1: '',
            date2: '',
            selectedOptions2: ""
        },
         options:[{
             value:"生活" ,
             label:"生活",
              children:[{
                value:"情感",
                label:"情感"
              },{
                 value:"美食",
                 label:"美食"
              }]
         }],
         
      }
    },
    name:'user',
    methods: {
      onSubmit() {
          const that = this;
        	let  user_data = {
				"title":this.form.title,
                "status":3,
                "classify":this.form.selectedOptions2,
			};
			this.$axios.post(API_HOST+'/betopic',user_data).then(function(res){
                 if(res.status === 200||res.status === 201){
                     that.$message.success("创建成功")
                     that.form.title = ""
                     console.log(res,user_data)
                 }
                console.log(res,user_data) 
			}).catch(function(error){
				if(error.response.status===503){
                    that.$confirm(error.response.data.message, '提示', {
                        confirmButtonText: '确定',
                        cancelButtonText: '取消',
                        type: 'warning'
                    }).then((ress) => {
                        user_data.type=ress
                        console.log(user_data)
                        that.$axios.post(API_HOST+'/betopic',user_data)
                         .then((res) => {
                            if(res.status === 200||res.status === 201){
                               that.$message.success("再次创建")
                                that.form.title = ""
                            }
                        }).catch(() => {
                       })
                    }).catch(() => {
                 })
                }
			})
      },
      handleChange(value){
          console.log(value)
      }
    },
    mounted:function(){
	},
    components: {
      panelTitle
    }
  }
</script>
<style scoped lang="scss" type="text/scss">
    .inner_input{
        max-width:100% !important;
        width:600px;
    }
    .panel-b{
        display:flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
    }
</style>
