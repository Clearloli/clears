<script lang="ts" setup>
/** 定义变量 */
import { ref, reactive } from 'vue';
/** 路由跳转 */
import { useRouter } from 'vue-router';
/** 图标 */
import { User, Lock, Key, Picture, Loading, MagicStick } from '@element-plus/icons-vue';
/** 表单以及校验 */
import { type FormInstance, FormRules } from 'element-plus';
/** 登录相关 */
import { useUserStore } from '@/store/modules/user';
import { ILoginRequestData } from '@/api/login/types/login';
import { getLoginCodeApi } from '@/api/login';
/** 主题相关 */
import { type ThemeName, useTheme } from '@/hooks/useTheme';
const { themeList, activeThemeName, setTheme } = useTheme();
/** 实例化路由 */
const router = useRouter();

/** 登录按钮 Loading */
const loading = ref(false);

/** 验证码图片 URL */
const codeUrl = ref('');

/** 表单校验实例 */
const loginFormRef = ref<FormInstance | null>(null);

/** 登录表单 */
const loginForm: ILoginRequestData = reactive({
  username: 'admin',
  password: '123456',
  code: '',
});

/** 登录表单校验规则 */
const loginFormRules = reactive<FormRules>({
  username: [{ required: true, message: '请输入用户名', trigger: 'blur' }],
  password: [
    {
      required: true,
      message: '请输入密码',
      trigger: 'blur',
    },
    {
      min: 8,
      max: 16,
      message: '长度在 8 到 16 个字符之间',
      trigger: 'blur',
    },
  ],
  code: [
    {
      required: true,
      message: '请输入验证码',
      trigger: 'blur',
    },
  ],
});

/** 登录 */
const handleLogin = () => {
  loginFormRef.value?.validate((valid: Boolean) => {
    if (valid) {
      loading.value = true;
      /** 登录接口以及登录跳转 */
      useUserStore()
        .login(loginForm)
        .then(() => {
          router.push({ path: '/' });
        })
        .catch(() => {
          createCode();
          loginForm.password = '';
        })
        .finally(() => {
          loading.value = false;
        });
    } else {
      return false;
    }
  });
};

/** 创建验证码 */
const createCode = () => {
  // 先清空验证码
  loginForm.code = '';
  // 获取验证码
  codeUrl.value = '';
  // 实际请求接口地址: https://mock.mengxuegu.com/mock/63218b5fb4c53348ed2bc212/api/v1/login/code
  getLoginCodeApi().then(res => {
    if (res.code === 0) {
      codeUrl.value = res.data;
    }
  });
};

/** 初始化验证码 */
createCode();

const handleSelectTheme = () => {};
</script>

<template>
  <div class="login-container">
    <div class="theme-switch">
      <el-dropdown trigger="click" @command="handleSelectTheme">
        <div>
          <el-tooltip effect="dark" content="主题模式" placement="bottom">
            <el-icon :size="20">
              <MagicStick />
            </el-icon>
          </el-tooltip>
        </div>
        <template #dropdown>
          <el-dropdown-menu>
            <el-dropdown-item
              v-for="(item, index) in themeList"
              :key="item"
              :disabled="activeThemeName === item.name"
              :command="item.name"
            >
              <span>{{ item.title }}</span>
            </el-dropdown-item>
          </el-dropdown-menu>
        </template>
      </el-dropdown>
    </div>
    <div class="login-card">
      <div class="title">
        <img src="@/assets/layout/logo-text-2.png" alt="" class="" />
      </div>
      <div class="content">
        <el-form ref="loginFormRef" :model="loginForm" :rules="loginFormRules" @keyup.enter="handleLogin">
          <el-form-item prop="username">
            <el-input
              v-model.trim="loginForm.username"
              placeholder="用户名"
              type="text"
              tabindex="1"
              :prefix-icon="User"
              size="large"
            />
          </el-form-item>
          <el-form-item prop="password">
            <el-input
              v-model.trim="loginForm.password"
              placeholder="密码"
              type="password"
              tabindex="2"
              :prefix-icon="Lock"
              size="large"
              show-password
            />
          </el-form-item>
          <el-form-item prop="code">
            <el-input
              v-model.trim="loginForm.code"
              placeholder="验证码"
              type="text"
              tabindex="3"
              size="large"
              :prefix-icon="Key"
            >
              <template #append>
                <el-image :src="codeUrl" @click="createCode" draggable="false">
                  <template #placeholder>
                    <el-icon><Picture /></el-icon>
                  </template>
                  <template #error>
                    <el-icon><Loading /></el-icon>
                  </template>
                </el-image>
              </template>
            </el-input>
          </el-form-item>
          <el-button :loading="loading" type="primary" size="large" @click.prevent="handleLogin"> 登 录 </el-button>
        </el-form>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.login-container {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100%;
  width: 100%;
  min-width: 490px;
  .theme-switch {
    position: fixed;
    top: 5%;
    right: 5%;
    cursor: pointer;
  }
  .login-card {
    overflow: hidden;
    width: 480px;
    border-radius: 20px;
    background-color: #fff;
    box-shadow: 0 0 10px #dcdfe6;
    .title {
      display: flex;
      align-items: center;
      justify-content: center;
      height: 150px;
      img {
        height: 100%;
      }
    }
    .content {
      padding: 20px 50px 50px;
      :deep(.el-input-group__append) {
        padding: 0;
        overflow: hidden;
      }
      :deep(.el-button) {
        width: 100%;
        margin-top: 10px;
      }
    }
  }
}
</style>
