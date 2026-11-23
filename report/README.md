\# 实践项目2：个人实验报告



\## 1. 基本信息



\- GitHub 登录名：L-Y-LEARN

\- 个人仓库地址：https://github.com/L-Y-LEARN/my-docs

\- 最终网站地址：https://L-Y-LEARN.github.io/my-docs/



\## 2. 实践过程与关键截图



\### 2.1 仓库创建



\*\*做了什么\*\*：登录 GitHub 后点击右上角「+」→「New repository」，仓库名填写 `my-docs`，可见性选择 Public，勾选「Add a README file」初始化 main 分支，点击 Create repository 完成创建。随后在本地项目目录执行 `git clone` 将仓库克隆到 `C:\\Users\\刘洋\\Desktop\\my-docs`。



\*\*为什么这样做\*\*：仓库必须设为 Public，因为 GitHub Pages 的免费静态站点托管仅对公开仓库提供；勾选初始化 README 是为了让远程仓库带有 main 分支，避免后续推送时出现「远程分支不存在」的问题。



\*\*结果\*\*：仓库创建成功，克隆到本地后执行 `git status` 显示 `On branch main` 且 `Your branch is up to date with 'origin/main'`，说明本地与远程仓库关联正常。



!\[个人仓库](images/01-repository.png)



\### 2.2 项目结构与 MkDocs 配置



\*\*做了什么\*\*：在项目中创建 `docs/` 目录，编写四个 Markdown 页面：`index.md`（首页，含指向其余三页的内部链接）、`about.md`（含技术组成表格与方案对比表格）、`guide.md`（使用指南，含有序列表与代码块）、`api.md`（API 参考，含 JSON 代码块）。随后创建 `mkdocs.yml`，配置站点名称、描述、`theme: material`、`nav` 导航结构与 `site\_url`。同时创建 `requirements.txt` 记录依赖，创建 `.gitignore` 写入 `site/`、`.venv/`、`\_\_pycache\_\_/` 三条忽略规则。



\*\*为什么这样做\*\*：`docs/` 是 MkDocs 约定的源文件目录，是文档内容的唯一来源；`nav` 字段决定导航栏的结构与显示顺序，其路径相对于 `docs\_dir` 计算，因此写 `index.md` 而非 `docs/index.md`，写错会导致构建时找不到文件；`.gitignore` 忽略 `site/` 是因为它是构建产物、可随时重新生成，忽略 `.venv/` 是因为虚拟环境体积大且与本机绑定，两者都不应进入版本库。



\*\*结果\*\*：目录结构完整，`mkdocs.yml` 配置通过后续 `--strict` 严格模式校验，无任何警告。



!\[项目结构与配置](images/02-structure-config.png)



\### 2.3 本地构建或预览



\*\*做了什么\*\*：先执行 `python -m venv .venv` 创建虚拟环境，用 `source .venv/Scripts/activate` 激活（提示符出现 `(.venv)`），再执行 `python -m pip install -r requirements.txt` 安装依赖。然后执行 `python -m mkdocs build --strict` 构建站点，最后执行 `python -m mkdocs serve` 启动本地预览服务。



\*\*为什么这样做\*\*：使用虚拟环境可将本项目依赖与系统全局 Python 环境隔离，避免多个项目间包版本冲突；`--strict` 参数让任何警告都视为错误，能提前暴露链接失效、配置不规范等问题；本地预览可在推送前确认渲染效果，避免把错误发布到线上。



\*\*结果\*\*：构建输出 `Documentation built in 1.66 seconds`，无警告无报错。本地服务地址为 `http://127.0.0.1:8000/my-docs/`，浏览器中四个页面均正常渲染。检查完毕后按 Ctrl+C 停止服务，终端输出 `Shutting down...`。



!\[本地构建与预览](images/03-local-build.png)



\### 2.4 Pages 设置



\*\*做了什么\*\*：进入仓库 Settings → Pages → Build and deployment，将 Source 由默认的 `Deploy from a branch` 改为 `GitHub Actions`。随后进入 Settings → Actions → General，确认 Actions 权限为 `Allow all actions and reusable workflows`。



\*\*为什么这样做\*\*：默认的 `Deploy from a branch` 模式会读取指定分支中的静态文件进行发布，而本方案的 `site/` 已被 `.gitignore` 忽略、从未提交到 main 分支，该模式下 GitHub 找不到可发布文件，必然失败。改为 `GitHub Actions` 后由工作流构建并主动发布。Actions 权限保持允许状态以保证工作流可执行，但部署权限由工作流内 `permissions` 字段单独声明，因此无需修改仓库全局默认权限，也未勾选「Allow GitHub Actions to create and approve pull requests」。



\*\*结果\*\*：Source 成功切换为 GitHub Actions，Actions 权限确认无需改动。



!\[Pages 设置](images/04-pages-setting.png)



\### 2.5 Actions 首次构建和部署



\*\*做了什么\*\*：创建 `.github/workflows/deploy.yml`，定义 `build` 与 `deploy` 两个串联 job。build 依次执行检出代码、安装 Python 3.12、安装依赖、`mkdocs build --strict`、上传 `site` 目录为构建产物；deploy 依赖 build 完成后，通过 `actions/deploy-pages` 将产物发布到 GitHub Pages。触发条件为推送到 main 分支，并配置 `workflow\_dispatch` 支持手动触发。



\*\*为什么这样做\*\*：拆分为两个 job 可让「构建」与「发布」职责分离，构建失败时不会执行发布，避免把错误站点推到线上；`permissions` 中声明 `contents: read`、`pages: write`、`id-token: write` 三项，遵循最小权限原则；配置 `paths` 过滤可避免修改无关文件时触发无意义的构建。



\*\*结果\*\*：首次推送后工作流自动触发，build 与 deploy 两个 job 均显示绿色成功状态。



!\[Actions 首次部署](images/05-actions-first-run.png)



\### 2.6 网站访问与四页导航检查



\*\*做了什么\*\*：从 Settings → Pages 页面获取实际站点地址 `https://L-Y-LEARN.github.io/my-docs/`，在浏览器打开后逐一检查四个页面。



\*\*为什么这样做\*\*：线上环境的路径前缀、资源引用方式与本地不同，必须实际访问验证，不能仅凭本地预览通过就认为部署成功。



\*\*结果\*\*：网站正常加载，地址栏显示完整项目路径。四页导航检查情况：首页正常显示并呈现三个页面链接；「关于」页两个表格均渲染为表格样式，未出现 Markdown 竖线符号；「使用指南」页有序列表与代码块正常；「API 参考」页 JSON 代码块语法高亮正常。顶部导航栏四个页面可自由切换，首页三个内部链接点击后均正确跳转，无 404。浏览器控制台无静态资源加载报错。



!\[网站访问效果](images/06-website-online.png)



\### 2.7 第二次推送与自动更新验证



\*\*做了什么\*\*：修改 `docs/guide.md` 中的部分内容，执行 `git add docs/guide.md`、`git commit -m "docs: 更新使用指南内容"`、`git push origin main`，随后观察 Actions 页面是否自动触发新一轮运行。



\*\*为什么这样做\*\*：验证「推送即自动部署」这一核心能力是否真正生效，确认工作流不是只在首次运行时有效。



\*\*结果\*\*：推送后 Actions 自动触发第二次运行，运行记录中显示对应的 commit 信息，构建与部署均成功。刷新线上网站，修改后的内容已同步显示，证明自动化链路完整可用。



\- 对应提交链接：https://github.com/L-Y-LEARN/my-docs/commits/main

\- 对应 Actions 运行链接：https://github.com/L-Y-LEARN/my-docs/actions



!\[第二次自动部署](images/07-second-deploy.png)



\## 3. 问题与解决



\### 问题1：Actions 页面为空，未识别到任何工作流



\*\*现象\*\*：完成 `deploy.yml` 编写后打开仓库 Actions 页面，页面显示「Get started with GitHub Actions」并推荐模板，左侧没有任何工作流记录，说明 GitHub 未识别到工作流文件。



\*\*关键线索\*\*：执行 `git status`，输出中同时出现两段信息：

On branch main

Your branch is up to date with 'origin/main'.

Changes to be committed:

new file: .github/workflows/deploy.yml

...



\*\*原因分析\*\*：`up to date with 'origin/main'` 表示本地与远程完全一致，但下方又有 8 个文件处于「待提交」状态，两者结合说明文件只执行了 `git add` 进入暂存区，尚未 `git commit` 和 `git push`，远程仓库中实际不存在 `deploy.yml`，因此 Actions 无从触发。



\*\*解决操作\*\*：补执行 `git commit -m "搭建MkDocs文档并配置Pages自动部署"` 与 `git push origin main`，确认输出中出现 `main -> main` 后刷新 Actions 页面，工作流正常识别并自动开始运行。



\### 问题2：`git diff --staged` 输出过长导致终端无法输入



\*\*现象\*\*：执行 `git diff --staged` 后终端逐行显示 `deploy.yml` 完整内容，底部停留在 `byte 813`，无法继续输入命令，一度误以为是程序卡死或报错。



\*\*原因分析\*\*：这是 Git 调用 `less` 分页器的正常行为，并非错误。输出内容超过一屏时会自动进入分页浏览模式。



\*\*解决操作\*\*：按 `q` 退出分页器恢复正常输入。后续改用 `git diff --staged --name-only` 仅列出文件名，既避免刷屏，又能快速核对暂存内容是否包含 `site/` 或 `.venv/`。



\### 问题3：本地预览地址带有 `/my-docs/` 路径



\*\*现象\*\*：`mkdocs serve` 提示的地址是 `http://127.0.0.1:8000/my-docs/`，与教程中描述的 `http://127.0.0.1:8000/` 不一致，怀疑配置有误。



\*\*原因分析\*\*：`mkdocs.yml` 中配置的 `site\_url` 带有 `/my-docs/` 路径前缀，本地开发服务器会遵循该配置生成子路径，这是预期行为。



\*\*解决操作\*\*：无需修改，直接访问终端提示的完整地址即可。该配置与线上 Pages 的项目路径结构保持一致，反而有利于本地与线上表现统一。



\### 核对过的配置与日志



除上述问题外，还主动核对了以下内容：`git status` 确认 `site/` 与 `.venv/` 未出现在待提交列表中，验证 `.gitignore` 生效；`ls .github/workflows` 确认文件名是 `deploy.yml` 而非 `deploy.yml.txt`，排除 Windows 隐藏扩展名导致的识别失败；核对 `deploy.yml` 中 `permissions` 三项权限完整，避免 deploy 阶段因权限不足失败。



\## 4. 原理理解



\### 4.1 docs/ 和 site/ 各保存什么？为什么不用手工修改 site/？



`docs/` 保存人工编写的 Markdown 源文件，是文档内容的唯一真实来源，所有修改都在这里进行。`site/` 保存 `mkdocs build` 生成的完整静态网站，包含 HTML、CSS、JS 等浏览器可直接使用的文件。



不需要手工修改 `site/`，原因有三：一是它是派生产物，每次执行构建都会被清空重建，手工改动会立刻丢失；二是它已在 `.gitignore` 中忽略，改动不会进入版本库，也无法同步到线上；三是本方案由 GitHub Actions 在云端重新构建，本地 `site/` 目录甚至可以不保留。正确的做法是只改 `docs/`，让工具自动生成 `site/`。



\### 4.2 本次工作流在什么情况下自动运行？



在两种情况下运行：一是向 main 分支推送代码，且改动涉及 `paths` 过滤规则中列出的路径（`docs/\*\*`、`mkdocs.yml`、`requirements.txt`、`.github/workflows/deploy.yml`）；二是在 Actions 页面手动点击「Run workflow」按钮，由 `workflow\_dispatch` 触发。推送到其他分支、或只修改 `report/`、`README.md` 等未列入 `paths` 的文件，都不会触发运行。



\### 4.3 build 与 deploy 两个任务有什么区别？



`build` 负责「生产」：检出源码、准备 Python 环境、安装依赖、执行 `mkdocs build --strict` 生成静态文件，并通过 `actions/upload-pages-artifact` 把 `site/` 目录上传为构建产物。它不接触线上环境。



`deploy` 负责「发布」：通过 `needs: build` 声明依赖，必须等 build 成功后才执行，使用 `actions/deploy-pages` 把已上传的产物发布到 GitHub Pages。



拆分的价值在于职责分离与失败隔离：构建阶段出错时不会执行发布，线上站点保持原样不受影响；同时 deploy 使用 `environment: github-pages`，可获得 GitHub 提供的环境保护与部署记录。



\### 4.4 只修改 report/README.md 会触发部署吗？为什么？



不会。工作流的 `on.push.paths` 只列出了 `docs/\*\*`、`mkdocs.yml`、`requirements.txt` 和 `.github/workflows/deploy.yml`，`report/README.md` 与根目录 `README.md` 都不在其中，因此推送这类改动时 GitHub 不会触发工作流。



这是有意设计的路径过滤：作业报告、仓库说明等文件不参与网站构建，修改它们无需重新构建和发布站点，跳过可节省执行时间、避免产生无意义的部署记录。



\### 4.5 本次方案为何无需创建 gh-pages 分支？



早期 MkDocs 部署方案（如 `mkdocs gh-deploy` 命令）的做法是把构建产物推送到一个名为 `gh-pages` 的分支，再由 GitHub Pages 从该分支读取发布，因此需要额外维护一个分支。



本方案改用 GitHub Actions 的官方 Pages 部署机制：构建产物通过 `actions/upload-pages-artifact` 上传为 Actions 的临时制品，再由 `actions/deploy-pages` 直接发布到 Pages 服务。整个过程产物不写入任何分支，仓库中始终只有 main 一个分支，既避免了产物污染版本历史，也不需要处理分支同步问题。这也是 Pages 来源必须设置为 `GitHub Actions` 而非 `Deploy from a branch` 的原因。

