<template>
  <div>

    <div class="card" style="margin: 5px">
      <el-input v-model="data.name" style="width: 300px; margin-right: 10px" placeholder="请输入名称查询"></el-input>
      <el-button type="primary" @click="load">查询</el-button>
      <el-button type="info" style="margin: 0 10px" @click="reset">重置</el-button>
    </div>

    <div class="card">
      <div style="margin-bottom: 10px">
        <el-button type="primary" @click="handleAdd">新增</el-button>
      </div>
      <el-table :data="data.tableData" stripe>
        <el-table-column label="账号" prop="username"></el-table-column>
        <el-table-column label="名称" prop="name"></el-table-column>
        <el-table-column label="头像">
          <template #default="scope">
            <el-image :src="scope.row.avatar" style="width: 40px; height: 40px; border-radius: 50%"></el-image>
          </template>
        </el-table-column>
        <el-table-column label="角色" prop="role"></el-table-column>
        <el-table-column label="账户余额" prop="balance"></el-table-column>
        <el-table-column label="操作" align="center" width="160">
          <template #default="scope">
<!--            <el-button type="primary" @click="handleEdit(scope.row)">编辑</el-button>-->
            <el-button type="danger" @click="del(scope.row.id)">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>

    <div class="card">
      <el-pagination @current-change="load" background layout="total, prev, pager, next"
                     v-model:page-size="data.pageSize" v-model:current-page="data.pageNum" :total="data.total"/>
    </div>

<!--    <el-dialog title="信息" width="30%" v-model="data.formVisible" :close-on-click-modal="false" destroy-on-close>-->
<!--      <el-form :model="data.form" label-width="80px" style="padding-right: 30px">-->
<!--        <el-form-item label="头像" prop="avatar">-->
<!--          <el-upload :action="uploadUrl" list-type="picture" :on-success="handleImgSuccess">-->
<!--            <el-button type="primary">上传图片</el-button>-->
<!--          </el-upload>-->
<!--        </el-form-item>-->
<!--        <el-form-item label="账号" prop="username">-->
<!--          <el-input :disabled="data.form.id" v-model="data.form.username" autocomplete="off" />-->
<!--        </el-form-item>-->
<!--        <el-form-item label="名称" prop="name">-->
<!--          <el-input v-model="data.form.name" autocomplete="off" />-->
<!--        </el-form-item>-->
<!--      </el-form>-->
<!--      <template #footer>-->
<!--      <span class="dialog-footer">-->
<!--        <el-button @click="data.formVisible = false">取 消</el-button>-->
<!--        <el-button type="primary" @click="save">保 存</el-button>-->
<!--      </span>-->
<!--      </template>-->
<!--    </el-dialog>-->

  </div>
</template>

<script setup>
  import {reactive} from "vue";
  import {Search} from "@element-plus/icons-vue";
  import request from "@/utils/request";
  import {ElMessage, ElMessageBox} from "element-plus";

  const data=reactive({
    name:null,
    tableData:[],
    total:0,
    pageNum:1,
    pageSize:5
  })

  //分页查询
  const load = () => {
    request.get('/user/selectPage',{
      params:{//给usercontroller selectpage()传参数
        pageNum:data.pageNum,
        pageSize:data.pageSize,
        name:data.name
      } // /user/selectPage?pageNum=1&pageSize=5&name=张三
    }).then(res=>{
      if(res.code==='200'){
        data.tableData=res.data?.list
        data.total=res.data?.total
      }
      else {
        ElMessage.error(res.msg)
      }
    })
  }

  //重置
  const reset = () => {
    data.name=null
    load()
  }

  // /user/delete/123
  const del = (id) => {
    ElMessageBox.confirm('确定删除吗？','删除确认',{type:'warning'}).then(res=>{
      request.delete('/user/delete/' + id).then(res=>{
        if(res.code==='200'){
          ElMessage.success("操作成功")
          load()
        }
        else {
          ElMessage.error(res.msg)
        }
      })
    }).catch(err => {})
  }
  load()
</script>