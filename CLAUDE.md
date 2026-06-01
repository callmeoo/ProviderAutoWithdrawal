# CLAUDE.md · 中间服务商提现优化原型

面向维护本原型的 AI / 开发者。先读这份，再动代码。

## 项目性质

需求文档**交互原型**，不是生产代码。纯静态 HTML，Vue 3 global build + Tailwind CDN + marked.js + lucide，全部 CDN，无构建、无测试、无依赖安装。改完直接刷新浏览器看。

## 文件地图

- `index.html` — 主壳。整份需求文档存为一段 JS 模板字符串 `MD`，用 `marked.parse()` 渲染。左侧导航 + 顶部 tab 切换章节。
- `{pc-home,pc,mobile,approval}.html` — 四个界面原型，各自是**独立 Vue 实例**，通过 index.html 的 `<iframe v-show>` 嵌入（始终在 DOM，靠 v-show 切换）。
- `common.css` — 公共样式，被所有页面共享。改它会**同时影响所有 iframe**，慎改全局规则。
- `中间服务商结算明单.xlsx` — 附件1 数据，index.html 内有下载链接，文件名勿改。

## 改 index.html 需求文档时的关键约定（容易踩）

1. **章节标题是切片锚点**。`setup()` 里用 `MD.indexOf('## 二、')`、`MD.indexOf('## 7、上线初始化设计')`、以及 `funcTabs` 的 `mark/next`（如 `'## 1、'`→`'## 2、'`）把全文切成 tab 段。**改动 `##` 级标题文案前，先确认它是不是某个 `indexOf` 的锚点**——改错锚点会导致对应 tab 渲染空白或串段。
2. **Tailwind Preflight 把 `ol/ul` 的 `list-style` 重置为 none**，markdown 列表的「1. 2. 3.」「- 」序号/圆点**不会渲染**。需要可见编号时，把「N、」**写进文本里**（如 `**1、中间服务商 自动提现相关**`），不要依赖 markdown 列表标记。
3. `.md-body table` 已有专用样式（见 index.html `<style>`），表格用标准 markdown 写即可；车牌等 CJK+字母 token 靠 `word-break: keep-all` 防止断行。

## 需求口径红线

- **失败通知对象**统一为：**对应服务商的区域经理 + 客服（闫珂、潘彩欣）**，多个区域经理全部通知（口径见功能点 8.1 / 验收 13.6 / 待确认 14.3，附件1 视图已对齐）。
- 需求内容的增删改一律以需求方明确指令为准，不自行"优化"业务规则。

## 预览

`python3 -m http.server 8000`，访问 `http://localhost:8000/index.html`。
