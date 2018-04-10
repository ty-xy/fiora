<template>
  <div class="panel">
    <panel-title :title="$route.meta.title"></panel-title>
    <div class="panel-body">
        <el-form ref="form" :model="form" label-width="80px">
            <el-form-item label="题目">
                <el-input v-model="form.title" class="inner_input" type="textarea"></el-input>
            </el-form-item>
            <el-form-item label="分类">
                    <el-cascader
                        expand-trigger="hover"
                        :options="options"
                        v-model="form.selectedOptions2"
                        @change="handleChange">
                    </el-cascader>
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
            // region: '',
            date1: '',
            date2: '',
            // delivery: false,
            // type: [],
            // resource: '',
            selectedOptions2: []
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
        console.log('submit!',this.form,API_HOST+'/betopic')
        	const user_data = {
				"title":this.form.title,
                "status":3,
                "classify":this.form.selectedOptions2[0],
               
			};
			this.$axios.post(API_HOST+'/betopic',user_data).then(function(res){
                 if(res.status === 200||res.status === 201){
                     that.$message.success("创建成功")
                     that.form.title = ""
                     console.log(res,user_data)
                 }
			}).catch(function(error){
				console.log(error);
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
        width:800px;
    }
</style>
