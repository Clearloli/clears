<script setup lang="ts">
import { h } from 'vue';
import { useTheme } from '@/hooks/useTheme';
import { ElConfigProvider, ElNotification } from 'element-plus';
import zhCn from 'element-plus/lib/locale/lang/zh-cn';
import en from 'element-plus/lib/locale/lang/en';

const { initTheme } = useTheme();

/** 初始化主题 */
initTheme();
/** 将 Element Plus 的语言设置为中文 */
// const locale = zhCn;
const locale = en;

ElNotification({
  title: 'Hello',
  dangerouslyUseHTMLString: true,
  message:
    '<a style="color: teal;" href="http://github.com/un-pay/v3-admin-vite" target="_blank">小项目获取 star 不易，如果你喜欢这个项目的画，欢迎点击这里支持一个 star ! 这是作者持续维护的唯一动力</a>',
  duration: 0,
  position: 'bottom-right',
});
// ElNotification({
//   title: 'Hello',
//   message: h(
//     'a',
//     { style: 'color: teal', target: '_blank', href: 'http://github.com/un-pay/v3-admin-vite' },
//     '小项目获取 star 不易，如果你喜欢这个项目的画，欢迎点击这里支持一个 star ! 这是作者持续维护的唯一动力'
//   ),
//   duration: 0,
//   position: 'bottom-right',
//   showClose: false,
// });
</script>

<template>
  <ElConfigProvider :locale="locale">
    <router-view></router-view>
  </ElConfigProvider>
</template>
