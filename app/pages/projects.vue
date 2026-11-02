
<script setup>
import { ref, onMounted, watch } from 'vue'

// localStorage 使用的資料名稱
const STORAGE_KEY = 'research-gantt-tasks'

// 預設研究任務
const defaultTasks = [
  {
    id: 1,
    name: '量化論文閱讀',
    category: 'Research / Paper Reading',
    startDate: '2026-11-02',
    endDate: '2026-11-20',
    progress: 65,
    color: 'brown'
  },
  {
    id: 2,
    name: '小模型量化實驗',
    category: 'AI / Experiment',
    startDate: '2026-11-15',
    endDate: '2026-12-15',
    progress: 35,
    color: 'green'
  },
  {
    id: 3,
    name: '實驗結果分析',
    category: 'Analysis / Evaluation',
    startDate: '2026-12-10',
    endDate: '2026-12-30',
    progress: 15,
    color: 'rose'
  }
]

const tasks = ref([...defaultTasks])

const showForm = ref(false)
const editingTaskId = ref(null)

// 確認是否已經完成 localStorage 載入
const isLoaded = ref(false)

const emptyTask = () => ({
  name: '',
  category: '',
  startDate: '',
  endDate: '',
  progress: 0,
  color: 'brown'
})

const newTask = ref(emptyTask())
const editTaskData = ref(emptyTask())

// 時間軸範圍
const timelineStart = new Date('2026-11-01T00:00:00')
const timelineEnd = new Date('2026-12-31T00:00:00')

const timelineDays =
  (timelineEnd - timelineStart) / 86400000 + 1

// ======================================
// localStorage：載入資料
// ======================================

onMounted(() => {
  try {
    const savedTasks = localStorage.getItem(STORAGE_KEY)

    if (savedTasks !== null) {
      const parsedTasks = JSON.parse(savedTasks)

      if (Array.isArray(parsedTasks)) {
        tasks.value = parsedTasks
      }
    }
  } catch (error) {
    console.error('讀取任務資料失敗：', error)
  }

  isLoaded.value = true
})

// ======================================
// localStorage：監聽資料並自動保存
// ======================================

watch(
  tasks,
  (newTasks) => {
    if (!isLoaded.value) return

    try {
      localStorage.setItem(
        STORAGE_KEY,
        JSON.stringify(newTasks)
      )
    } catch (error) {
      console.error('保存任務資料失敗：', error)
      alert('任務保存失敗，請檢查瀏覽器儲存空間。')
    }
  },
  { deep: true }
)

// ======================================
// 計算甘特圖任務位置
// ======================================

function getTaskStyle(task) {
  const start = new Date(`${task.startDate}T00:00:00`)
  const end = new Date(`${task.endDate}T00:00:00`)

  const offset = (start - timelineStart) / 86400000
  const duration = (end - start) / 86400000 + 1

  return {
    left: `${(offset / timelineDays) * 100}%`,
    width: `${(duration / timelineDays) * 100}%`
  }
}

// ======================================
// 新增任務
// ======================================

function addTask() {
  if (
    !newTask.value.name.trim() ||
    !newTask.value.category.trim() ||
    !newTask.value.startDate ||
    !newTask.value.endDate
  ) {
    alert('請填寫所有任務資料')
    return
  }

  if (newTask.value.endDate < newTask.value.startDate) {
    alert('結束日期不能早於開始日期')
    return
  }

  tasks.value.push({
    ...newTask.value,
    id: Date.now(),
    name: newTask.value.name.trim(),
    category: newTask.value.category.trim(),
    progress: Number(newTask.value.progress)
  })

  newTask.value = emptyTask()
  showForm.value = false
}

// ======================================
// 編輯任務
// ======================================

function startEdit(task) {
  editingTaskId.value = task.id
  editTaskData.value = { ...task }
}

// 儲存編輯
function saveEdit() {
  if (
    !editTaskData.value.name.trim() ||
    !editTaskData.value.category.trim() ||
    !editTaskData.value.startDate ||
    !editTaskData.value.endDate
  ) {
    alert('請填寫所有任務資料')
    return
  }

  if (editTaskData.value.endDate < editTaskData.value.startDate) {
    alert('結束日期不能早於開始日期')
    return
  }

  const index = tasks.value.findIndex(
    task => task.id === editingTaskId.value
  )

  if (index !== -1) {
    tasks.value[index] = {
      ...editTaskData.value,
      name: editTaskData.value.name.trim(),
      category: editTaskData.value.category.trim(),
      progress: Number(editTaskData.value.progress)
    }
  }

  cancelEdit()
}

// 取消編輯
function cancelEdit() {
  editingTaskId.value = null
  editTaskData.value = emptyTask()
}

// ======================================
// 刪除任務
// ======================================

function deleteTask(task) {
  const confirmed = window.confirm(
    `確定要刪除「${task.name}」這筆任務嗎？`
  )

  if (!confirmed) return

  tasks.value = tasks.value.filter(
    item => item.id !== task.id
  )

  if (editingTaskId.value === task.id) {
    cancelEdit()
  }
}
</script>

<template>
  <main class="projects-page">
    <section>
      <p class="eyebrow">MY WORK & RESEARCH</p>
      <h1>作品與研究</h1>

      <p class="introduction">
        記錄我的學習歷程、研究探索與實作成果，
        在每一次嘗試中累積經驗。
      </p>

      <div class="project-list">
        <article class="project-card">
          <h2>01. Neural Network Quantization</h2>
          <p>神經網路量化</p>
          <p>
            探索模型量化技術，理解低位元表示、
            量化方法與模型效能之間的關係，
            並透過實驗學習量化技術的實際應用。
          </p>
          <p>Category：AI / Model Compression</p>
          <p>Status：Learning & Research</p>
        </article>

        <article class="project-card">
          <h2>02. Time Series Forecasting</h2>
          <p>多模態時間序列預測</p>
          <p>
            閱讀多模態時間序列預測相關研究，
            探索不同資料模態如何結合，
            並了解基礎模型在時間序列任務中的應用。
          </p>
          <p>Category：AI / Time Series</p>
          <p>Status：Paper Reading</p>
        </article>

        <article class="project-card">
          <h2>03. Personal Website</h2>
          <p>個人網站開發</p>
          <p>
            使用 Vue 與 Nuxt 4 建立個人網站，
            練習頁面路由、共用版型、
            元件管理與靜態網站生成。
          </p>
          <p>Category：Web Development</p>
          <p>Status：In Progress</p>
        </article>
      </div>
    </section>

    <section class="timeline-section">
      <p class="eyebrow">PLAN & PROGRESS</p>
      <h2 class="timeline-title">Research Timeline</h2>
      <p class="timeline-description">
        我的研究與學習進度規劃
      </p>

      <div class="timeline-card">
        <div class="timeline-heading">
          <div>
            <h3>2026 Research Plan</h3>
            <span>November — December</span>
          </div>

          <button
            class="add-button"
            @click="showForm = !showForm"
          >
            {{ showForm ? '取消' : '+ 新增任務' }}
          </button>
        </div>

        <!-- 新增任務表單 -->
        <form
          v-if="showForm"
          class="task-form"
          @submit.prevent="addTask"
        >
          <h3>新增研究任務</h3>

          <label>
            任務名稱
            <input
              v-model="newTask.name"
              placeholder="例如：閱讀量化論文"
              required
            />
          </label>

          <label>
            任務分類
            <input
              v-model="newTask.category"
              placeholder="例如：AI / Research"
              required
            />
          </label>

          <div class="form-dates">
            <label>
              開始日期
              <input
                v-model="newTask.startDate"
                type="date"
                required
              />
            </label>

            <label>
              結束日期
              <input
                v-model="newTask.endDate"
                type="date"
                :min="newTask.startDate"
                required
              />
            </label>
          </div>

          <label>
            完成進度：{{ newTask.progress }}%
            <input
              v-model.number="newTask.progress"
              type="range"
              min="0"
              max="100"
            />
          </label>

          <label>
            任務顏色
            <select v-model="newTask.color">
              <option value="brown">暖棕色</option>
              <option value="green">鼠尾草綠</option>
              <option value="rose">奶茶粉</option>
            </select>
          </label>

          <button class="submit-button" type="submit">
            確認新增
          </button>
        </form>

        <!-- 時間軸月份 -->
        <div class="month-row">
          <span>任務</span>
          <span>11月</span>
          <span>12月</span>
        </div>

        <!-- 任務列表 -->
        <div
          v-for="task in tasks"
          :key="task.id"
          class="task-item"
        >
          <div class="task-row">
            <div class="task-info">
              <strong>{{ task.name }}</strong>
              <small>{{ task.category }}</small>
            </div>

            <div class="task-track">
              <div
                class="task-bar"
                :class="`bar-${task.color}`"
                :style="getTaskStyle(task)"
              ></div>
            </div>

            <span class="progress-label">
              {{ task.progress }}%
            </span>
          </div>

          <!-- 編輯與刪除按鈕 -->
          <div class="task-actions">
            <button
              class="edit-button"
              @click="startEdit(task)"
            >
              編輯任務
            </button>

            <button
              class="delete-button"
              @click="deleteTask(task)"
            >
              刪除任務
            </button>
          </div>

          <!-- 編輯任務表單 -->
          <form
            v-if="editingTaskId === task.id"
            class="task-form edit-form"
            @submit.prevent="saveEdit"
          >
            <h3>編輯研究任務</h3>

            <label>
              任務名稱
              <input
                v-model="editTaskData.name"
                required
              />
            </label>

            <label>
              任務分類
              <input
                v-model="editTaskData.category"
                required
              />
            </label>

            <div class="form-dates">
              <label>
                開始日期
                <input
                  v-model="editTaskData.startDate"
                  type="date"
                  required
                />
              </label>

              <label>
                結束日期
                <input
                  v-model="editTaskData.endDate"
                  type="date"
                  :min="editTaskData.startDate"
                  required
                />
              </label>
            </div>

            <label>
              完成進度：{{ editTaskData.progress }}%
              <input
                v-model.number="editTaskData.progress"
                type="range"
                min="0"
                max="100"
              />
            </label>

            <label>
              任務顏色
              <select v-model="editTaskData.color">
                <option value="brown">暖棕色</option>
                <option value="green">鼠尾草綠</option>
                <option value="rose">奶茶粉</option>
              </select>
            </label>

            <div class="edit-actions">
              <button class="submit-button" type="submit">
                儲存修改
              </button>

              <button
                class="cancel-button"
                type="button"
                @click="cancelEdit"
              >
                取消
              </button>
            </div>
          </form>
        </div>

        <div class="timeline-legend">
          <span><i class="dot brown"></i>暖棕色</span>
          <span><i class="dot green"></i>鼠尾草綠</span>
          <span><i class="dot rose"></i>奶茶粉</span>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped>
.projects-page {
  color: #51483f;
}

.eyebrow {
  color: #a38d74;
  font-size: 11px;
  letter-spacing: 2px;
}

h1,
.timeline-title {
  font-family: Georgia, serif;
  font-weight: 400;
}

h1 {
  font-size: 42px;
}

.introduction,
.timeline-description {
  color: #82776b;
  line-height: 1.9;
}

.project-list {
  display: grid;
  gap: 22px;
  margin-top: 40px;
}

.project-card {
  padding: 26px;
  border: 1px solid #e9e3d9;
  border-radius: 12px;
  background: #fffdf9;
  line-height: 1.8;
}

.project-card h2 {
  font-family: Georgia, serif;
  font-size: 21px;
  font-weight: 400;
}

.project-card p {
  color: #82776b;
}

.timeline-section {
  margin-top: 85px;
}

.timeline-title {
  font-size: 34px;
  margin-bottom: 8px;
}

.timeline-card {
  margin-top: 30px;
  padding: 30px;
  background: #fffdf9;
  border: 1px solid #e9e3d9;
  border-radius: 14px;
  overflow-x: auto;
}

.timeline-heading {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  margin-bottom: 35px;
}

.timeline-heading h3 {
  font-family: Georgia, serif;
  font-size: 20px;
  font-weight: 400;
}

.timeline-heading span,
.month-row {
  color: #a39787;
  font-size: 12px;
}

.add-button,
.submit-button {
  border: 0;
  border-radius: 7px;
  padding: 11px 18px;
  background: #a88b6a;
  color: white;
  cursor: pointer;
}

.task-form {
  display: grid;
  gap: 16px;
  margin-bottom: 30px;
  padding: 24px;
  border: 1px solid #e9e3d9;
  border-radius: 10px;
  background: #faf8f3;
}

.task-form h3 {
  font-family: Georgia, serif;
  font-weight: 400;
  font-size: 20px;
}

.task-form label {
  display: grid;
  gap: 8px;
  color: #82776b;
  font-size: 13px;
}

.task-form input:not([type="range"]),
.task-form select {
  width: 100%;
  box-sizing: border-box;
  padding: 11px;
  border: 1px solid #e9e3d9;
  border-radius: 6px;
  background: #fffdf9;
  color: #51483f;
}

.form-dates {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 14px;
}

.month-row,
.task-row {
  display: grid;
  grid-template-columns: 180px minmax(180px, 1fr) 45px;
  align-items: center;
  gap: 15px;
}

.month-row {
  grid-template-columns: 180px 1fr 1fr;
  padding-bottom: 15px;
  border-bottom: 1px solid #e9e3d9;
}

.task-item {
  border-bottom: 1px solid #f0ece5;
}

.task-row {
  min-height: 75px;
}

.task-info {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.task-info strong {
  font-size: 13px;
  font-weight: 500;
}

.task-info small {
  color: #a39787;
  font-size: 10px;
}

.task-track {
  position: relative;
  height: 12px;
  border-radius: 20px;
  background: #f0ece5;
  overflow: hidden;
}

.task-bar {
  position: absolute;
  top: 0;
  height: 100%;
  border-radius: 20px;
}

.bar-brown {
  background: #a88b6a;
}

.bar-green {
  background: #8e9a83;
}

.bar-rose {
  background: #c5a4a0;
}

.progress-label {
  color: #82776b;
  font-size: 12px;
  text-align: right;
}

.task-actions {
  display: flex;
  justify-content: flex-end;
  flex-wrap: wrap;
  gap: 10px;
  padding: 0 0 12px;
}

.edit-button,
.cancel-button {
  padding: 8px 14px;
  border: 1px solid #e9e3d9;
  border-radius: 6px;
  background: #fffdf9;
  color: #82776b;
  cursor: pointer;
}

.delete-button {
  padding: 8px 14px;
  border: 1px solid #ead8d5;
  border-radius: 6px;
  background: #fff9f8;
  color: #b47d76;
  cursor: pointer;
}

.edit-form {
  margin-top: 10px;
}

.edit-actions {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.timeline-legend {
  display: flex;
  flex-wrap: wrap;
  gap: 22px;
  margin-top: 28px;
  color: #82776b;
  font-size: 11px;
}

.timeline-legend span {
  display: flex;
  align-items: center;
  gap: 7px;
}

.dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
}

.dot.brown {
  background: #a88b6a;
}

.dot.green {
  background: #8e9a83;
}

.dot.rose {
  background: #c5a4a0;
}

@media (max-width: 600px) {
  h1 {
    font-size: 34px;
  }

  .timeline-card {
    padding: 20px;
  }

  .month-row,
  .task-row {
    grid-template-columns: 125px minmax(150px, 1fr) 35px;
    gap: 10px;
  }

  .month-row {
    grid-template-columns: 125px 1fr 1fr;
  }

  .timeline-heading {
    align-items: flex-start;
    flex-direction: column;
  }

  .form-dates {
    grid-template-columns: 1fr;
  }
}
</style>
