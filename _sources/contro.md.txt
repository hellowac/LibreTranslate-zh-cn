以下是该段内容的完整翻译，保留了原有格式和说明：

---

# 贡献指南（Contributing）

如果你想对代码进行修改，可以从源码构建并运行 API。

## 构建依赖（Build Dependencies）

- cmake

### Debian / Ubuntu

```sh
sudo apt-get install cmake
```

### Fedora / RHEL

```sh
sudo dnf install cmake
```

---

## 快速开始（Getting Started）

安装 [`hatch`](https://hatch.pypa.io) 来管理项目依赖并运行开发脚本：

```bash
pipx install hatch
```

克隆仓库：

```bash
git clone https://github.com/LibreTranslate/LibreTranslate.git
cd LibreTranslate
```

Hatch 会自动在虚拟环境中安装所需依赖，并启用 [`pre-commit`](https://pre-commit.com/)，在每次提交前运行代码格式化检查。如果需要，你可以跳过 pre-commit 检查：

```bash
git commit --no-verify -m "Fix"
```

以开发模式运行：

```bash
hatch run dev --debug
```

然后在浏览器中打开：<http://localhost:5000>

你也可以进入安装了 libretranslate 的虚拟环境：

```bash
hatch shell
libretranslate [args]
# 或者
python main.py [args]
```

> 如果不想使用 hatch，也可以直接使用 `pip install -e ".[test]"` 安装依赖。

---

## 运行测试（Run the tests）

运行测试套件和代码检查：

```bash
hatch run test
```

调试时显示所有 `print()`：

```bash
hatch run test -s
```

在多个 Python 版本下运行测试：

```bash
hatch run all:test
```

清理虚拟环境：

```bash
hatch env prune
```

---

## 使用 Docker 运行（Run with Docker）

Linux/macOS：`./run.sh [args]`  
Windows：`run.bat [args]`

然后在浏览器中打开：<http://localhost:5000>

---

## 使用 Docker 构建（Build with Docker）

```bash
docker build -f docker/Dockerfile [--build-arg with_models=true] -t libretranslate .
```

如果你希望在完全离线的环境中运行 Docker 镜像，请添加 `--build-arg with_models=true` 参数。这样语言模型会在构建镜像时下载。否则模型会在镜像/容器首次运行时下载。

运行已构建的镜像：

```bash
docker run -it -p 5000:5000 libretranslate [args]
```

或者使用 Docker Compose 构建并运行：

```bash
docker compose up -d --build
```

> 你可以根据部署需求修改 [`docker-compose.yml`](https://github.com/LibreTranslate/LibreTranslate/blob/main/docker-compose.yml)，也可以创建一个额外的 `docker-compose.prod.yml` 文件用于生产环境配置。
>
> 模型被存储在容器中的 `/home/libretranslate/.local/share` 和 `/home/libretranslate/.local/cache` 路径下。如果你不希望每次销毁容器后重新下载模型，可以挂载 volume。要更新模型，使用 `--update-models` 参数。

---

## 常见问题（FAQ）

### Externally Managed Environment（外部管理的环境）

某些用户在安装依赖包时可能遇到以下错误：

```text
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try apt install
    python3-xyz, where xyz is the package you are trying to
    install.

    …
```

这是因为你的操作系统依赖并管理系统级 Python 环境。在这种情况下，应该使用 `venv`（虚拟环境）来管理项目依赖。

这样可以避免系统范围内安装 pip 包，从而防止不同项目或系统自身产生依赖冲突。

参考资料：

- [Python venv 官方文档](https://docs.python.org/library/venv.html)
