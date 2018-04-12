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
        <!-- <el-table-column
          prop="id"
          label="id"
           width="100"
          sortable>
        </el-table-column> -->
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
          prop="time"
          label="发布场次"
          width="230" 
          sortable>
        </el-table-column>
        <el-table-column
          cell-style="color:red"
          prop="status"
          label="状态"
          width="180"
          sortable>
        </el-table-column>
        <el-table-column
          label="操作"
          width="180">
          <template scope="props">
            <el-button type="danger" 
            size="small" 
            icon="delete"
             @click="delete_data(props.row.id)" 
             v-if= 'props.row.status==="等待中"'
             >撤销发布</el-button>
            <router-link 
            :to="{name: 'detail', params: {data:props.row}}" 
            tag="span" 
            v-else>
            <el-button type="info" 
             size="small" 
             icon="edit"
             >查看详情</el-button>
              </router-link>
          </template>
        </el-table-column>
      </el-table>
      <bottom-tool-bar>
        <el-button
          type="danger"
          icon="delete"
          size="small"
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
    },
    methods: {
       get_table_data(){
        this.load_data = true
        const that = this
			this.$axios.get(API_HOST+"/betopics",{params:{sort: {createdAt: 0 }}}).then(function(res){
                 console.log(res,res.data.length)
                 if(res.status === 200||res.status === 201){
                     that.load_data = false
                     that.total = res.data.length
                    //  alert(that.total)
                     const pages = that.currentPage -1
                     if(res.data.length-pages*10>10){
                      that.table_data = res.data.slice(pages*10,pages*10+10)
                       that.table_data.forEach((item)=>{
                          item.time =  that.$dateFormat(item.time,"yyyy-MM-dd hh:mm:ss")
                          item.status = item.status -1 >0 ?"已结束":item.status -1<0?"等待中":"进行中"
                      })
                      }else{
                         that.table_data = res.data.slice(pages*10)
                          that.table_data.forEach((item)=>{
                          item.time =  that.$dateFormat(item.time,"yyyy-MM-dd hh:mm:ss") 
                          item.status = item.status -1 >0 ?"已结束":item.status -1 <0?"等待中":"进行中"
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
        this.$confirm('此操作将批量删除选择数据, 是否继续?', '提示', {
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
    //   cellStyle(row, column, rowIndex, columnIndex){
    //      if(column===4){
    //          return {
    //              color:"red"
    //          }
    //      }
    //   },
    delete_data(item){
        const that = this
        this.$confirm('此操作将删除该数据, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
        })
            .then(() => {
            that.load_data = true
             that.$axios.delete(`${API_HOST}/topic/${item}`)
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
