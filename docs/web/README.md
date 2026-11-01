# 粤策哨 · 静态演示版（web/）

面向粤东西北县域强村公司与合作社的**申报前自查辅助工具**静态 demo：输入帮扶项目描述（或点击预置案例一键填入），查看四段式体检报告。

> **免责声明（产品级，随报告强制展示）**：本报告仅为申报前自查辅助参考，不构成合规判定，请以政策法规原文及主管部门意见为准。本演示不输出"该项目合规 / 可以申报"类结论。

## 目录结构

```
web/
├── index.html      # 单页应用：首页输入 + 预置案例 + 四段式报告
├── css/style.css   # 样式（红头文件×岭南纸墨风格；系统字体，无外部资源）
├── js/data.js      # 预置案例与报告数据（全部文号/条款/日期来自知识库真实条目）
├── js/app.js       # 交互逻辑（纯前端，无请求）
└── README.md       # 本文件
```

## 本地运行

**零依赖，双击即开**：

1. 直接双击 `index.html`，用任意现代浏览器打开即可（Chrome / Edge / Safari / Firefox）；
2. 或在本目录起一个静态服务器：

```bash
cd web
python3 -m http.server 8080
# 浏览器打开 http://localhost:8080
```

本页面为纯静态 HTML/CSS/JS：无后端、无 API 密钥、无外部 CDN 依赖，断网也能完整使用；所有内容均不出本机。

## 使用方式

1. 在「项目描述」输入框粘贴帮扶项目描述，点击 **开始体检**；
2. 或点击下方 **预置案例卡片**（产业园基础配套类 / 设备购置类 / 人居环境类各一条），描述会自动填入输入框，再点「开始体检」；
3. 报告为四段式：
   - **① 适用政策清单**——含条款时效状态（现行有效 / 申报已截止 / 已失效供参考）；
   - **② 负面清单命中提示**——两级标注（规则命中 / 疑似命中·建议对照原文），每条附依据文号＋条款号＋日期；
   - **③ 材料缺项清单**；
   - **④ 修改建议**（建议口吻，不做判定）。

输入未匹配预置案例的内容时，页面只提示"演示版未内置该描述的报告"，不会生成任何未经编排的报告。

## 数据来源与红线

- 预置案例取自团队工作区 `04_政策语料/审计公告/案例标注表.csv` 的「改编后申报场景描述」列（案例 C139、C83、C02），均已复核；
- 全部文号、条款号、日期来自 `04_政策语料/00_工作区/registry.json` 与 `04_政策语料/政策文件清单-初稿.csv` 真实条目，条款原文摘录自 `04_政策语料/03_清洗文本/` 对应文件（逐字摘录），**未编造任何文号与日期**；
- 原文载明施行条款的按原文标注施行日期；未载明的按 registry「发布日期」标注印发日期，不作推断；
- 政策语料原文不入本仓库，`web/js/data.js` 仅保留必要短摘录；
- 页面固定展示免责声明，全文无"合规 / 可以申报"类结论性表述。

## GitHub Pages 部署

仓库结构下 `web/` 不是 Pages 默认根目录，二选一：

### 方式一（推荐）：GitHub Actions 发布 `web/` 目录

1. 在仓库根目录新建 `.github/workflows/pages.yml`：

```yaml
name: Deploy web demo to GitHub Pages
on:
  push:
    branches: [main]
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: true
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./web
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

2. 仓库 **Settings → Pages → Build and deployment → Source** 选择 **GitHub Actions**；
3. 该 workflow 合并进 `main` 后自动构建，站点地址形如 `https://<owner>.github.io/yueceshao/`。

### 方式二：发布仓库根目录（含 web/ 子路径）

**Settings → Pages → Source → Deploy from a branch → main / (root)**，站点上通过 `https://<owner>.github.io/yueceshao/web/` 访问本 demo。

> 提示：静态页面用相对路径引用资源，两种方式均无需修改任何代码。以上 workflow 仅为部署说明文档，是否落地由 owner 决定。
