不需要再額外引入 vue 函數例如 ref 或 computed 已經配置自動引入了 組件酷的代碼直接貼上就好 也不需要在頁面中另外引入 自己封裝的 component 也一樣 不需要寫 import MyComponent from '@/component/MyComponent.vue'
使用圖標的方式
(1)到 Iconify 選擇要引入的 icon 複製 component 代碼到.vue 文件 例如:<Icon icon="bxs:cart-add" width="24" height="24" />
(2)直接使用組件庫提供的圖標 使用方式:<el-icon><Male /></el-icon>
可以根據 api 簡化使用方式 例如 button 結合 icon <el-button icon="Edit" type="success">Success</el-button>
