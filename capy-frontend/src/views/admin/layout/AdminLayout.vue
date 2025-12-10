<script setup>
import { useRoute } from "vue-router";
const route = useRoute();
const scrollbarRef = ref(null); // 指向 el-scrollbar
watch(
  () => route.fullPath,
  () => {
    const wrap = scrollbarRef.value?.wrapRef;
    if (wrap) wrap.scrollTo({ top: 0, behavior: "smooth" });
  }
);
const isCollapse = ref(true);

const userProfile = {
  name: "Admin User",
  avatar: "https://picsum.photos/seed/capy-admin/80",
};

const handleUserCommand = (command) => {
  if (command === "logout") {
    ElMessage.info("已登出（待串接 API）");
    return;
  }
  if (command === "switch-teacher") {
    ElMessage.info("切換至講師端（待串接路由）");
    return;
  }
  if (command === "switch-student") {
    ElMessage.info("切換至學生端（待串接路由）");
  }
};
</script>
<template>
  <div class="common-layout">
    <el-container>
      <el-aside :style="{ width: isCollapse ? '220px' : '72px' }">
        <!-- Logo Header (only show when expanded) -->
        <div v-if="isCollapse" class="sidebar-header">
          <div class="logo-area">
            <span class="logo-text">CapyCourse</span>
          </div>
        </div>

        <!-- Menu -->
        <el-scrollbar class="sidebar-menu-scrollbar">
          <el-menu :collapse="!isCollapse" class="admin-sidebar-menu">
            <router-link :to="{ name: 'adminWorkspace' }">
              <el-menu-item index="adminWorkspace">
                <el-icon><House /></el-icon>
                <template #title>工作台</template>
              </el-menu-item>
            </router-link>
            <el-sub-menu index="course">
              <template #title>
                <el-icon><Edit /></el-icon>
                <span>課程管理</span>
              </template>
              <RouterLink :to="{ name: 'course_application_list' }">
                <el-menu-item index="course_application_list">
                  <template #title>上架申請列表</template>
                </el-menu-item>
              </RouterLink>
              <RouterLink :to="{ name: 'courseManagement' }">
                <el-menu-item index="courseManagement">
                  <template #title>課程狀態管理</template>
                </el-menu-item>
              </RouterLink>
            </el-sub-menu>
            <el-sub-menu index="user">
              <template #title>
                <el-icon><UserFilled /></el-icon>
                <span>用戶管理</span>
              </template>
              <RouterLink :to="{ name: 'instructor_application_list' }">
                <el-menu-item index="instructor_application_list">
                  <template #title>教師申請列表</template>
                </el-menu-item>
              </RouterLink>
              <RouterLink :to="{ name: 'userManagement' }">
                <el-menu-item index="userManagement">
                  <template #title>用戶狀態管理</template>
                </el-menu-item>
              </RouterLink>
            </el-sub-menu>
            <el-sub-menu index="platform">
              <template #title>
                <el-icon><Management /></el-icon>
                <span>平台管理</span>
              </template>
              <RouterLink :to="{ name: 'platform_announcement' }">
                <el-menu-item index="platform_announcement">
                  <template #title>平台公告</template>
                </el-menu-item>
              </RouterLink>
              <RouterLink :to="{ name: 'category_management' }">
                <el-menu-item index="category_management">
                  <template #title>分類管理</template>
                </el-menu-item>
              </RouterLink>
            </el-sub-menu>
            <RouterLink :to="{ name: 'datastatic' }">
              <el-menu-item index="datastatic">
                <el-icon><DataAnalysis /></el-icon>
                <template #title>數據分析</template>
              </el-menu-item>
            </RouterLink>
            <RouterLink :to="{ name: 'operationrecord' }">
              <el-menu-item index="operationrecord">
                <el-icon><SetUp /></el-icon>
                <template #title>操作紀錄查詢</template>
              </el-menu-item>
            </RouterLink>
          </el-menu>
        </el-scrollbar>

        <!-- Toggle Button -->
        <div class="sidebar-footer">
          <button class="toggle-btn" @click="isCollapse = !isCollapse" :title="isCollapse ? '收合側邊欄' : '展開側邊欄'">
            <el-icon :size="18">
              <component :is="isCollapse ? 'DArrowLeft' : 'DArrowRight'" />
            </el-icon>
          </button>
        </div>
      </el-aside>
      <el-container
        style="transition: margin 0.3s ease"
        :style="{ 'margin-left': isCollapse ? '220px' : '72px' }"
      >
        <el-scrollbar ref="scrollbarRef" style="height: 100vh; width: 100%">
          <el-header>
            <div class="header-actions">
              <el-dropdown trigger="hover" @command="handleUserCommand">
                <span class="user-chip">
                  <el-avatar :size="40" :src="userProfile.avatar" />
                  <span class="user-name">{{ userProfile.name }}</span>
                  <el-icon class="arrow"><ArrowDown /></el-icon>
                </span>
                <template #dropdown>
                  <el-dropdown-menu style="width: 200px">
                    <el-dropdown-item command="switch-teacher">切換至講師端</el-dropdown-item>
                    <el-dropdown-item command="switch-student">切換至學生端</el-dropdown-item>
                    <el-dropdown-item divided command="logout"
                      ><span
                        ><el-icon size="large" style="vertical-align: middle"
                          ><SwitchButton /></el-icon
                        >退出登入</span
                      ></el-dropdown-item
                    >
                  </el-dropdown-menu>
                </template>
              </el-dropdown>
            </div>
          </el-header>
          <el-main>
            <div class="main-container">
              <RouterView></RouterView>
            </div>
          </el-main>
          <el-footer>Copyright © 2025 CapyCourse. All rights reserved.</el-footer>
        </el-scrollbar>
      </el-container>
    </el-container>
  </div>
</template>
<style scoped>
/* ==================== Sidebar Container ==================== */
.el-aside {
  overflow: hidden;
  transition: width 0.3s ease;
  height: 100vh;
  position: fixed;
  background: linear-gradient(180deg, #1E1B4B 0%, #312E81 50%, #1E1B4B 100%);
  box-shadow: 4px 0 20px rgba(0, 0, 0, 0.15);
  display: flex;
  flex-direction: column;
}

/* ==================== Logo Header ==================== */
.sidebar-header {
  padding: 20px 16px;
  border-bottom: 1px solid rgba(99, 102, 241, 0.2);
}

.logo-area {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 32px;
}

.logo-text {
  font-size: 20px;
  font-weight: 700;
  color: #FFFFFF;
  letter-spacing: 1px;
  background: linear-gradient(135deg, #E0E7FF 0%, #A5B4FC 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.logo-text-short {
  font-size: 22px;
  font-weight: 800;
  background: linear-gradient(135deg, #E0E7FF 0%, #A5B4FC 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

/* Fade transition */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* ==================== Menu Scrollbar ==================== */
.sidebar-menu-scrollbar {
  flex: 1;
  overflow: hidden;
}

/* ==================== Menu Items ==================== */
:deep(.el-menu) {
  border-right: 0;
  background-color: transparent;
  padding: 12px 0;
  width: 100% !important;
}

:deep(.el-menu--collapse) {
  width: 72px !important;
}

:deep(.el-menu-item) {
  color: rgba(224, 231, 255, 0.85);
  border-radius: 10px;
  margin: 4px 12px;
  height: 48px;
  line-height: 48px;
  transition: all 0.2s ease;
}

/* Fix collapsed menu icon centering - unify padding for both trigger types */
:deep(.el-menu--collapse .el-menu-tooltip__trigger) {
  padding: 0 !important;
  display: flex !important;
  justify-content: center !important;
  align-items: center !important;
}

:deep(.el-menu--collapse .el-sub-menu__title) {
  padding: 0 !important;
  display: flex !important;
  justify-content: center !important;
  align-items: center !important;
}

:deep(.el-menu--collapse .el-menu-item .el-icon),
:deep(.el-menu--collapse .el-sub-menu__title .el-icon) {
  margin: 0 !important;
}

:deep(.el-menu-item:hover) {
  background-color: rgba(99, 102, 241, 0.25);
  color: #FFFFFF;
}

:deep(.el-menu-item .el-icon) {
  font-size: 20px;
}

:deep(.el-sub-menu__title) {
  color: rgba(224, 231, 255, 0.85);
  border-radius: 10px;
  margin: 4px 12px;
  height: 48px;
  line-height: 48px;
  transition: all 0.2s ease;
}

:deep(.el-menu--collapse .el-sub-menu__title) {
  margin: 4px auto !important;
  padding: 0 !important;
  width: 52px !important;
  display: flex !important;
  justify-content: center !important;
  align-items: center !important;
}

:deep(.el-menu--collapse .el-sub-menu__title .el-icon) {
  margin: 0 !important;
}

:deep(.el-menu--collapse .el-sub-menu__title .el-sub-menu__icon-arrow) {
  display: none !important;
}

:deep(.el-sub-menu__title:hover) {
  background-color: rgba(99, 102, 241, 0.25);
  color: #FFFFFF;
}

:deep(.el-sub-menu__title .el-icon) {
  font-size: 20px;
}

:deep(.el-sub-menu:has(.router-link-active) > .el-sub-menu__title) {
  color: #A5B4FC;
}

.router-link-active {
  color: #A5B4FC;
}

.router-link-active .el-menu-item {
  background: linear-gradient(135deg, rgba(99, 102, 241, 0.3) 0%, rgba(129, 140, 248, 0.2) 100%);
  color: #FFFFFF;
  box-shadow: 0 2px 8px rgba(99, 102, 241, 0.3);
}

.el-menu--popup li {
  color: #374151;
}

.el-menu--popup .router-link-active .el-menu-item {
  color: #4F46E5;
  background-color: #EEF2FF;
}

/* ==================== Sidebar Footer ==================== */
.sidebar-footer {
  padding: 16px;
  border-top: 1px solid rgba(99, 102, 241, 0.2);
  display: flex;
  justify-content: center;
}

.toggle-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: none;
  background: linear-gradient(135deg, #6366F1 0%, #4F46E5 100%);
  color: #FFFFFF;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s ease;
  box-shadow: 0 4px 12px rgba(79, 70, 229, 0.4);
}

.toggle-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 6px 16px rgba(79, 70, 229, 0.5);
}

.toggle-btn:active {
  transform: scale(0.95);
}

/* ==================== Header ==================== */
:deep(.el-header) {
  border-bottom: 1px solid #E5E7EB;
  background-color: #FFFFFF;
  height: auto;
  padding: 12px 28px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.04);
}

/* ==================== Main Content ==================== */
.el-main {
  background-color: #F5F7FA;
  min-height: 100vh;
  padding: 24px 32px;
}

.main-container {
  max-width: 1320px;
  margin: 0 auto;
}

/* ==================== Footer ==================== */
.el-footer {
  text-align: center;
  color: #6B7280;
  font-size: 13px;
  font-weight: 400;
  background-color: #F5F7FA;
  padding: 24px;
}

/* ==================== User Header Actions ==================== */
.header-actions {
  display: flex;
  align-items: center;
  gap: 16px;
}

.user-chip {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 6px 16px;
  border-radius: 50px;
  background-color: #F9FAFB;
  border: 1px solid #E5E7EB;
  color: #374151;
  font-weight: 500;
  cursor: pointer;
  outline: none;
  transition: all 0.2s ease;
}

.user-chip:hover {
  background-color: #F3F4F6;
  border-color: #D1D5DB;
}

.user-name {
  max-width: 180px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.arrow {
  font-size: 14px;
  color: #6B7280;
}

/* ==================== Dropdown Menu ==================== */
:deep(.el-dropdown-menu__item) {
  padding: 14px 20px;
  font-size: 15px;
  letter-spacing: 0.3px;
  justify-content: center;
  font-weight: 500;
  transition: all 0.15s ease;
}

:deep(.el-dropdown-menu__item:hover) {
  background-color: #EEF2FF;
  color: #4F46E5;
}
</style>


