<template>
  <div class="mdo-grid">
    <!-- Left panel -->
    <aside class="mdo-panel mdo-glass mdo-neon">
      <div class="mdo-section-title">
        <h2>控制台 / 待办管理</h2>
        <small>{{ formatTime(now) }}</small>
      </div>

      <div class="mdo-row" style="margin-bottom: 12px">
        <span class="mdo-chip mdo-chip--active">任务总览</span>
      </div>

      <div style="display: grid; gap: 10px">
        <div style="display: grid; gap: 6px">
          <label style="font-size: 12px; color: rgba(226,232,240,0.7)"
            >任务名称</label
          >
          <input
            class="mdo-input"
            v-model.trim="draft.title"
            placeholder="例如：检查产线数据异常"
          />
        </div>

        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px">
          <div style="display: grid; gap: 6px">
            <label style="font-size: 12px; color: rgba(226,232,240,0.7)"
              >优先级</label
            >
            <select class="mdo-select" v-model="draft.priority">
              <option value="低">低</option>
              <option value="中">中</option>
              <option value="高">高</option>
            </select>
          </div>
          <div style="display: grid; gap: 6px">
            <label style="font-size: 12px; color: rgba(226,232,240,0.7)"
              >截止时间</label
            >
            <input class="mdo-input" type="date" v-model="draft.due" />
          </div>
        </div>

        <div style="display: flex; gap: 10px; align-items: center">
          <button class="mdo-btn mdo-btn--primary" type="button" @click="addTodo">
            + 添加任务
          </button>
        </div>

        <div class="mdo-filters" style="margin-top: 4px; margin-bottom: 4px">
          <button
            class="mdo-btn"
            :class="filter === 'all' ? 'mdo-neon' : ''"
            type="button"
            @click="filter = 'all'"
          >
            全部
          </button>
          <button
            class="mdo-btn"
            :class="filter === 'active' ? 'mdo-neon' : ''"
            type="button"
            @click="filter = 'active'"
          >
            进行中
          </button>
          <button
            class="mdo-btn"
            :class="filter === 'completed' ? 'mdo-neon' : ''"
            type="button"
            @click="filter = 'completed'"
          >
            已完成
          </button>
          <button
            class="mdo-btn"
            :class="filter === 'overdue' ? 'mdo-neon' : ''"
            type="button"
            @click="filter = 'overdue'"
          >
            已超期
          </button>
        </div>

        <div style="display: flex; gap: 10px; flex-wrap: wrap">
          <span class="mdo-pill mdo-pill--low">活动：{{ stats.active }}</span>
          <span class="mdo-pill mdo-pill--mid">完成：{{ stats.completed }}</span>
          <span class="mdo-pill mdo-pill--high">超期：{{ stats.overdue }}</span>
        </div>
      </div>
    </aside>

    <!-- Main panel -->
    <section>
      <div class="mdo-stats">
        <div class="mdo-stat mdo-neon">
          <div class="mdo-row">
            <span class="mdo-chip">任务总数</span>
          </div>
          <strong>{{ stats.total }}</strong>
          <span>当前周期</span>
        </div>
        <div class="mdo-stat">
          <span class="mdo-chip mdo-chip--active">进行中</span>
          <strong style="color: rgba(224,242,254,0.98)">{{ stats.active }}</strong>
          <span>未完成任务</span>
        </div>
        <div class="mdo-stat">
          <span class="mdo-chip">已完成</span>
          <strong style="color: rgba(209,250,229,0.98)">{{ stats.completed }}</strong>
          <span>已标记完成</span>
        </div>
        <div class="mdo-stat">
          <span class="mdo-chip">已超期</span>
          <strong style="color: rgba(254,226,226,0.98)">{{ stats.overdue }}</strong>
          <span>需要关注</span>
        </div>
      </div>

      <div class="mdo-panel mdo-glass" style="margin-bottom: 14px">
        <div class="mdo-section-title">
          <h2>任务列表 / 表格信息区</h2>
          <small>支持：完成 / 编辑 / 删除</small>
        </div>

        <div class="mdo-row" style="margin-bottom: 12px">
          <div style="flex: 1; max-width: 420px">
            <input
              class="mdo-input"
              v-model.trim="search"
              placeholder="搜索任务（例如：数据/报警/运行）"
            />
          </div>
          <div style="display: flex; gap: 10px; align-items: center">
            <button class="mdo-btn mdo-btn--success" type="button" @click="markAllActiveDone">
              一键完成
            </button>
            <button class="mdo-btn mdo-btn--danger" type="button" @click="clearCompleted">
              清空已完成
            </button>
          </div>
        </div>

        <div class="mdo-table-wrap">
          <table class="mdo-table">
            <thead>
              <tr>
                <th style="width: 70px">序号</th>
                <th>任务名称</th>
                <th style="width: 110px">优先级</th>
                <th style="width: 160px">截止时间</th>
                <th style="width: 130px">状态</th>
                <th style="width: 220px">操作</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(t, idx) in visibleTodos" :key="t.id">
                <td>{{ idx + 1 }}</td>
                <td style="font-weight: 650">
                  {{ t.title }}
                </td>
                <td>
                  <span
                    class="mdo-badge"
                    :class="
                      t.priority === '低'
                        ? 'mdo-pill--low'
                        : t.priority === '中'
                          ? 'mdo-pill--mid'
                          : 'mdo-pill--high'
                    "
                    >{{ t.priority }}</span
                  >
                </td>
                <td>
                  <span style="color: rgba(226,232,240,0.85)">{{
                    formatDue(t.due)
                  }}</span>
                </td>
                <td>
                  <span
                    class="mdo-badge"
                    :class="todoStatusClass(t)"
                    >{{ todoStatusText(t) }}</span
                  >
                </td>
                <td>
                  <div style="display: flex; gap: 10px; flex-wrap: wrap">
                    <button
                      class="mdo-btn"
                      :class="t.done ? '' : 'mdo-btn--success'"
                      type="button"
                      @click="toggleDone(t.id)"
                    >
                      {{ t.done ? "撤销完成" : "标记完成" }}
                    </button>
                    <button class="mdo-btn" type="button" @click="openEdit(t)">
                      编辑
                    </button>
                    <button class="mdo-btn mdo-btn--danger" type="button" @click="removeTodo(t.id)">
                      删除
                    </button>
                  </div>
                </td>
              </tr>
              <tr v-if="visibleTodos.length === 0">
                <td colspan="6" style="padding: 18px; color: rgba(226,232,240,0.65)">
                  没有匹配的任务
                </td>
              </tr>
            </tbody>
          </table>
        </div>

        <div class="mdo-bottom-actions">
          <button class="mdo-btn mdo-btn--primary" type="button" @click="filter = 'active'">
            运行（显示进行中）
          </button>
          <button class="mdo-btn" type="button" @click="filter = 'overdue'">
            故障（显示超期）
          </button>
          <button class="mdo-btn" type="button" @click="filter = 'completed'">
            关机（显示已完成）
          </button>
          <button class="mdo-btn mdo-btn--danger" type="button" @click="clearCompleted">
            停止并清理
          </button>
        </div>
      </div>
    </section>
  </div>

  <!-- Toast -->
  <div v-if="toast" class="mdo-modal-backdrop" style="background: transparent; place-items: end;">
    <div class="mdo-modal mdo-glass mdo-neon" style="width: min(520px, 100%); padding: 14px 16px">
      <div style="font-weight: 800; margin-bottom: 6px">{{ toast.title }}</div>
      <div style="font-size: 12px; color: rgba(226,232,240,0.75)">{{ toast.detail }}</div>
    </div>
  </div>

  <!-- Edit modal -->
  <div v-if="editing" class="mdo-modal-backdrop" @click.self="editing = null">
    <div class="mdo-modal mdo-glass mdo-neon">
      <h3>编辑任务</h3>
      <div style="display: grid; gap: 10px">
        <div style="display: grid; gap: 6px">
          <label style="font-size: 12px; color: rgba(226,232,240,0.7)">任务名称</label>
          <input class="mdo-input" v-model.trim="editingDraft.title" />
        </div>

        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px">
          <div style="display: grid; gap: 6px">
            <label style="font-size: 12px; color: rgba(226,232,240,0.7)">优先级</label>
            <select class="mdo-select" v-model="editingDraft.priority">
              <option value="低">低</option>
              <option value="中">中</option>
              <option value="高">高</option>
            </select>
          </div>
          <div style="display: grid; gap: 6px">
            <label style="font-size: 12px; color: rgba(226,232,240,0.7)">截止时间</label>
            <input class="mdo-input" type="date" v-model="editingDraft.due" />
          </div>
        </div>
      </div>

      <div class="mdo-modal-actions">
        <button class="mdo-btn" type="button" @click="editing = null">取消</button>
        <button class="mdo-btn mdo-btn--primary" type="button" @click="saveEdit">保存</button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, onBeforeUnmount, reactive, ref } from "vue";

type Priority = "低" | "中" | "高";
type Filter = "all" | "active" | "completed" | "overdue";

type Todo = {
  id: string;
  title: string;
  priority: Priority;
  due: string; // YYYY-MM-DD
  done: boolean;
  createdAt: number;
};

type Toast = { title: string; detail: string } | null;

const now = ref(new Date());
let timer: number | undefined;
timer = window.setInterval(() => {
  now.value = new Date();
}, 1000);

onBeforeUnmount(() => {
  if (timer) window.clearInterval(timer);
});

const filter = ref<Filter>("all");
const search = ref("");

const todayYmd = computed(() => {
  const d = now.value;
  const yyyy = d.getFullYear();
  const mm = String(d.getMonth() + 1).padStart(2, "0");
  const dd = String(d.getDate()).padStart(2, "0");
  return `${yyyy}-${mm}-${dd}`;
});

const parseDue = (due: string) => {
  // 将 YYYY-MM-DD 转换到当天结束时间，避免时区造成的误判
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

const todoStatusClass = (t: Todo) => {
  if (t.done) return "mdo-badge--online";
  if (isOverdue(t)) return "mdo-badge--offline";
  return "mdo-badge--active";
};

const makeId = () => {
  if (typeof crypto !== "undefined" && "randomUUID" in crypto) return crypto.randomUUID();
  return `t_${Date.now()}_${Math.random().toString(16).slice(2)}`;
};

const defaultDue = () => {
  // 默认截止时间：今天 + 1 天
  const d = new Date();
  d.setDate(d.getDate() + 1);
  const yyyy = d.getFullYear();
  const mm = String(d.getMonth() + 1).padStart(2, "0");
  const dd = String(d.getDate()).padStart(2, "0");
  return `${yyyy}-${mm}-${dd}`;
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
    due: todayYmd.value,
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
      const yyyy = d.getFullYear();
      const mm = String(d.getMonth() + 1).padStart(2, "0");
      const dd = String(d.getDate()).padStart(2, "0");
      return `${yyyy}-${mm}-${dd}`;
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

const draft = reactive<{ title: string; priority: Priority; due: string }>({
  title: "",
  priority: "中",
  due: defaultDue(),
});

const addTodo = () => {
  const title = draft.title.trim();
  if (!title) {
    showToast("无法添加：请输入任务名称", "至少填写任务标题");
    return;
  }
  const due = draft.due;
  const t: Todo = {
    id: makeId(),
    title,
    priority: draft.priority,
    due,
    done: false,
    createdAt: Date.now(),
  };
  todos.value.unshift(t);
  draft.title = "";
  draft.priority = "中";
  draft.due = defaultDue();
  showToast("已添加任务", `任务：${t.title}`);
};

const filteredBySearch = computed(() => {
  const q = search.value.trim().toLowerCase();
  if (!q) return todos.value;
  return todos.value.filter((t) => (t.title + t.priority + t.due).toLowerCase().includes(q));
});

const filteredByMode = computed(() => {
  const mode = filter.value;
  if (mode === "all") return filteredBySearch.value;
  if (mode === "active")
    return filteredBySearch.value.filter((t) => !t.done);
  if (mode === "completed")
    return filteredBySearch.value.filter((t) => t.done);
  return filteredBySearch.value.filter((t) => isOverdue(t));
});

const visibleTodos = computed(() => {
  const list = [...filteredByMode.value];
  list.sort((a, b) => {
    // 超期优先，其次按截止时间升序
    const ao = isOverdue(a) ? 0 : 1;
    const bo = isOverdue(b) ? 0 : 1;
    if (ao !== bo) return ao - bo;
    const ad = parseDue(a.due)?.getTime() ?? Number.POSITIVE_INFINITY;
    const bd = parseDue(b.due)?.getTime() ?? Number.POSITIVE_INFINITY;
    return ad - bd;
  });
  return list;
});

const toggleDone = (id: string) => {
  const t = todos.value.find((x) => x.id === id);
  if (!t) return;
  t.done = !t.done;
  showToast(t.done ? "标记完成" : "已撤销完成", t.title);
};

const removeTodo = (id: string) => {
  const t = todos.value.find((x) => x.id === id);
  todos.value = todos.value.filter((x) => x.id !== id);
  if (t) showToast("已删除任务", t.title);
};

const markAllActiveDone = () => {
  const active = todos.value.filter((t) => !t.done);
  if (active.length === 0) {
    showToast("无需操作", "当前没有进行中的任务");
    return;
  }
  todos.value = todos.value.map((t) => (t.done ? t : { ...t, done: true }));
  showToast("一键完成成功", `完成 ${active.length} 条任务`);
};

const clearCompleted = () => {
  const completed = todos.value.filter((t) => t.done).length;
  if (completed === 0) {
    showToast("无需清理", "没有已完成任务可清空");
    return;
  }
  todos.value = todos.value.filter((t) => !t.done);
  showToast("清空完成", `删除已完成 ${completed} 条任务`);
};

const editing = ref<Todo | null>(null);
const editingDraft = reactive<{ title: string; priority: Priority; due: string }>({
  title: "",
  priority: "中",
  due: defaultDue(),
});

const openEdit = (t: Todo) => {
  editing.value = t;
  editingDraft.title = t.title;
  editingDraft.priority = t.priority;
  editingDraft.due = t.due;
};

const saveEdit = () => {
  if (!editing.value) return;
  const title = editingDraft.title.trim();
  if (!title) {
    showToast("无法保存：标题不能为空", "请输入任务名称");
    return;
  }
  editing.value.title = title;
  editing.value.priority = editingDraft.priority;
  editing.value.due = editingDraft.due;
  editing.value = null;
  showToast("已保存修改", title);
};

const toast = ref<Toast>(null);
let toastTimer: number | undefined;

const showToast = (title: string, detail: string) => {
  toast.value = { title, detail };
  if (toastTimer) window.clearTimeout(toastTimer);
  toastTimer = window.setTimeout(() => {
    toast.value = null;
  }, 1800);
};

const formatTime = (d: Date) => {
  const hh = String(d.getHours()).padStart(2, "0");
  const mm = String(d.getMinutes()).padStart(2, "0");
  return `${hh}:${mm}`;
};

const formatDue = (due: string) => {
  if (!due) return "—";
  return due;
};
</script>

