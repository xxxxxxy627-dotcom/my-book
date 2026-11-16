# 会后总结 · 任务汇报

用 **bookdown** 维护的汇报文集：每写一篇就多一章，编译成一个固定网址。
老师收藏一次网址，之后每次刷新都是最新内容。

```
网址（示例）：https://你的用户名.github.io/my-book/
```

---

## 一、文件结构

```
my-book/
├── index.Rmd                  ← 首页：标题、说明（改标题和作者）
├── 01-huiyi-zongjie.Rmd       ← 会后总结模板（复制它写新的）
├── 02-renwu-huibao.Rmd        ← 任务汇报模板（复制它写新的）
├── _bookdown.yml              ← 目录顺序、输出目录（不用动）
├── _output.yml                ← 网页样式配置（不用动）
├── css/style.css              ← 配色字体（想调再动）
├── latex/                     ← 导出 PDF 才用到
├── README.md                  ← 本文件
└── .github/workflows/build-book.yaml   ← 自动编译发布（别动）
```

**目录顺序 = 文件名开头的数字。** 编号不要重复，`index.Rmd` 永远排第一。

---

## 二、每次怎么写

### 写一篇新的会后总结

1. 复制 `01-huiyi-zongjie.Rmd` → 粘贴出副本
2. 改名为下一个编号，比如 `03-huiyi-zongjie.Rmd`
3. 打开改两处：
   - 标题那行的日期 `# 会后总结（2026-11-16 组会）`
   - 花括号里的锚点 `{#meeting-1}` → 改成不重复的，如 `{#meeting-1116}`
4. 往下填表格（会议信息、议题、决议、待办）

### 写一篇新的任务汇报

1. 复制 `02-renwu-huibao.Rmd` → 改名为 `04-renwu-huibao.Rmd`
2. 改标题的周次和锚点 `{#report-1}` → `{#report-12}`
3. 填「本期完成情况」表格和「下期计划」

> 文件开头的 `<!-- ... -->` 是给自己看的说明，编译后不会显示给老师，可以保留也可以删掉。

---

## 三、上传到 GitHub 生成网址（一次性设置）

### 1. 建仓库

<https://github.com> → 右上角 **+ → New repository** → 名字 `my-book` → 选 **Public** → 不勾 README → **Create repository**

### 2. 传文件

打开上传页（把用户名换成你自己的）：

```
https://github.com/你的用户名/my-book/upload/main
```

在 `my-book` 文件夹里 **Ctrl+A 全选 13 个项目**（先在资源管理器「查看 → 显示 → 隐藏的项目」里勾上，13 个里有 3 个文件夹：`.github`、`css`、`latex`），拖进浏览器上传区 → 底部 **Commit changes**。

### 3. 开 Pages

**Settings → Pages → Source 下拉 → 选 `GitHub Actions`**

### 4. 等编译

点 **Actions** 标签，等「编译并发布我的书」变成 ✅（首次 2–4 分钟）。

回到 **Settings → Pages**，顶部显示：

```
Your site is live at https://你的用户名.github.io/my-book/
```

---

## 四、老师怎么查阅

把上面这个网址发给他，他随时打开都是最新版。

**更新要多久？** 不是实时，是**提交后 1–2 分钟**自动更新：

- 你在网页上改完 `Commit changes`
- GitHub 服务器自动重编译（约 60–120 秒）
- 老师刷新页面就是新内容

页面上有「编辑」链接（配置在 `_output.yml` 里），点了会跳到 GitHub 的在线编辑器，**手机上也能改**。

### 如果需要「边写老师边看」的真正实时

GitHub Pages 做不到秒级同步。要真·实时的话用这些（都是 Markdown、都能发链接）：

| 工具 | 网址 | 特点 |
|---|---|---|
| HackMD | hackmd.io | Markdown 原生、协作实时、可设只读共享链接，最接近你的需求 |
| 语雀 | yuque.com | 国内访问快、支持 Markdown 粘贴、知识库分享 |
| 飞书文档 | feishu.cn | 团队协作、老师可能已有账号 |
| 腾讯文档 | docs.qq.com | 门槛最低 |

**建议**：日常写作用本项目的 GitHub 网址（正式归档、格式统一、永远一个链接）；如果老师要求**实时盯着进度**，把同样的内容同步到 HackMD 共享一篇。

---

## 五、本地预览（可选）

装了 R + `bookdown` 包之后，在 RStudio 里点 **Build Book** 可以在本地看效果。

```r
install.packages("bookdown", repos = "https://mirrors.tuna.tsinghua.edu.cn/CRAN/")
```

编译结果在 `docs/` 文件夹，双击 `docs/index.html` 即可预览。

不装也能写——直接用 GitHub 网页的在线编辑器改，提交后看线上效果。

---

## 六、常用 Markdown 写法

```markdown
# 一级标题     ← 等于"新的一章"
## 二级标题
### 三级标题

**加粗**  *斜体*  `代码`

- 无序列表
1. 有序列表

> 引用

[链接](https://example.com)
![图片说明](images/pic.png)

| 表头 | 表头 |
|:-----|:-----|
| 左对齐 | 内容 |

行内公式 $E=mc^2$

$$
\frac{\partial u}{\partial t} = \alpha \nabla^2 u
$$
```

表格里的 `|:-----|` 冒号控制对齐：`左` 冒号在左、`右` 冒号在右、`居中` 两边都有。

---

## 七、常见问题

**Q：Pages 显示 404？**
① Settings → Pages 的 Source 必须是 **GitHub Actions**；② Actions 里最后一次部署要成功；③ 首次部署后等 1–2 分钟。

**Q：Actions 红叉？**
点进失败那步看日志。最常见是 `.github/workflows/build-book.yaml` 没传上去，或某个 `.Rmd` 代码块没闭合（少一个 ```）。

**Q：想导出 Word 交给老师？**
浏览器打开线上页面，`Ctrl+P` → 另存为 PDF 最快。要真 Word 就用 Pandoc：
```
pandoc docs/index.html -o 汇报.docx
```

**Q：字体太小/太窄？**
改 `css/style.css`，比如加 `body { font-size: 16px; }`。

---

## 八、来源

改编自 <https://github.com/yihui/bookdown-chinese>（CC0）。
已改造：输出目录改为 `docs`、加入 GitHub Actions 自动编译发布、模板改为「会后总结 / 任务汇报」两类。
