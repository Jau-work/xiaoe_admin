<template>
  <div class="app-container">
    <!-- 课程信息表单 -->
    <el-form label-width="120px">

      <el-form-item label="课程标题">
        <el-input v-model="courseInfo.title" placeholder=" 示例：机器学习项目课：从基础到搭建项目视频课程。专业名称注意大小写"/>
      </el-form-item>

      <!-- 课程讲师 TODO -->
      <el-form-item label="课程讲师">
        <el-select v-model="courseInfo.teacherId">
          <el-option v-for="teacher in teacherList" :key="teacher.id" :label="teacher.name" :value="teacher.id"/>
        </el-select>
      </el-form-item>

      <!-- 所属分类：级联下拉列表 -->
      <el-form-item label="课程类别">
        <!-- 一级分类 -->
        <el-select
          v-model="courseInfo.subjectParentId"
          placeholder="请选择"
          @change="subjectChanged">
          <el-option
            v-for="subject in subjectList"
            :key="subject.id"
            :label="subject.title"
            :value="subject.id"/>
        </el-select>

        <!-- 二级分类 -->
        <el-select
          v-model="courseInfo.subjectId"
          placeholder="请选择">
          <el-option
            v-for="subject in subjectLevelTwoList"
            :key="subject.id"
            :label="subject.title"
            :value="subject.id"/>
        </el-select>
      </el-form-item>

      <el-form-item label="总课时">
        <el-input-number :min="0" v-model="courseInfo.lessonNum" controls-position="right" placeholder="请填写课程的总课时数"/>
      </el-form-item>

      <!-- 课程简介 TODO -->

      <!-- 课程封面 TODO -->

      <el-form-item label="课程价格">
        <el-input-number :min="0" v-model="courseInfo.price" controls-position="right" placeholder="免费课程请设置为0元"/> 元
      </el-form-item>
    </el-form>

    <div style="text-align:center">
      <el-button :disabled="saveBtnDisabled" type="primary" @click="saveAndNext">保存并下一步</el-button>
    </div>
  </div>
</template>

<script>
import courseApi from '@/api/course'
import teacherApi from '@/api/teacher'
import subjectApi from '@/api/subject'

export default {
  data() {
    return {
      saveBtnDisabled: false, // 按钮是否禁用
      courseInfo: { // 课程基本信息
        price: 0,
        lessonNum: 0,
        // 以下解决表单数据不全时insert语句非空校验
        teacherId: '',
        subjectId: '',
        subjectParentId: '',
        cover: '',
        description: ''
      },
      teacherList: [], // 讲师列表
      subjectList: [], // 一级分类
      subjectLevelTwoList: [] // 二级分类
    }
  },
  created() {
    this.initTeacherList()
    this.initSubjectList()
  },
  methods: {
    // 获取讲师列表
    initTeacherList() {
      teacherApi.list().then(res => {
        this.teacherList = res.data.items
      })
    },
    // 保存并下一步
    saveAndNext() {
      this.saveBtnDisabled = true
      this.saveData()
    },
    saveData() {
      courseApi.saveCourseInfo(this.courseInfo).then(res => {
        this.$message.success(res.message)
        this.$parent.courseId = res.data.courseId // 获取courseId
        this.$parent.active = 1 // 访问父组件的成员 $parent
      })
    },
    updateData() {

    },
    // 获取课程分类列表
    initSubjectList() {
      subjectApi.getNestedTreeList().then(res => {
        this.subjectList = res.data.items
      })
    },
    // 切换一级类别下拉列表
    subjectChanged(value) {
      // 遍历一级类别
      this.subjectList.forEach(subject => {
        // 判断当前选中的一级类别是否与当前遍历的一级类别id一致
        if (subject.id === value) {
          // 清空当前二级类别
          this.courseInfo.subjectId = ''
          // 如果一致, 则将当前遍历的一级类别的子类别绑定在页面的二级类别列表中
          this.subjectLevelTwoList = subject.children
        }
      })
    }
  }
}
</script>

<style>

</style>
