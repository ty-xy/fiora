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
            <router-link :to="{name: 'choose', params: {id: props.row.id}}" tag="span">
              <el-button type="info" size="middle" icon="edit" :disabled="props.row.status===1">通过</el-button>
            </router-link>
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
  import {API_HOST} from '../../util/config'
  export default{
    data(){
      return {
        msg: 'home page' ,         
        batch_select: [],
        table_data: null,
          //当前页码
         currentPage: 1,
        //数据总条目
        total: 0,
        //每页显示多少条数据
        length: 10,
        load_data: false,
      }
    },
    name:'user',
     created(){
      this.get_table_data()
      console.log(this.$dateFormat(new Date, "yyyy-MM-dd hh:mm:ss"))
    },
    methods: {
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
    delete_data(item){
        const that = this
        this.$confirm('此操作将删除该数据, 无法恢复,是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        })
            .then(() => {
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
      bottomToolBar
    }
  }
</script>
<style scoped lang="scss" type="text/scss">
   .el-button{
       padding:6px 15px;
   }
</style>
