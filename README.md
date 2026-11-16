# 我的中文报告（bookdown 项目）

这是一个用 [**bookdown**](https://bookdown.org) 写中文书/报告的项目。
它把一堆普通的 Markdown 文件（`.Rmd`，其实是 Markdown + 少量 R 代码块）编译成一个带目录的网站、一个 PDF、一个 EPUB。

**你只需要写 `.Rmd` 文件，格式和排版由模板负责。**

---

## 一、文件结构（哪些该改，哪些别动）

```
my-book/
├── index.Rmd            ← 封面：标题、作者、前言、致谢（要改）
├── 01-introduction.Rmd  ← 第 1 章（改成你的内容）
├── 02-foo.Rmd           ← 第 2 章（改成你的内容）
├── 07-hmm.Rmd           ← 附录（用不上就删掉）
├── 08-references.Rmd    ← 参考文献（要引用文献才用）
├── book.bib             ← 参考文献条目（BibTeX 格式）
├── _bookdown.yml        ← 章节目录顺序、输出目录（一般不用动）
├── _output.yml          ← 网页/PDF 样式配置（一般不用动）
├── css/style.css        ← 网页样式（想换配色改这里）
├── latex/               ← PDF 用的 LaTeX 模板（做 PDF 才需要）
└── .github/workflows/build-book.yaml  ← 自动编译脚本（别动）
```

**章节顺序 = 文件名开头的数字顺序**（`index.Rmd` 永远排第一）。想加一章就新建 `03-你的标题.Rmd`，文件名必须以数字开头且补零。

---

## 二、最省事的用法：不装任何软件，全靠网页

> 编译（把 `.Rmd` 变成网页）交给 GitHub 的服务器做，你只负责改文字。改完提交，等 2 分钟，刷新网页就能看到新版。

### 第 1 步：注册 / 登录 GitHub

浏览器打开 <https://github.com> ，没有账号点 **Sign up** 注册（邮箱 + 密码 + 验证码，免费）。

### 第 2 步：建一个新仓库（Repository）

1. 右上角 **+** → **New repository**
2. **Repository name** 填 `my-book`（名字随意，建议全用英文小写，不要空格和中文）
3. 选 **Public**（公开；GitHub Pages 免费账号必须公开仓库才能发布）
4. **不要**勾选 "Add a README file"（我们后面自己传）
5. 点 **Create repository**

建好后页面地址就是你的仓库：

```
https://github.com/你的用户名/my-book
```

这就是"打开 GitHub 仓库"的意思——用浏览器访问这个网址就是打开它。

### 第 3 步：把项目文件传上去

在仓库页面点 **Add file → Upload files**，把本项目文件夹里的**所有文件和文件夹**拖进去
（`.github`、`css`、`latex` 这三个文件夹也要一起拖），然后点 **Commit changes**。

> 注意：Windows 默认隐藏 `.github` 文件夹。在文件资源管理器里勾上「查看 → 显示 → 隐藏的项目」才看得到。
> 如果拖拽时漏了 `.github/workflows/build-book.yaml`，自动编译就不会启动，后面 Pages 会一直空白。

### 第 4 步：改掉两处占位符

在仓库里点开 `_output.yml` 和 `index.Rmd`，点右上角铅笔图标编辑：

- `index.Rmd` 里的 `author: "你的名字"`、`title: "我的报告"`、`github-repo: YOUR_GITHUB_USERNAME/my-book`
- `_output.yml` 里的 `YOUR_GITHUB_USERNAME`

改成你自己的 GitHub 用户名（不改也能编译，只是"编辑此页"链接会 404）。

### 第 5 步：打开 Pages

1. 仓库页面上方 **Settings**
2. 左侧栏 **Pages**
3. **Build and deployment → Source** 选 **GitHub Actions**（不要选 "Deploy from a branch"）

### 第 6 步：等它编译，然后打开你的书

1. 点仓库页面上方 **Actions** 标签，能看到一条叫「编译并发布我的书」的任务在跑
2. 等它变成 ✅ 绿色对勾（第一次约 2–4 分钟，要装 R 和 bookdown 包）
3. 回到 **Settings → Pages**，顶部会显示：

```
Your site is live at https://你的用户名.github.io/my-book/
```

**这个网址就是"应用模板"后的成品**——用浏览器打开，看到的就是一本带侧边目录的中文电子书。手机上也能看，可以直接发给别人。

以后每次改完 `commit`，它都会自动重新编译、自动更新这个网址。

---

## 三、本地预览（可选，但要边写边看就得装）

不想每次都等 GitHub 编译，可以本机装。

**你机器上的现状（已确认）：** R 4.5.1 装在 `D:\R-4.5.1`，RStudio 也装了，`knitr` 和 `rmarkdown` 有了，**只差 `bookdown` 这一个包**。

1. 打开 **RStudio** → `File → Open Project` → 选本文件夹里的 `bookdown-chinese.Rproj`
2. 在 Console（左下角）里跑一次，**用国内镜像**（直连 CRAN 国内经常失败）：

```r
install.packages("bookdown", repos = "https://mirrors.tuna.tsinghua.edu.cn/CRAN/")
```

   如果报"无法访问"，再试这几个镜像，任选一个能通的：

```r
# 中科大
install.packages("bookdown", repos = "https://mirrors.ustc.edu.cn/CRAN/")
# 阿里云
install.packages("bookdown", repos = "https://mirrors.aliyun.com/CRAN/")
```

3. 点编辑器上方的 **Build Book** 按钮（或 `Build` 面板 → **Build Book**）
4. 生成结果在 `docs/` 文件夹，双击 `docs/index.html` 预览

一次装好，以后点 Build Book 就行。Pandoc 不用单独装，RStudio 自带。

---

## 四、最常用的 Markdown 写法

```markdown
# 一级标题        ← 在 bookdown 里等于"新的一章"
## 二级标题       ← 一节
### 三级标题      ← 一小节

**加粗**  *斜体*  `行内代码`

- 列表项
- 列表项

1. 有序
2. 列表

> 引用

[链接文字](https://example.com)

![图片说明](images/pic.png)

| 表头 | 表头 |
|------|------|
| 内容 | 内容 |
```

**公式**（LaTeX 语法，网页和 PDF 都能渲染）：

```markdown
行内公式 $E = mc^2$

独立公式：
$$
\frac{\partial u}{\partial t} = \alpha \nabla^2 u
$$
```

**代码块**（带语法高亮）：

````markdown
```python
print("hello")
```
````

**R 代码块会真的被执行**（跑出图、跑出表），如果只想展示代码不想执行，写成：

````markdown
```{r, eval=FALSE}
plot(cars)
```
````

---

## 五、常见问题

**Q：Actions 是红叉（失败）怎么办？**
点进失败的那一步看日志。最常见原因：`.github/workflows/build-book.yaml` 没上传成功、或某个 `.Rmd` 里有语法错误（比如代码块没闭合）。

**Q：Pages 显示 404？**
① 确认 Settings → Pages 的 Source 是 **GitHub Actions**；② 确认 Actions 里最后一次部署是成功的；③ 第一次部署后等 1–2 分钟再刷新。

**Q：想导出 PDF / Word？**
- PDF：需要本地装 R + LaTeX（TinyTeX），`bookdown::render_book("index.Rmd", "bookdown::pdf_book")`。
- Word：bookdown 不直接支持，可以先用 `pandoc` 把 `docs/` 里的单章转 Word，或直接用 Typora 的导出。

**Q：能不能不用 GitHub，本地就行？**
可以。装好 R 后本地 Build Book，结果在 `docs/`，双击 `docs/index.html` 就能看，整个 `docs` 文件夹拷给别人也能看。

---

## 六、这个模板的来源

改编自 <https://github.com/yihui/bookdown-chinese>（Yihui Xie 的中文 bookdown 示例，CC0 授权），
已做如下修改：输出目录改为 `docs`、添加 GitHub Actions 自动编译发布、清理指向原作者仓库的链接、封面与前言改为报告骨架。
