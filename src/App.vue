<template>
  <div class="mdo-app">
    <div class="mdo-container">
      <header class="mdo-topbar">
        <div class="mdo-brand">
          <div class="mdo-icon-btn" aria-hidden="true">⌂</div>
          <div class="mdo-brand-title">
            <strong>模锭挤出中控系统</strong>
            <span>待办事项应用（墨刀风格 UI）</span>
          </div>
        </div>

        <nav class="mdo-nav" aria-label="顶部导航">
          <span class="mdo-chip mdo-chip--active">报表日志</span>
          <span class="mdo-chip">配方管理</span>
          <span class="mdo-chip">数据分析</span>
          <span class="mdo-chip">数据统计</span>
        </nav>

        <button class="mdo-icon-btn" type="button" aria-label="退出">
          ⎋
        </button>
      </header>

      <MDOControlPrototype />
    </div>
  </div>
</template>

<script setup lang="ts">
import MDOControlPrototype from "./components/MDOControlPrototype.vue";
</script>

