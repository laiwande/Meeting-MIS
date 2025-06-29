<script setup lang="ts">
import { RouterLink } from 'vue-router'
import { useAuthStore } from '@/stores/auth';
import { Button } from '@/components/ui/button'
import { onMounted, ref } from 'vue'

// 导入所有需要的图标
import { 
  Home,               // 总览
  Building2,          // 会议室列表
  CalendarDays,       // 我的预约
  CalendarPlus,       // 新建预约
  BarChart,           // 状态看板 & 使用统计
  Settings,           // 会议室设置
  Users,              // 用户管理
  CalendarCheck,      // 会议日程
  Bell               // 通知
} from 'lucide-vue-next'

const authStore = useAuthStore();
const unreadCount = ref(0)

onMounted(async () => {
  try {
    const res = await fetch('/api/notices/my')
    const notices = await res.json()
    const lastReadId = Number(localStorage.getItem('lastReadNoticeId') || 0)
    unreadCount.value = notices.filter((n:any) => n.id > lastReadId).length
  } catch {}
})
</script>

<template>
  <aside class="w-64 border-r bg-background p-4 flex flex-col">
    <h2 class="text-xl font-bold mb-6 px-4">会议室预订系统</h2>
    
    <nav class="flex flex-col gap-1">
      <h3 class="text-sm font-semibold text-muted-foreground px-4 mt-2 mb-1">主菜单</h3>
      <Button variant="ghost" class="justify-start" as-child>
        <RouterLink to="/dashboard" active-class="bg-accent text-accent-foreground">
          <Home class="mr-2 h-4 w-4" />
          总览仪表盘
        </RouterLink>
      </Button>

      <Button variant="ghost" class="justify-start" as-child>
        <RouterLink to="/reservations/new" active-class="bg-accent text-accent-foreground">
          <CalendarPlus class="mr-2 h-4 w-4" />
          新建预约
        </RouterLink>
      </Button>

      <Button variant="ghost" class="justify-start" as-child>
        <RouterLink to="/rooms" active-class="bg-accent text-accent-foreground">
          <Building2 class="mr-2 h-4 w-4" />
          会议室列表
        </RouterLink>
      </Button>

      <Button variant="ghost" class="justify-start" as-child>
        <RouterLink to="/reservations/my" active-class="bg-accent text-accent-foreground">
          <CalendarDays class="mr-2 h-4 w-4" />
          我的预约
        </RouterLink>
      </Button>

      <Button variant="ghost" class="justify-start" as-child>
        <RouterLink to="/calendar" active-class="bg-accent text-accent-foreground">
          <CalendarCheck class="mr-2 h-4 w-4" />
          会议日程
        </RouterLink>
      </Button>

      <Button variant="ghost" class="justify-start relative" as-child>
        <RouterLink to="/notices" active-class="bg-accent text-accent-foreground">
          <Bell class="mr-2 h-4 w-4" />
          我的通知
          <span v-if="unreadCount > 0" class="ml-2 inline-flex items-center justify-center px-2 py-0.5 text-xs font-bold leading-none text-white bg-red-500 rounded-full">
            {{ unreadCount }}
          </span>
        </RouterLink>
      </Button>

      <template v-if="authStore.hasRole('ROOM_ADMIN') || authStore.hasRole('SYSTEM_ADMIN')" class="mt-4">
        <h3 class="text-sm font-semibold text-muted-foreground px-4 mt-2 mb-1">会议室管理</h3>
        <Button variant="ghost" class="justify-start" as-child>
          <RouterLink to="/admin/dashboard" active-class="bg-accent text-accent-foreground">
            <BarChart class="mr-2 h-4 w-4" />
            状态看板
          </RouterLink>
        </Button>
        <Button variant="ghost" class="justify-start" as-child>
          <RouterLink to="/admin/manage-rooms" active-class="bg-accent text-accent-foreground">
            <Settings class="mr-2 h-4 w-4" />
            会议室设置
          </RouterLink>
        </Button>
        <Button variant="ghost" class="justify-start" as-child>
          <RouterLink to="/admin/statistics" active-class="bg-accent text-accent-foreground">
            <BarChart class="mr-2 h-4 w-4" />
            使用统计
          </RouterLink>
        </Button>
      </template>
      
      <template v-if="authStore.hasRole('SYSTEM_ADMIN')" class="mt-4">
        <h3 class="text-sm font-semibold text-muted-foreground px-4 mb-1">系统管理</h3>
        <Button variant="ghost" class="justify-start" as-child>
          <RouterLink to="/admin/users" active-class="bg-accent text-accent-foreground">
            <Users class="mr-2 h-4 w-4" />
            用户管理
          </RouterLink>
        </Button>
      </template>
    </nav>
  </aside>
</template>