<template>
  <div class="mdo-proto">
    <!-- Top navigation -->
    <header class="mdo-proto__topbar">
      <div class="mdo-proto__topbar-left">
        <button class="mdo-proto__iconBtn" type="button" aria-label="首页" @click="toastIt('返回首页', '示例：这里可切换页面')">
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" aria-hidden="true">
            <path
              d="M4 10.5L12 4L20 10.5V20a1 1 0 0 1-1 1h-5v-6H10v6H5a1 1 0 0 1-1-1v-9.5Z"
              stroke="currentColor"
              stroke-width="2"
              stroke-linejoin="round"
            />
          </svg>
        </button>
        <div class="mdo-proto__brand">
          <div class="mdo-proto__brandTitle">
            模锭挤出中控系统
          </div>
          <div class="mdo-proto__brandSub">数字孪生 / 运行监控（待办演示映射）</div>
        </div>
      </div>

      <nav class="mdo-proto__tabs" aria-label="顶部功能切换">
        <button
          v-for="tab in topTabs"
          :key="tab.id"
          type="button"
          class="mdo-proto__tab"
          :class="activeTopTab === tab.id ? 'mdo-proto__tab--active' : ''"
          @click="activeTopTab = tab.id"
        >
          {{ tab.label }}
        </button>
      </nav>

      <button class="mdo-proto__iconBtn mdo-proto__iconBtn--ghost" type="button" aria-label="退出" @click="toastIt('已退出', '示例按钮')">
        <svg width="18" height="18" viewBox="0 0 24 24" fill="none" aria-hidden="true">
          <path d="M10 17l5-5-5-5" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </button>
    </header>

    <main class="mdo-proto__main">
      <!-- Left: quick tiles -->
      <section class="mdo-proto__left">
        <div class="mdo-proto__leftTitle">
          设备异常 / 待办映射
          <span class="mdo-proto__leftTitleTime">{{ formatTime(now) }}</span>
        </div>

        <div class="mdo-proto__smallTiles">
          <button
            v-for="t in leftTiles"
            :key="t.id"
            class="mdo-proto__smallTile"
            :class="t.done ? 'mdo-proto__smallTile--done' : t.tone"
            type="button"
            @click="toggleDone(t.id)"
          >
            <div class="mdo-proto__smallTileTop">
              <span class="mdo-proto__smallTileIcon" aria-hidden="true">
                <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
                  <path
                    d="M12 2L20 6V12C20 17 16.5 21 12 22C7.5 21 4 17 4 12V6L12 2Z"
                    stroke="currentColor"
                    stroke-width="2"
                    stroke-linejoin="round"
                  />
                </svg>
              </span>
              <span class="mdo-proto__smallTileTag">{{ t.status }}</span>
            </div>
            <div class="mdo-proto__smallTileName">{{ t.name }}</div>
            <div class="mdo-proto__smallTileMeta">
              优先级：{{ t.priority }}
            </div>
          </button>
        </div>
      </section>

      <!-- Center: device grid -->
      <section class="mdo-proto__center">
        <div class="mdo-proto__panelHeader">
          <div class="mdo-proto__panelHeaderLeft">
            运行设备瓷片
          </div>
          <div class="mdo-proto__panelHeaderRight">
            <span class="mdo-proto__pill mdo-proto__pill--good">进行中：{{ stats.active }}</span>
            <span class="mdo-proto__pill mdo-proto__pill--mid">已完成：{{ stats.completed }}</span>
            <span class="mdo-proto__pill mdo-proto__pill--bad">超期：{{ stats.overdue }}</span>
          </div>
        </div>

        <div class="mdo-proto__deviceGrid">
          <button
            v-for="d in centerTiles"
            :key="d.id"
            class="mdo-proto__deviceTile"
            :class="d.done ? 'mdo-proto__deviceTile--done' : d.tone"
            type="button"
            @click="toggleDone(d.id)"
          >
            <div class="mdo-proto__deviceTileTop">
              <span class="mdo-proto__deviceIcon" aria-hidden="true">
                <svg width="22" height="22" viewBox="0 0 24 24" fill="none">
                  <path
                    d="M4 10V14C4 14.5523 4.44772 15 5 15H8L13 19V5L8 9H5C4.44772 9 4 9.44772 4 10Z"
                    stroke="currentColor"
                    stroke-width="2"
                    stroke-linejoin="round"
                  />
                </svg>
              </span>
              <span class="mdo-proto__deviceBadge">
                {{ d.done ? '已完成' : d.status }}
              </span>
            </div>
            <div class="mdo-proto__deviceName">{{ d.name }}</div>
            <div class="mdo-proto__deviceBottom">
              <div class="mdo-proto__deviceMeta">截止：{{ d.due || '—' }}</div>
              <div class="mdo-proto__deviceLevel">优先级：{{ d.priority }}</div>
            </div>
          </button>
        </div>
      </section>

      <!-- Right: parameter panel -->
      <aside class="mdo-proto__right">
        <div class="mdo-proto__rightTitle">参数显示</div>

        <div class="mdo-proto__paramTable">
          <div v-for="row in params" :key="row.label" class="mdo-proto__paramRow">
            <div class="mdo-proto__paramLabel">{{ row.label }}</div>
            <div class="mdo-proto__paramValue">
              <span class="mdo-proto__paramValueMain">{{ row.value }}</span>
              <span class="mdo-proto__paramTrend" :class="row.trendTone">
                {{ row.trend }}
              </span>
            </div>
          </div>
        </div>

        <div class="mdo-proto__rightSub">
          <div class="mdo-proto__rightSubItem">
            参考表
            <span class="mdo-proto__rightSubBadge">实时更新（示例）</span>
          </div>
          <div class="mdo-proto__rightSubList">
            <div class="mdo-proto__rightSubLine" v-for="s in referenceRows" :key="s.k">
              <span class="mdo-proto__rightSubKey">{{ s.k }}</span>
              <span class="mdo-proto__rightSubVal">{{ s.v }}</span>
            </div>
          </div>
        </div>
      </aside>
    </main>

    <!-- Bottom: status line -->
    <footer class="mdo-proto__bottom">
      <div class="mdo-proto__bottomStatus">
        一号橡胶挤出生产线　产品编号：SG531 DS A　订单名称：SG531 DS A
        <span class="mdo-proto__bottomStatusDot">/</span>
        生产批次：20250216　生产速度：100.2m/min　生产耗时：15000
      </div>

      <div class="mdo-proto__bottomActions">
        <button class="mdo-proto__action mdo-proto__action--success" type="button" @click="toastIt('温控启动', '示例：触发温控策略')">
          温控启动
        </button>
        <button class="mdo-proto__action mdo-proto__action--danger" type="button" @click="toastIt('温控停止', '示例：停止温控策略')">
          温控停止
        </button>
        <button class="mdo-proto__action mdo-proto__action--info" type="button" @click="toastIt('加速', '示例：提高转速档位')">
          加速
        </button>
        <button class="mdo-proto__action mdo-proto__action--warn" type="button" @click="toastIt('减速', '示例：降低转速档位')">
          减速
        </button>
        <button class="mdo-proto__action mdo-proto__action--cyan" type="button" @click="toastIt('倍率', '示例：切换倍率档位')">
          倍位
        </button>
        <button class="mdo-proto__action mdo-proto__action--success" type="button" @click="toastIt('启动', '示例：开始挤出流程')">
          启动
        </button>
        <button class="mdo-proto__action mdo-proto__action--danger" type="button" @click="toastIt('停止', '示例：停止挤出流程')">
          停止
        </button>
      </div>
    </footer>

    <!-- Toast -->
    <div v-if="toast" class="mdo-proto__toast">
      <div class="mdo-proto__toastTitle">{{ toast.title }}</div>
      <div class="mdo-proto__toastDetail">{{ toast.detail }}</div>
      <button class="mdo-proto__toastClose" type="button" @click="toast = null">关闭</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onBeforeUnmount, ref } from "vue";

type Priority = "低" | "中" | "高";

type Todo = {
  id: string;
  title: string;
  priority: Priority;
  due: string; // YYYY-MM-DD
  done: boolean;
  createdAt: number;
};

type TopTabId = "分析" | "统计";

type TileTone = "toneGood" | "toneWarn" | "toneBad";

type SmallTile = {
  id: string;
  name: string;
  priority: Priority;
  due: string;
  done: boolean;
  status: string;
  tone: TileTone;
};

const now = ref(new Date());
const tick = window.setInterval(() => {
  now.value = new Date();
}, 1000);
onBeforeUnmount(() => window.clearInterval(tick));

const topTabs = [
  { id: "分析" as const, label: "数据分析" },
  { id: "统计" as const, label: "数据统计" },
];

const activeTopTab = ref<TopTabId>("统计");

const makeId = () => {
  if (typeof crypto !== "undefined" && "randomUUID" in crypto) return crypto.randomUUID();
  return `t_${Date.now()}_${Math.random().toString(16).slice(2)}`;
};

const defaultDue = () => {
  const d = new Date();
  d.setDate(d.getDate() + 1);
  const yyyy = d.getFullYear();
  const mm = String(d.getMonth() + 1).padStart(2, "0");
  const dd = String(d.getDate()).padStart(2, "0");
  return `${yyyy}-${mm}-${dd}`;
};

const parseDue = (due: string) => {
  if (!due) return null;
  const dt = new Date(`${due}T23:59:59`);
  return Number.isNaN(dt.getTime()) ? null : dt;
};

const isOverdue = (t: Todo) => {
  if (t.done) return false;
  const dt = parseDue(t.due);
  if (!dt) return false;
  return dt.getTime() < now.value.getTime();
};

const todoStatusText = (t: Todo) => {
  if (t.done) return "已完成";
  if (isOverdue(t)) return "已超期";
  return "进行中";
};

const statusTone = (t: Todo): TileTone => {
  if (t.done) return "toneGood";
  if (isOverdue(t)) return "toneBad";
  // 优先级影响轻微色调
  return t.priority === "高" ? "toneWarn" : "toneGood";
};

const todos = ref<Todo[]>([
  {
    id: makeId(),
    title: "检查配方批次参数",
    priority: "高",
    due: defaultDue(),
    done: false,
    createdAt: Date.now() - 1000 * 60 * 60 * 6,
  },
  {
    id: makeId(),
    title: "监控熔体温度曲线（异常回归）",
    priority: "中",
    due: (() => {
      const d = new Date();
      d.setDate(d.getDate());
      return `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, "0")}-${String(d.getDate()).padStart(2, "0")}`;
    })(),
    done: false,
    createdAt: Date.now() - 1000 * 60 * 60 * 3,
  },
  {
    id: makeId(),
    title: "更新产线运行日志",
    priority: "低",
    due: defaultDue(),
    done: true,
    createdAt: Date.now() - 1000 * 60 * 60 * 20,
  },
  {
    id: makeId(),
    title: "处理报警：供料压力波动",
    priority: "高",
    due: (() => {
      const d = new Date();
      d.setDate(d.getDate() - 1);
      return `${d.getFullYear()}-${String(d.getMonth() + 1).padStart(2, "0")}-${String(d.getDate()).padStart(2, "0")}`;
    })(),
    done: false,
    createdAt: Date.now() - 1000 * 60 * 60 * 9,
  },
]);

const stats = computed(() => {
  const total = todos.value.length;
  const completed = todos.value.filter((t) => t.done).length;
  const overdue = todos.value.filter((t) => isOverdue(t)).length;
  const active = total - completed;
  return { total, completed, overdue, active };
});

const centerTiles = computed(() => {
  // 用 top tab 控制显示“分析/统计”两种视图（用颜色与排序模拟）
  const list = [...todos.value];
  if (activeTopTab.value === "分析") {
    // 分析：超期优先
    list.sort((a, b) => Number(isOverdue(a)) - Number(isOverdue(b)) || a.createdAt - b.createdAt);
  } else {
    // 统计：未完成优先
    list.sort((a, b) => Number(a.done) - Number(b.done) || b.priority.localeCompare(a.priority));
  }
  return list.map((t) => ({
    id: t.id,
    name: t.title,
    priority: t.priority,
    due: t.due,
    done: t.done,
    status: todoStatusText(t),
    tone: statusTone(t),
  }));
});

const leftTiles = computed<SmallTile[]>(() => {
  // 左侧展示最多 6 条：按超期、进行中、完成的顺序取
  const overdue = todos.value.filter((t) => isOverdue(t));
  const active = todos.value.filter((t) => !t.done && !isOverdue(t));
  const done = todos.value.filter((t) => t.done);
  const list = [...overdue, ...active, ...done].slice(0, 6);
  return list.map((t) => ({
    id: t.id,
    name: t.title,
    priority: t.priority,
    due: t.due,
    done: t.done,
    status: todoStatusText(t),
    tone: statusTone(t),
  }));
});

type Toast = { title: string; detail: string } | null;
const toast = ref<Toast>(null);
let toastTimer: number | undefined;

const toastIt = (title: string, detail: string) => {
  toast.value = { title, detail };
  if (toastTimer) window.clearTimeout(toastTimer);
  toastTimer = window.setTimeout(() => {
    toast.value = null;
  }, 2000);
};

const toggleDone = (id: string) => {
  const t = todos.value.find((x) => x.id === id);
  if (!t) return;
  t.done = !t.done;
  toastIt(t.done ? "任务完成" : "任务恢复", t.title);
};

const formatTime = (d: Date) => {
  const hh = String(d.getHours()).padStart(2, "0");
  const mm = String(d.getMinutes()).padStart(2, "0");
  return `${hh}:${mm}`;
};

const params = computed(() => {
  const up = (v: number) => `▲ ${v.toFixed(1)}`;
  const down = (v: number) => `▼ ${v.toFixed(1)}`;
  const seed = stats.active + stats.overdue * 2 + stats.completed * 0.4;
  const v1 = 82 + seed * 0.7;
  const v2 = 120 + seed * 0.9;
  const v3 = 65 + seed * 0.35;
  const v4 = 0.8 + seed * 0.03;
  return [
    { label: "设备开机率", value: `${Math.max(75, Math.min(99, 92 - stats.overdue * 1.9)).toFixed(0)}%`, trend: stats.overdue ? down(stats.overdue * 0.2) : up(0.3), trendTone: stats.overdue ? "trendDown" : "trendUp" },
    { label: "熔体温度", value: `${v1.toFixed(1)}°C`, trend: v1 > 95 ? up(0.6) : down(0.5), trendTone: v1 > 95 ? "trendUp" : "trendDown" },
    { label: "供料压力", value: `${v2.toFixed(0)}kPa`, trend: stats.overdue ? down(0.8) : up(0.4), trendTone: stats.overdue ? "trendDown" : "trendUp" },
    { label: "运行档位", value: `${v3.toFixed(0)}`, trend: up(v4), trendTone: "trendUp" },
    { label: "故障风险", value: `${Math.max(0.1, 0.9 - stats.completed * 0.08).toFixed(2)}`, trend: stats.overdue ? up(0.2) : down(0.1), trendTone: stats.overdue ? "trendUp" : "trendDown" },
    { label: "报警数量", value: `${stats.overdue}`, trend: stats.overdue ? up(0.3) : down(0.2), trendTone: stats.overdue ? "trendUp" : "trendDown" },
  ];
});

const referenceRows = computed(() => {
  const a = stats.active;
  const c = stats.completed;
  const o = stats.overdue;
  return [
    { k: "当前批次", v: `B-${String(20250216 + a * 7).slice(-6)}` },
    { k: "计划速度", v: `${100 + a * 0.3}m/min` },
    { k: "实际速度", v: `${(100 + a * 0.3 - o * 0.7).toFixed(1)}m/min` },
    { k: "温控偏差", v: `${(0.6 + o * 0.12).toFixed(2)}°C` },
    { k: "联网状态", v: o > 0 ? "异常" : "正常" },
  ];
});
</script>

