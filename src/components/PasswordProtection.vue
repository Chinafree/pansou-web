<script setup lang="ts">
import { ref, onMounted } from 'vue';

// 密码输入状态
const password = ref('');
const error = ref('');
const loading = ref(false);

// 从环境变量获取密码
const PASSWORD = import.meta.env.VITE_PASSWORD || 'free';

defineProps({
  onVerify: Function
});
const emit = defineEmits(['verify']);

// 处理密码提交
const handleSubmit = () => {
  loading.value = true;
  error.value = '';

  // 简单的密码验证
  setTimeout(() => {
    if (password.value === PASSWORD) {
      // 验证成功
      localStorage.setItem('isVerified', 'true');
      emit('verify', true);
      if (props.onVerify) {
        props.onVerify(true);
      }
    } else {
      // 验证失败
      error.value = '密码错误，请重试';
    }
    loading.value = false;
  }, 500);
};

// 检查是否已经验证过
const checkVerificationStatus = () => {
  const isVerified = localStorage.getItem('isVerified') === 'true';
  if (isVerified) {
    emit('verify', true);
    if (props.onVerify) {
      props.onVerify(true);
    }
  }
};

// 组件挂载时检查验证状态
onMounted(() => {
  checkVerificationStatus();
});
</script>

<template>
  <div class="min-h-screen bg-background flex items-center justify-center p-4">
    <div class="bg-card border border-border rounded-lg shadow-lg w-full max-w-md p-8 animate-fadeIn">
      <div class="text-center mb-6">
        <div class="w-12 h-12 bg-primary rounded-lg flex items-center justify-center mx-auto mb-4">
          <svg class="w-6 h-6 text-primary-foreground" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path>
          </svg>
        </div>
        <h1 class="text-2xl font-bold text-foreground">PanSou</h1>
        <p class="text-muted-foreground mt-2">请输入密码以继续访问</p>
      </div>

      <form @submit.prevent="handleSubmit" class="space-y-4">
        <div>
          <label for="password" class="block text-sm font-medium text-foreground mb-1">密码</label>
          <div class="relative">
            <input
              type="password"
              id="password"
              v-model="password"
              class="w-full px-4 py-2 border border-border rounded-md bg-input focus:outline-none focus:ring-2 focus:ring-primary focus:border-transparent"
              placeholder="请输入密码"
              :disabled="loading"
              required
            />
          </div>
          <p v-if="error" class="mt-1 text-sm text-destructive">{{ error }}</p>
        </div>

        <button
          type="submit"
          class="w-full py-2 px-4 bg-primary hover:bg-primary/90 text-primary-foreground font-medium rounded-md transition-colors"
          :disabled="loading"
        >
          <span v-if="loading" class="inline-block animate-spin rounded-full h-4 w-4 border-2 border-white border-t-transparent mr-2"></span>
          {{ loading ? '验证中...' : '验证' }}
        </button>
      </form>

      <div class="mt-6 text-center text-sm text-muted-foreground">
        <p>因服务器资源有限，加入了密码校验。</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.bg-background {
  background-color: hsl(var(--background));
}

.bg-card {
  background-color: hsl(var(--card));
}

.border-border {
  border-color: hsl(var(--border));
}

.text-foreground {
  color: hsl(var(--foreground));
}

.text-muted-foreground {
  color: hsl(var(--muted-foreground));
}

.bg-primary {
  background-color: hsl(var(--primary));
}

.text-primary-foreground {
  color: hsl(var(--primary-foreground));
}

.bg-input {
  background-color: hsl(var(--input));
}

.text-destructive {
  color: hsl(var(--destructive));
}
</style>
