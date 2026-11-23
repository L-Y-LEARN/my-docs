\# 我的技术文档



基于 MkDocs Material 构建的个人技术文档站点，通过 GitHub Actions 实现推送即自动部署，无需手动构建或上传静态文件。



\*\*在线访问\*\*：https://L-Y-LEARN.github.io/my-docs/



\*\*个人实验报告\*\*：\[个人实验报告](report/README.md)



\## 项目结构



| 路径 | 说明 |

| --- | --- |

| docs/ | 网站 Markdown 源文件 |

| mkdocs.yml | MkDocs 主配置，定义站点信息与导航 |

| requirements.txt | Python 依赖清单 |

| .github/workflows/deploy.yml | 自动构建与部署工作流 |

| report/ | 个人实验报告与过程截图 |



\## 本地预览



```bash

python -m venv .venv

source .venv/Scripts/activate

python -m pip install -r requirements.txt

python -m mkdocs serve

