# mdo-todo-vue（墨刀风格待办事项）

基于 **Vue 3 + Vite**，并使用深蓝科幻 UI 还原你提供的墨刀原型风格。

## 本地运行

1. 安装 Node.js（建议 Node 18+）
2. 在项目目录运行：

```bash
npm install
npm run dev
```

访问地址：`http://localhost:5173`

## 部署到 Vercel

### 方式 A（推荐）：GitHub 一键部署

1. 在 GitHub 新建仓库并把本项目代码推送上去
2. 登录 Vercel（使用同一个 GitHub 账号）
3. 在 Vercel 中选择 **New Project**
4. 选择该仓库
5. 检查构建配置：
   - Framework：`Vite`
   - Build Command：`npm run build`
   - Output Directory：`dist`
6. 点击 **Deploy**
7. 部署完成后，Vercel 会给出一个唯一的访问网址（URL）

### 方式 B：命令行（需要 Vercel Token）

```bash
npm i -g vercel
vercel login
vercel
```

第一次会引导你选择项目/范围，后续会直接输出部署 URL。

## 说明

- 当前页面为本地状态示例：新增/完成/编辑/删除都只存在于浏览器运行时的状态中。
- 你后续如果需要把待办数据接到接口（REST/WebSocket/GraphQL），我可以帮你把数据层补齐。

