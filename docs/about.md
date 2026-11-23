\# 关于本项目



\## 项目目标



实现从 Markdown 文档到公开网站的自动化发布：只要把 Markdown 文件推送到仓库的 main 分支，网站内容就会自动更新，无需手动构建和上传。



\## 技术组成



| 工具 | 作用 |

| --- | --- |

| Markdown | 编写文档内容 |

| MkDocs Material | 将文档构建成静态网站 |

| GitHub Actions | 自动执行安装、构建和部署 |

| GitHub Pages | 托管并提供网站访问地址 |

| Git | 版本控制，记录每次文档变更 |



\## 各工具之间的关系



文档的流转路径是这样的：

Markdown 源文件 → MkDocs 构建 → 静态 HTML → GitHub Pages 托管 → 浏览器访问

↑

GitHub Actions 自动触发

