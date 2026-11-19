# Project Mentor · Windows 项目导师

面向个人的 Windows 10 桌面 Python 项目导师。接收你已选好的 GitHub 仓库和目标岗位，把本地代码分析、分步学习、验收证据、改造记录、模拟面试和真实简历草稿保存在本机。悬浮角色使用用户提供的参考图。

**当前验证结果以 [开发进度](docs/progress.md) 和 [逐项验收账本](docs/acceptance.md) 为准。** 没有用户密钥时不调用真实 API；协议模拟测试不能证明某家模型的真实连通性、视觉能力或回答质量。不要把“已实现”视为全部验收通过。

## 直接运行交付包

本机已生成 `dist/ProjectMentor/ProjectMentor.exe`（Windows10 x64目录版）。分发请复制整个 `ProjectMentor` 文件夹，或解压 `dist/ProjectMentor-Win10-x64.zip` 后运行其中EXE，不需要另装Python。首次打开在本地设置Key，也可不配置Key先导入项目使用本地功能。

最新源码自动回归100项通过，DPAPI另有真实实测记录；最终二进制完整交互尚未复核。真实MySQL实验与限制见 [测试报告](docs/test-report.md)，完整17项验收仍有未验证/网络失败项。

## 源码安装与启动

要求 Windows 10 22H2 x64、Python 3.11/3.12 x64、Git。推荐普通用户运行。当前开发机为 Windows 10 build 19045、隔离 Python 3.12.14；默认系统 Python 3.9 不用于本项目。安装自己的 Python 后，在项目根目录 PowerShell 执行：

```powershell
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements-lock.txt
.\.venv\Scripts\python.exe -m pip install --no-build-isolation --no-deps -e .
.\.venv\Scripts\python.exe -m project_mentor.app
```

如果没有 `py` 命令，用已安装的 Python 3.12 `python.exe` 的完整路径代替第一行的 `py -3.12`。不要覆盖其他项目的虚拟环境。本项目使用 [pyproject.toml](pyproject.toml) 中的明确依赖版本。现成 `.wheels` 目录可用时可离线安装：

```powershell
.\.venv\Scripts\python.exe -m pip install --no-index --find-links .wheels -e ".[dev]" --no-build-isolation
```

离线安装仍需要本地存在所有依赖和构建工具；缺失时根据 pip 提示补齐可信 wheel。`scripts/download_deps.py` 仅用于下载项目固定依赖，不运行导入仓库中的安装脚本。

## 首次使用

1. 首次提示后选择仓库存放工作区。没有 Key 也能使用本地分析、计划、证据、面试和报告。
2. 在“设置 / 成本”新增服务商配置，填写官方 Base URL 与模型 ID。Key 只在本地密码输入框录入并保存。不要把 Key 发到聊天、写进 `.env` 或提交 Git。
3. 逐项核对模型能力；所有模型默认只启用文本。不确定视觉、工具或结构化输出能力时保持关闭。模型 ID 可手工编辑，不依赖某一过时推荐列表。
4. “连接测试”会先请求你确认，发送极小文本请求，仍可能计费；记录在请求预算中。ChatGPT Plus 与各厂商 API 额度分开。
5. 在“项目”导入已选 GitHub URL 或本地目录，选择岗位；导入不执行 README 中的命令。按“为什么 / 操作 / 预期 / 验收”完成当前步骤。

详细流程见 [使用手册](docs/user-guide.md)，密钥与上传边界见 [隐私与安全](docs/privacy.md)，常见错误见 [故障排查](docs/troubleshooting.md)。

## 测试与构建

```powershell
.\.venv\Scripts\python.exe -m pytest -q
.\.venv\Scripts\python.exe -m compileall -q src
.\.venv\Scripts\python.exe -m project_mentor.app --smoke
powershell -ExecutionPolicy Bypass -File scripts/build.ps1
```

`--smoke` 只渲染应用自己的窗口为图片并退出，不采集桌面、不调用 API。`build.ps1` 先运行 pytest，再打包到 `dist/ProjectMentor/`。分发时复制整个目录；仅复制其中 EXE 会缺少 Qt/Python 动态库。构建成功与完整的 Win10 交互验收是两件事，具体实测状态见验收账本。

## 本地数据与卸载

默认 `%LOCALAPPDATA%\ProjectMentor`：`mentor.sqlite3` 学习记录、`secrets\*.dpapi` 当前 Windows 用户加密的密钥、`captures\` 截图、`mentor.log` 脱敏运行日志。环境变量 `PROJECT_MENTOR_DATA` 可指定另一数据目录；测试与日常数据应分开。数据库备份不包含截图文件或可迁移的密钥。

退出托盘后删除程序分发目录即可卸载；需要彻底清理时先导出报告/备份，再在应用中删除项目、清理截图、删除服务商密钥，最后自行删除数据目录。项目源仓库与应用记录分开，删除应用记录不会删除源码。密钥若曾泄露，应在服务商控制台撤销，单纯删除本地记录不会使已泄露 Key 失效。
