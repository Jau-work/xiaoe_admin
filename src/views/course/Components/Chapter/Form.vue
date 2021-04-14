<template>
  <!-- 添加和修改章节表单 -->
  <el-dialog :visible="dialogVisible" :title="title" @close="close()">
    <el-form :model="chapter" label-width="120px">
      <el-form-item label="章节标题">
        <el-input v-model="chapter.title"/>
      </el-form-item>
      <el-form-item label="章节排序">
        <el-input-number v-model="chapter.sort" :min="0"/>
      </el-form-item>
    </el-form>
    <div slot="footer" class="dialog-footer">
      <el-button @click="close()">取 消</el-button>
      <el-button type="primary" @click="saveOrUpdate">确 定</el-button>
    </div>
  </el-dialog>
</template>

<script>
import chapterApi from '@/api/chapter'

export default{
  data() {
    return {
      title: '',
      dialogVisible: false, // 是否显示对话框
      chapter: { // 章节对象
        sort: 0
      }
    }
  },
  methods: {
    // 打开对话框
    open(chapterId) {
      this.dialogVisible = true
      if (chapterId) {
        this.title = '编辑章节'
        chapterApi.getById(chapterId).then(response => {
          this.chapter = response.data.item
        })
      } else {
        this.title = '添加章节'
      }
    },
    // 关闭对话框
    close() {
      this.dialogVisible = false
      this.resetForm()
    },
    // 重置表单
    resetForm() {
      this.chapter = {
        sort: 0
      }
    },
    // 保存或更新
    saveOrUpdate() {
      if (!this.chapter.id) {
        this.save()
      } else {
        this.update()
      }
    },
    // 保存章节
    save() {
      this.chapter.courseId = this.$parent.$parent.courseId
      chapterApi.save(this.chapter).then(response => {
        this.$message.success(response.message)
        this.close()
        this.$parent.fetchNodeList()
      })
    },
    // 更新章节
    update() {
      chapterApi.updateById(this.chapter).then(response => {
        this.$message.success(response.message)
        this.close()
        this.$parent.fetchNodeList()
      })
    }
  }
}
</script>
