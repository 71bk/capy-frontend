<script setup>
import { ref, computed, onMounted } from "vue";
import GradientLineChart from "@/components/admin/GradientLineChart.vue";
import UserGrowthChart from "@/components/admin/UserGrowthChart.vue";
import { getDashboard, getWeeklyUserTrend } from "@/api/admin/dashboard";

const progressBarColor = ["#409eff", "#67c23a", "#e6a23c", "#f56c6c", "#909399"];

// Dashboard data
const dashboardData = ref({
  todayNewUsers: 0,
  pendingCourseReview: 0,
  pendingInstructorApplications: 0,
  currentMonthRevenue: 0,
  currentMonthNewUsers: 0,
  popularCourses: [],
});

// Weekly user trend data
const weeklyUserTrend = ref([]);

// Loading states
const loading = ref(false);

const datalist = computed(() => {
  const courses = dashboardData.value.popularCourses || [];
  const total = courses.reduce((sum, item) => sum + item.enrollmentCount, 0) || 1;
  return courses.map((item, index) => ({
    id: item.courseId,
    name: item.courseTitle,
    num: item.enrollmentCount,
    value: Math.ceil((item.enrollmentCount / total) * 100),
    color: progressBarColor[index] || "#ccc",
  }));
});

const formatCurrency = (value) => {
  return `NT$${Number(value || 0).toLocaleString()}`;
};

const getTimestamps = () => {
  const now = new Date();

  // Today start (00:00:00)
  const todayStart = new Date(now.getFullYear(), now.getMonth(), now.getDate());
  const todayStartEpochMillis = todayStart.getTime();

  // Month start (first day of current month 00:00:00)
  const monthStart = new Date(now.getFullYear(), now.getMonth(), 1);
  const monthStartEpochMillis = monthStart.getTime();

  return { todayStartEpochMillis, monthStartEpochMillis };
};

const fetchDashboard = async () => {
  try {
    loading.value = true;
    const { todayStartEpochMillis, monthStartEpochMillis } = getTimestamps();
    const data = await getDashboard(todayStartEpochMillis, monthStartEpochMillis);
    if (data) {
      dashboardData.value = data;
    }
  } catch (error) {
    console.error("Failed to fetch dashboard:", error);
  } finally {
    loading.value = false;
  }
};

const fetchWeeklyUserTrend = async () => {
  try {
    const { monthStartEpochMillis } = getTimestamps();
    const data = await getWeeklyUserTrend(monthStartEpochMillis);
    if (data) {
      weeklyUserTrend.value = data;
    }
  } catch (error) {
    console.error("Failed to fetch weekly user trend:", error);
  }
};

onMounted(() => {
  fetchDashboard();
  fetchWeeklyUserTrend();
});
</script>

<template>
  <h2 class="section-heading">管理員工作台</h2>
  <div v-loading="loading" style="padding-top: 24px; display: flex; flex-direction: column; gap: 36px">
    <div>
      <h3 class="section-title">課程資訊</h3>
      <el-row :gutter="20">
        <el-col :span="6">
          <div class="data-wrapper">
            <span>今日新註冊用戶</span>
            <span class="data">{{ dashboardData.todayNewUsers }}</span>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="data-wrapper">
            待審核上架申請
            <span class="data" :class="{ undone: dashboardData.pendingCourseReview > 0 }">
              {{ dashboardData.pendingCourseReview }}
            </span>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="data-wrapper">
            待審核教師申請
            <span class="data" :class="{ undone: dashboardData.pendingInstructorApplications > 0 }">
              {{ dashboardData.pendingInstructorApplications }}
            </span>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="data-wrapper">
            本月收益
            <span class="data">
              <el-text class="data" truncated>{{ formatCurrency(dashboardData.currentMonthRevenue) }}</el-text>
            </span>
          </div>
        </el-col>
      </el-row>
    </div>

    <div>
      <h3 class="section-title">平台數據一覽</h3>
      <el-row :gutter="20">
        <el-col :span="14">
          <div class="wrapper">
            <p style="font-size: 20px; color: rgb(84, 80, 80); display: flex">
              本月學生人數成長
              <span style="margin-left: auto; font-weight: 700">{{ dashboardData.currentMonthNewUsers }}人</span>
            </p>
            <div class="chart-container">
              <UserGrowthChart :data="weeklyUserTrend" />
            </div>
          </div>
        </el-col>
        <el-col :span="10">
          <div class="wrapper">
            <p style="font-size: 20px; color: rgb(84, 80, 80)">熱門課程Top5</p>

            <ul class="top-course-list">
              <template v-if="datalist.length">
                <li v-for="item in datalist" :key="item.id">
                  <div class="top-course-label">
                    {{ item.name }}
                    <el-button link type="primary">查看</el-button>
                  </div>
                  <p style="font-size: 14px; font-weight: 500; margin-bottom: 4px">{{ item.num }}人</p>
                  <el-progress :color="item.color" :show-text="false" :percentage="item.value" />
                </li>
              </template>
              <el-empty v-else description="暫無數據" />
            </ul>
          </div>
        </el-col>
      </el-row>
    </div>
    <div>
      <div class="wrapper">
        <GradientLineChart />
      </div>
    </div>
  </div>
</template>

<style scoped>
.data-wrapper {
  height: fit-content;
  font-size: 16px;
  color: rgb(84, 80, 80);
  background-color: #fcfcfd;
  box-shadow: 0 0 8px #0000001f;
  padding: 24px;
  border-radius: 16px;
}
.data {
  margin-top: 24px;
  font-weight: 700;
  font-size: 24px;
  display: block;
}
.undone {
  position: relative;
}
.undone::after {
  content: "";
  margin-left: 4px;
  position: absolute;

  top: -12%;
  border-radius: 50%;
  width: 8px;
  height: 8px;
  background-color: red;
}
.chart-container {
  height: 500px;
  width: 100%;
}
.top-course-list {
  padding-top: 36px;
  display: flex;
  flex-direction: column;
  gap: 24px;
}
.top-course-label {
  padding-right: 2%;
  margin-bottom: 8px;
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
}
</style>
