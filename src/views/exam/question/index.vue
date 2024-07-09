<template>
  <div class="app-container">
    <el-form
      :model="queryParams"
      ref="queryRef"
      :inline="true"
      v-show="showSearch"
      label-width="68px"
    >
      <el-form-item label="题目ID" prop="id">
        <el-input
          v-model="queryParams.id"
          placeholder="请输入题目ID"
          clearable
          @keyup.enter="handleQuery"
        />
      </el-form-item>
      <el-form-item label="题型" prop="questionType">
        <el-select
          v-model="queryParams.questionType"
          placeholder="请选择题型"
          clearable
        >
          <el-option
            v-for="dict in question_type"
            :key="dict.value"
            :label="dict.label"
            :value="dict.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="学科" prop="subjectId">
        <el-select
          v-model="queryParams.subjectId"
          placeholder="请选择学科"
          clearable
        >
          <el-option
            v-for="dict in exam_subject"
            :key="dict.value"
            :label="dict.label"
            :value="dict.value"
          />
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" icon="Search" @click="handleQuery"
          >搜索</el-button
        >
        <el-button icon="Refresh" @click="resetQuery">重置</el-button>
      </el-form-item>
    </el-form>

    <el-row :gutter="10" class="mb8">
      <el-col :span="1.5">
        <el-button
          type="primary"
          plain
          icon="Plus"
          @click="handleAdd"
          v-hasPermi="['exam:question:add']"
          >新增</el-button
        >
      </el-col>
      <el-col :span="1.5">
        <el-button
          type="success"
          plain
          icon="Edit"
          :disabled="single"
          @click="handleUpdate"
          v-hasPermi="['exam:question:edit']"
          >修改</el-button
        >
      </el-col>
      <el-col :span="1.5">
        <el-button
          type="danger"
          plain
          icon="Delete"
          :disabled="multiple"
          @click="handleDelete"
          v-hasPermi="['exam:question:remove']"
          >删除</el-button
        >
      </el-col>
      <el-col :span="1.5">
        <el-button
          type="warning"
          plain
          icon="Download"
          @click="handleExport"
          v-hasPermi="['exam:question:export']"
          >导出</el-button
        >
      </el-col>
      <right-toolbar
        v-model:showSearch="showSearch"
        @queryTable="getList"
      ></right-toolbar>
    </el-row>

    <el-table
      v-loading="loading"
      :data="questionList"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="55" align="center" />
      <el-table-column label="题目ID" align="center" prop="id" />
      <el-table-column label="题型" align="center" prop="questionType">
        <template #default="scope">
          <dict-tag :options="question_type" :value="scope.row.questionType" />
        </template>
      </el-table-column>
      <el-table-column label="学科" align="center" prop="subjectId">
        <template #default="scope">
          <dict-tag :options="exam_subject" :value="scope.row.subjectId" />
        </template>
      </el-table-column>
      <el-table-column label="分值" align="center" prop="score" />
      <el-table-column label="题干" align="center" prop="content" />
      <el-table-column label="选项" align="center" prop="options" />
      <el-table-column label="图片" align="center" prop="image" width="100">
        <template #default="scope">
          <image-preview :src="scope.row.image" :width="50" :height="50" />
        </template>
      </el-table-column>
      <el-table-column
        label="操作"
        align="center"
        class-name="small-padding fixed-width"
      >
        <template #default="scope">
          <el-button
            link
            type="primary"
            icon="Edit"
            @click="handleUpdate(scope.row)"
            v-hasPermi="['exam:question:edit']"
            >修改</el-button
          >
          <el-button
            link
            type="primary"
            icon="Delete"
            @click="handleDelete(scope.row)"
            v-hasPermi="['exam:question:remove']"
            >删除</el-button
          >
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="total > 0"
      :total="total"
      v-model:page="queryParams.pageNum"
      v-model:limit="queryParams.pageSize"
      @pagination="getList"
    />

    <!-- 添加或修改试题列表对话框 -->
    <el-dialog :title="title" v-model="open" width="500px" append-to-body>
      <el-form
        ref="questionRef"
        :model="form"
        :rules="rules"
        label-width="80px"
      >
        <el-form-item label="题型" prop="questionType">
          <el-select v-model="form.questionType" placeholder="请选择题型">
            <el-option
              v-for="dict in question_type"
              :key="dict.value"
              :label="dict.label"
              :value="parseInt(dict.value)"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="学科" prop="subjectId">
          <el-select v-model="form.subjectId" placeholder="请选择学科">
            <el-option
              v-for="dict in exam_subject"
              :key="dict.value"
              :label="dict.label"
              :value="parseInt(dict.value)"
            ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="分值" prop="score">
          <el-input v-model="form.score" placeholder="请输入分值" />
        </el-form-item>
        <el-form-item label="答案" prop="correct">
          <el-input
            v-model="form.correct"
            type="textarea"
            placeholder="请输入内容"
          />
        </el-form-item>
        <el-form-item label="题干" prop="content">
          <el-input
            v-model="form.content"
            type="textarea"
            placeholder="请输入内容"
          />
        </el-form-item>
        <el-form-item label="选项" prop="options">
          <el-input
            v-model="form.options"
            type="textarea"
            placeholder="请输入内容"
          />
        </el-form-item>
        <el-form-item label="图片" prop="image">
          <!-- <oss-image-upload v-model="form.image" :limit="1" /> -->
          <image-upload v-model="form.image" />
        </el-form-item>
      </el-form>
      <template #footer>
        <div class="dialog-footer">
          <el-button type="primary" @click="submitForm">确 定</el-button>
          <el-button @click="cancel">取 消</el-button>
        </div>
      </template>
    </el-dialog>
  </div>
</template>

<script setup name="Question">
import {
  listQuestion,
  getQuestion,
  delQuestion,
  addQuestion,
  updateQuestion,
} from "@/api/exam/question";

const { proxy } = getCurrentInstance();
const { question_type, exam_subject } = proxy.useDict(
  "question_type",
  "exam_subject"
);

const questionList = ref([]);
const open = ref(false);
const loading = ref(true);
const showSearch = ref(true);
const ids = ref([]);
const single = ref(true);
const multiple = ref(true);
const total = ref(0);
const title = ref("");

const data = reactive({
  form: {},
  queryParams: {
    pageNum: 1,
    pageSize: 10,
    id: null,
    questionType: null,
    subjectId: null,
    content: null,
  },
  rules: {},
});

const { queryParams, form, rules } = toRefs(data);

/** 查询试题列表列表 */
function getList() {
  loading.value = true;
  listQuestion(queryParams.value).then((response) => {
    questionList.value = response.rows;
    total.value = response.total;
    loading.value = false;
  });
}

// 取消按钮
function cancel() {
  open.value = false;
  reset();
}

// 表单重置
function reset() {
  form.value = {
    id: null,
    questionType: null,
    subjectId: null,
    score: null,
    correct: null,
    content: null,
    options: null,
    image: null,
  };
  proxy.resetForm("questionRef");
}

/** 搜索按钮操作 */
function handleQuery() {
  queryParams.value.pageNum = 1;
  getList();
}

/** 重置按钮操作 */
function resetQuery() {
  proxy.resetForm("queryRef");
  handleQuery();
}

// 多选框选中数据
function handleSelectionChange(selection) {
  ids.value = selection.map((item) => item.id);
  single.value = selection.length != 1;
  multiple.value = !selection.length;
}

/** 新增按钮操作 */
function handleAdd() {
  reset();
  open.value = true;
  title.value = "添加试题列表";
}

/** 修改按钮操作 */
function handleUpdate(row) {
  reset();
  const _id = row.id || ids.value;
  getQuestion(_id).then((response) => {
    form.value = response.data;
    open.value = true;
    title.value = "修改试题列表";
  });
}

/** 提交按钮 */
function submitForm() {
  proxy.$refs["questionRef"].validate((valid) => {
    if (valid) {
      if (form.value.id != null) {
        updateQuestion(form.value).then((response) => {
          proxy.$modal.msgSuccess("修改成功");
          open.value = false;
          getList();
        });
      } else {
        addQuestion(form.value).then((response) => {
          proxy.$modal.msgSuccess("新增成功");
          open.value = false;
          getList();
        });
      }
    }
  });
}

/** 删除按钮操作 */
function handleDelete(row) {
  const _ids = row.id || ids.value;
  proxy.$modal
    .confirm('是否确认删除试题列表编号为"' + _ids + '"的数据项？')
    .then(function () {
      return delQuestion(_ids);
    })
    .then(() => {
      getList();
      proxy.$modal.msgSuccess("删除成功");
    })
    .catch(() => {});
}

/** 导出按钮操作 */
function handleExport() {
  proxy.download(
    "exam/question/export",
    {
      ...queryParams.value,
    },
    `question_${new Date().getTime()}.xlsx`
  );
}

getList();
</script>
