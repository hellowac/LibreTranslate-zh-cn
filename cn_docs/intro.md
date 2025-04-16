# LibreTranslate

[Try it online!](https://libretranslate.com) | [API Docs](https://libretranslate.com/docs) | [Community Forum](https://community.libretranslate.com/)

[![Python versions](https://img.shields.io/pypi/pyversions/libretranslate)](https://pypi.org/project/libretranslate) [![Run tests](https://github.com/LibreTranslate/LibreTranslate/workflows/Run%20tests/badge.svg)](https://github.com/LibreTranslate/LibreTranslate/actions?query=workflow%3A%22Run+tests%22) [![Build and Publish Docker Image](https://github.com/LibreTranslate/LibreTranslate/actions/workflows/publish-docker.yml/badge.svg)](https://github.com/LibreTranslate/LibreTranslate/actions/workflows/publish-docker.yml) [![Publish package](https://github.com/LibreTranslate/LibreTranslate/actions/workflows/publish-package.yml/badge.svg)](https://github.com/LibreTranslate/LibreTranslate/actions/workflows/publish-package.yml) [![Awesome Humane Tech](https://raw.githubusercontent.com/humanetech-community/awesome-humane-tech/main/humane-tech-badge.svg?sanitize=true)](https://codeberg.org/teaserbot-labs/delightful-humane-design)

免费开源机器翻译 API，完全自托管。与其他 API 不同，它不依赖 Google 或 Azure 等专有服务提供商来执行翻译。相反，它的翻译引擎由开源 [Argos Translate](https://github.com/argosopentech/argos-translate) 库提供支持。

![Translation](https://github.com/user-attachments/assets/457696b5-dbff-40ab-a18e-7bfb152c5121)

[Try it online!](https://libretranslate.com) | [API Docs](https://libretranslate.com/docs)

## API 例子

### Simple

Request:

```javascript
const res = await fetch("https://libretranslate.com/translate", {
  method: "POST",
  body: JSON.stringify({
    q: "Hello!",
    source: "en",
    target: "es",
  }),
  headers: { "Content-Type": "application/json" },
});

console.log(await res.json());
```

Response:

```javascript
{
    "translatedText": "¡Hola!"
}
```

List of language codes: <https://libretranslate.com/languages>

### 自动检测语言

Request:

```javascript
const res = await fetch("https://libretranslate.com/translate", {
  method: "POST",
  body: JSON.stringify({
    q: "Ciao!",
    source: "auto",
    target: "en",
  }),
  headers: { "Content-Type": "application/json" },
});

console.log(await res.json());
```

Response:

```javascript
{
    "detectedLanguage": {
        "confidence": 83,
        "language": "it"
    },
    "translatedText": "Bye!"
}
```

### HTML

Request:

```javascript
const res = await fetch("https://libretranslate.com/translate", {
  method: "POST",
  body: JSON.stringify({
    q: '<p class="green">Hello!</p>',
    source: "en",
    target: "es",
    format: "html",
  }),
  headers: { "Content-Type": "application/json" },
});

console.log(await res.json());
```

Response:

```javascript
{
    "translatedText": "<p class=\"green\">¡Hola!</p>"
}
```

### 其他翻译

Request:

```javascript
const res = await fetch("https://libretranslate.com/translate", {
  method: "POST",
  body: JSON.stringify({
    q: "Hello",
    source: "en",
    target: "it",
    format: "text",
    alternatives: 3,
  }),
  headers: { "Content-Type": "application/json" },
});

console.log(await res.json());
```

Response:

```javascript
{
    "alternatives": [
        "Salve",
        "Pronto"
    ],
    "translatedText": "Ciao"
}
```

## 安装和运行

只需几行设置即可运行您自己的 API 服务器！

请确保您已安装 Python（建议使用 3.8 或更高版本），然后运行：

```bash
pip install libretranslate
libretranslate [args]
```

然后打开网页浏览器访问 <http://localhost:5000>

LibreTranslate 默认会安装所有可用语言的支持。如果只想加载特定语言并缩短启动时间，可以使用 **--load-only** 参数：

```bash
libretranslate --load-only en,es,fr
```

另请检查下方所有其他[参数](#设置--标志)。

在 Ubuntu 20.04 上，您还可以使用 <https://github.com/argosopentech/LibreTranslate-init> 提供的安装脚本。

## 使用 Docker 运行

您还可以使用 [docker](https://docker.com) 运行该应用程序：

### Linux/macOS

```bash
./run.sh [args]
```

### Windows

```bash
run.bat [args]
```

## 构建并运行

有关如何自行构建和运行项目的信息，请参阅 [CONTRIBUTING.md](./CONTRIBUTING.md)。

### CUDA

您可以使用硬件加速来加速安装了 CUDA 12.4.1 和 [nvidia-docker](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) 的 GPU 计算机上的翻译。

使用以下命令运行此版本：

```bash
docker compose -f docker-compose.cuda.yml up -d --build
```

## 参数

传递给进程或通过环境变量设置的参数分为两类。

- 用于切换特定运行模式或禁用应用程序某些部分的设置或运行时标志。这些设置或运行时标志在添加或删除时用作切换开关。

- 用于设置各种限制和配置应用程序的配置参数。这些配置参数需要传递给函数，如果删除，则使用默认参数。

### 设置 / 标志

| 参数                        | 描述                                                                     | 默认设置               | 环境变量名称                 |
| --------------------------- | ------------------------------------------------------------------------ | ---------------------- | ---------------------------- |
| --debug                     | 启用调试环境                                                             | `已禁用`               | LT_DEBUG                     |
| --ssl                       | 是否启用 SSL                                                             | `已禁用`               | LT_SSL                       |
| --api-keys                  | 在达到 --req-limit 时启用基于 API 密钥的每客户端速率限制数据库           | `不使用 API 密钥`      | LT_API_KEYS                  |
| --require-api-key-origin    | 要求对 API 的编程访问使用 API 密钥，除非请求来源匹配指定域名             | `对来源域没有限制`     | LT_REQUIRE_API_KEY_ORIGIN    |
| --require-api-key-secret    | 要求对 API 的编程访问使用 API 密钥，且客户端必须提供匹配的密钥           | `不要求提供密钥`       | LT_REQUIRE_API_KEY_SECRET    |
| --suggestions               | 允许用户建议                                                             | `已禁用`               | LT_SUGGESTIONS               |
| --disable-files-translation | 禁用文件翻译                                                             | `允许文件翻译`         | LT_DISABLE_FILES_TRANSLATION |
| --disable-web-ui            | 禁用 Web 界面                                                            | `Web 界面已启用`       | LT_DISABLE_WEB_UI            |
| --update-models             | 启动时更新语言模型                                                       | `仅在未找到模型时启用` | LT_UPDATE_MODELS             |
| --metrics                   | 启用 `/metrics` 端点以导出 [Prometheus](https://prometheus.io/) 使用指标 | `已禁用`               | LT_METRICS                   |

### 配置参数

| 参数                       | 描述                                                                                                                             | 默认参数                                 | 环境变量名称                |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- | --------------------------- |
| --host                     | 设置服务器绑定的主机地址                                                                                                         | `127.0.0.1`                              | LT_HOST                     |
| --port                     | 设置服务器绑定的端口号                                                                                                           | `5000`                                   | LT_PORT                     |
| --char-limit               | 设置字符数限制                                                                                                                   | `无限制`                                 | LT_CHAR_LIMIT               |
| --req-limit                | 设置每个客户端每分钟的最大请求数（不包括通过 API 密钥设置的限制）                                                                | `无限制`                                 | LT_REQ_LIMIT                |
| --req-limit-storage        | 设置用于请求限速数据存储的 URI。详见 [Flask Limiter 配置文档](https://flask-limiter.readthedocs.io/en/stable/configuration.html) | `memory://`                              | LT_REQ_LIMIT_STORAGE        |
| --req-time-cost            | 设置用于限速的时间代价（单位：秒）。如果一个请求耗时 10 秒，该值设为 5，则请求代价取 2 和实际耗时中的较大者                      | `无时间代价`                             | LT_REQ_TIME_COST            |
| --batch-limit              | 设置批量翻译请求中的最大文本数量                                                                                                 | `无限制`                                 | LT_BATCH_LIMIT              |
| --ga-id                    | 通过提供 ID，在 API 客户端页面启用 Google Analytics                                                                              | `空（不启用跟踪）`                       | LT_GA_ID                    |
| --frontend-language-source | 设置前端默认源语言                                                                                                               | `auto`                                   | LT_FRONTEND_LANGUAGE_SOURCE |
| --frontend-language-target | 设置前端默认目标语言                                                                                                             | `locale`（匹配站点语言）                 | LT_FRONTEND_LANGUAGE_TARGET |
| --frontend-timeout         | 设置前端翻译超时时间                                                                                                             | `500`                                    | LT_FRONTEND_TIMEOUT         |
| --api-keys-db-path         | 使用容器内的指定路径作为本地 API 密钥数据库路径，可为绝对路径或相对路径                                                          | `db/api_keys.db`                         | LT_API_KEYS_DB_PATH         |
| --api-keys-remote          | 使用远程端点验证 API 密钥，而不是使用本地数据库                                                                                  | `空（改为使用本地数据库）`               | LT_API_KEYS_REMOTE          |
| --get-api-key-link         | 在界面中显示获取 API 密钥的链接                                                                                                  | `空（Web UI 中不显示链接）`              | LT_GET_API_KEY_LINK         |
| --shared-storage           | 设置用于多进程数据共享的共享存储 URI（例如在使用 gunicorn 时）                                                                   | `memory://`                              | LT_SHARED_STORAGE           |
| --secondary                | 将该实例标记为副本节点，用于多节点部署中避免与主节点冲突                                                                         | `主节点`                                 | LT_SECONDARY                |
| --load-only                | 指定可用语言                                                                                                                     | `空（使用 argostranslate 中的全部语言）` | LT_LOAD_ONLY                |
| --threads                  | 设置线程数量                                                                                                                     | `4`                                      | LT_THREADS                  |
| --metrics-auth-token       | 通过 Authorization Bearer 令牌保护 `/metrics` 端点，仅允许拥有有效令牌的客户端访问                                               | `空（不启用身份验证）`                   | LT_METRICS_AUTH_TOKEN       |
| --url-prefix               | 为 URL 添加前缀，例如：example.com:5000/url-prefix/                                                                              | `/`                                      | LT_URL_PREFIX               |

以下是这段 Markdown 文本的中文翻译，保留了原有的技术风格和结构：

---

### 说明

- 每个参数都可以通过对应的环境变量来设置。环境变量会覆盖默认值，但其优先级低于命令行参数。环境变量特别适合在 Docker 中使用。环境变量的名称为对应参数名称的大写蛇形命名（upper-snake-case），并加上 `LT` 前缀。

- 若要启用 API 密钥机制，请将 `--req-limit` 设置为 `0` 并添加 `--api-keys` 参数。没有有效 API 密钥的请求将被拒绝。

- 启用 `--update-models` 参数时，将在程序启动时强制更新模型，无论是否有更新可用。

---

## 更新

### 软件

如果你是通过 pip 安装的：

```bash
pip install -U libretranslate
```

如果你使用的是 Docker：

```bash
docker pull libretranslate/libretranslate
```

### 语言模型

通过添加 `--update-models` 参数启动程序。例如：

```bash
libretranslate --update-models
```

或：

```bash
./run.sh --update-models
```

你也可以直接运行 `scripts/install_models.py` 脚本来安装模型。

---

## 使用 WSGI 与 Gunicorn 运行

```bash
pip install gunicorn
gunicorn --bind 0.0.0.0:5000 'wsgi:app'
```

你也可以通过以下方式将应用参数直接传递给 Gunicorn：

```bash
gunicorn --bind 0.0.0.0:5000 'wsgi:app(api_keys=True)'
```

---

## Kubernetes 部署

参见 [JM Robles 的 Medium 文章](https://jmrobles.medium.com/libretranslate-your-own-translation-service-on-kubernetes-b46c3e1af630) 以及由 @rasos 改进的 [k8s.yaml](https://github.com/LibreTranslate/LibreTranslate/blob/main/k8s.yaml)。

### Helm Chart

基于 @rasos 的工作，你现在可以通过 Helm 在 Kubernetes 上安装 LibreTranslate。

一个 Helm Chart 已在 [helm-chart 仓库](https://github.com/LibreTranslate/helm-chart/) 中提供，内含更多安装细节。

你可以通过以下命令快速在 Kubernetes 上使用 Helm 安装 LibreTranslate：

```bash
helm repo add libretranslate https://libretranslate.github.io/helm-chart/
helm repo update
helm search repo libretranslate

helm install libretranslate libretranslate/libretranslate --namespace libretranslate --create-namespace
```

---

## 管理 API 密钥

LibreTranslate 支持用户级别的限流配额。例如，你可以为用户分配 API 密钥，以允许他们享有更高的每分钟请求上限（前提是你设置了 `--req-limit`）。默认情况下，所有用户都受 `--req-limit` 的限制，但通过向 REST 接口传递可选参数 `api_key`，用户可获得更高的请求限额。你还可以为特定密钥设置字符限制，从而绕过默认的 `--char-limit` 设置。

要启用 API 密钥功能，只需使用 `--api-keys` 参数启动 LibreTranslate。如果你通过 `--api-keys-db-path` 参数修改了 API 密钥数据库路径，那么在使用 `ltmanage keys` 命令时也必须通过相同参数指定路径。

以下是这部分内容的翻译，保持 Markdown 格式和技术文风不变：

---

### 添加新密钥

要生成一个每分钟限 120 次请求的 API 密钥：

```bash
ltmanage keys add 120
```

要生成一个每分钟限 120 次请求、且每次最多允许 5,000 字符的 API 密钥：

```bash
ltmanage keys add 120 --char-limit 5000
```

如果你修改了 API 密钥数据库路径：

```bash
ltmanage keys --api-keys-db-path path/to/db/dbName.db add 120
```

---

### 删除密钥

```bash
ltmanage keys remove <api-key>
```

---

### 查看密钥

```bash
ltmanage keys
```

---

## Prometheus 指标监控

当你在启动时添加 `--metrics` 参数（默认关闭）后，LibreTranslate 将支持 Prometheus 的 [exporter](https://prometheus.io/docs/instrumenting/exporters/) 功能。启用后，实例上会挂载一个 `/metrics` 端点：

<http://localhost:5000/metrics>

返回示例（PromQL）：

```promql
# HELP libretranslate_http_requests_in_flight Multiprocess metric
# TYPE libretranslate_http_requests_in_flight gauge
libretranslate_http_requests_in_flight{api_key="",endpoint="/translate",request_ip="127.0.0.1"} 0.0
# HELP libretranslate_http_request_duration_seconds Multiprocess metric
# TYPE libretranslate_http_request_duration_seconds summary
libretranslate_http_request_duration_seconds_count{api_key="",endpoint="/translate",request_ip="127.0.0.1",status="200"} 0.0
libretranslate_http_request_duration_seconds_sum{api_key="",endpoint="/translate",request_ip="127.0.0.1",status="200"} 0.0
```

你可以通过如下方式配置 `prometheus.yml` 以抓取指标：

```yaml
scrape_configs:
  - job_name: "libretranslate"

    # 仅在启用 --metrics-auth-token 时需要
    #authorization:
    #credentials: "mytoken"

    static_configs:
      - targets: ["localhost:5000"]
```

为了保护 `/metrics` 端点，可以使用参数 `--metrics-auth-token mytoken`。

如果你使用 Gunicorn，需创建一个用于存放多进程指标数据的目录，并设置环境变量 `PROMETHEUS_MULTIPROC_DIR`：

```bash
mkdir -p /tmp/prometheus_data
rm /tmp/prometheus_data/*
export PROMETHEUS_MULTIPROC_DIR=/tmp/prometheus_data
gunicorn -c scripts/gunicorn_conf.py --bind 0.0.0.0:5000 'wsgi:app(metrics=True)'
```

---

## 语言绑定（Language Bindings）

你可以通过以下客户端库调用 LibreTranslate API：

- Rust: <https://github.com/DefunctLizard/libretranslate-rs>
- Node.js: <https://github.com/franciscop/translate>
- TypeScript: <https://github.com/tderflinger/libretranslate-ts>
- .Net: <https://github.com/sigaloid/LibreTranslate.Net>
- Go: <https://github.com/SnakeSel/libretranslate>
- Python: <https://github.com/argosopentech/LibreTranslate-py>
- PHP: <https://github.com/jefs42/libretranslate>
- C++: <https://github.com/argosopentech/LibreTranslate-cpp>
- Swift: <https://github.com/wacumov/libretranslate>
- Unix: <https://github.com/argosopentech/LibreTranslate-sh>
- Shell: <https://github.com/Hayao0819/Hayao-Tools/tree/master/libretranslate-sh>
- Java: <https://github.com/suuft/libretranslate-java>
- Ruby: <https://github.com/noesya/libretranslate>
- R: <https://github.com/myanesp/libretranslateR>

以下是这部分内容的翻译，保持了原有的结构和格式：

---

## Discourse 插件

你可以使用 [官方 Discourse 翻译插件](https://github.com/discourse/discourse-translator) 通过 LibreTranslate 翻译 [Discourse](https://discourse.org) 论坛主题。安装方法是修改 `/var/discourse/containers/app.yml` 文件：

```yaml
## 插件写在这里
## 详见 https://meta.discourse.org/t/19157
hooks:
  after_code:
    - exec:
        cd: $home/plugins
        cmd:
          - git clone https://github.com/discourse/docker_manager.git
          - git clone https://github.com/discourse/discourse-translator
    ...
```

然后运行 `./launcher rebuild app`。接着在 Discourse 管理面板中选择 "LibreTranslate" 作为翻译提供者，并配置相应的接口参数。

你可以在这个[页面](https://community.libretranslate.com/t/have-you-considered-adding-the-libretranslate-discourse-translator-multilingual-to-this-forum/766)上查看实际效果。

---

## 移动应用

- [LibreTranslator](https://codeberg.org/BeoCode/LibreTranslator)：一个 Android 应用，可在 [Play 商店](https://play.google.com/store/apps/details?id=de.beowulf.libretranslater) 和 [F-Droid 商店](https://f-droid.org/packages/de.beowulf.libretranslater/) 获取，使用 LibreTranslate API。
- [Translate You](https://github.com/you-apps/TranslateYou)：一个专注隐私的翻译应用，基于 MD3 设计，可在 [F-Droid 商店](https://f-droid.org/packages/com.bnyro.translate/) 获取，支持包括 LibreTranslate 在内的多个提供者。
- [LiTranslate](https://github.com/viktorkalyniuk/LiTranslate-iOS)：一个 iOS 应用，可在 [App Store](https://apps.apple.com/us/app/litranslate/id1644385339) 下载，使用 LibreTranslate API。

---

## 网页浏览器支持

- [minbrowser](https://minbrowser.org/) 是一个集成了 [LibreTranslate 支持](https://github.com/argosopentech/argos-translate/discussions/158#discussioncomment-1141551) 的网页浏览器。
- LibreTranslate 的 Firefox 插件[仍在开发中](https://github.com/LibreTranslate/LibreTranslate/issues/55)。

---

## 公共镜像站点

以下是一些公共的 LibreTranslate 实例，有些需要 API 密钥。如需添加新地址，请提交 pull request。

| URL                                                                 | 是否需要 API 密钥 | 链接                                                                                                      |
| ------------------------------------------------------------------- | ----------------- | --------------------------------------------------------------------------------------------------------- |
| [libretranslate.com](https://libretranslate.com)                    | ✅                 | [ [获取 API 密钥](https://portal.libretranslate.com) ] [ [服务状态](https://status.libretranslate.com/) ] |
| [translate.flossboxin.org.in](https://translate.flossboxin.org.in/) | ❌                 | [ [联系邮箱](mailto:dev@flossboxin.org.in) ]                                                              |
| [lt.blitzw.in](https://lt.blitzw.in/)                               | ❌                 |                                                                                                           |

---

## TOR / I2P 镜像

| URL                                                                                                                                            |
| ---------------------------------------------------------------------------------------------------------------------------------------------- |
| [lt.vernccvbvyi5qhfzyqengccj7lkove6bjot2xhh5kajhwvidqafczrad.onion](http://lt.vernccvbvyi5qhfzyqengccj7lkove6bjot2xhh5kajhwvidqafczrad.onion/) |
| [lt.vern.i2p](http://vernf45n7mxwqnp5riaax7p67pwcl7wcefdcnqqvim7ckdx4264a.b32.i2p/)                                                            |

---

## 添加新语言模型

你可以通过以下两种方式创建新的语言模型：

- [Locomotive](https://github.com/LibreTranslate/Locomotive)
- [Argos Train](https://github.com/argosopentech/argos-train)（[视频教程](https://www.youtube.com/watch?v=Vj_qgnhOEwg)）

大部分训练数据来自开源的平行语料库 [Opus](http://opus.nlpl.eu/)，也可以参考 [NLLU](https://nllu.libretranslate.com)。

---

## 本地化（界面语言支持）

LibreTranslate 的网页界面支持所有其翻译能力涵盖的语言，并且（粗略地）可以[翻译自己](https://github.com/LibreTranslate/LibreTranslate/blob/main/scripts/update_locales.py)！部分语言尚未被人工校审，因此不会出现在界面语言列表中。你可以通过开启 `--debug` 模式启用所有语言。

如果你想帮助改进或校对 UI 翻译：

- 前往 <https://hosted.weblate.org/projects/libretranslate/app/#translations>，所有更改会自动推送至代码库。
- 校审或修改所有字符串后，提交 pull request，修改 `libretranslate/locales/{code}/meta.json` 文件：

```json
{
 "name": "<Language>",
 "reviewed": true <-- 将此项从 false 改为 true
}
```

### UI 语言

| Language              | Reviewed           | Weblate Link                                                             |
| --------------------- | ------------------ | ------------------------------------------------------------------------ |
| Arabic                |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/ar/)      |
| Azerbaijani           |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/az/)      |
| Basque                | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/eu/)      |
| Chinese               |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/zh/)      |
| Chinese (Traditional) |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/zh_Hant/) |
| Czech                 | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/cs/)      |
| Danish                |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/da/)      |
| Dutch                 |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/nl/)      |
| English               | :heavy_check_mark: | [Edit](https://hosted.weblate.org/projects/libretranslate/app/)          |
| Esperanto             | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/eo/)      |
| Finnish               |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/fi/)      |
| French                | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/fr/)      |
| German                | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/de/)      |
| Greek                 |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/el/)      |
| Hebrew                |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/he/)      |
| Hindi                 |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/hi/)      |
| Hungarian             |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/hu/)      |
| Indonesian            |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/id/)      |
| Irish                 |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/ga/)      |
| Italian               | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/it/)      |
| Japanese              |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/ja/)      |
| Kabyle                | :heavy_check_mark: | [Edit](https://hosted.weblate.org/projects/libretranslate/app/kab/)      |
| Korean                | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/ko/)      |
| Occitan               |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/oc/)      |
| Persian               |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/fa/)      |
| Polish                |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/pl/)      |
| Portuguese            | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/pt/)      |
| Russian               | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/ru/)      |
| Slovak                |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/sk/)      |
| Spanish               | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/es/)      |
| Swedish               |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/sv/)      |
| Turkish               |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/tr/)      |
| Ukrainian             | :heavy_check_mark: | [Edit](https://hosted.weblate.org/translate/libretranslate/app/uk/)      |
| Vietnamese            |                    | [Edit](https://hosted.weblate.org/translate/libretranslate/app/vi/)      |

以下是这部分内容的翻译，保留了原格式、结构及细节说明：

---

## 路线图（Roadmap）

欢迎通过提交 Pull Request 来参与改进！

- [ ] 为所有编程语言添加语言绑定
- [ ] [改进翻译效果](https://community.libretranslate.com/t/the-best-way-to-train-models/172)

其他建议也非常欢迎！

---

## 常见问题（FAQ）

### 我可以在生产环境的应用中使用 libretranslate.com 上的 API 服务吗？

简短地说，可以，**[但你需要购买 API 密钥](https://portal.libretranslate.com)**。当然，你也可以免费在自己的服务器上部署 LibreTranslate。

---

### 为什么 libretranslate.com 上的部分翻译与自托管版本不同？

语言模型默认从 [argos-index](https://github.com/argosopentech/argospm-index) 加载。我们有时会在 libretranslate.com 上部署尚未加入 argos-index 的模型，例如从 OPUS 转换的模型（[相关讨论](https://community.libretranslate.com/t/opus-mt-language-models-port-thread/757)）。

---

### 语言模型存储在哪里？

在 `$HOME/.local/share/argos-translate/packages` 目录下。Windows 系统中为 `C:\Users\youruser\.local\share\argos-translate\packages`。

---

### 我可以在反向代理（如 Apache2 或 Caddy）后面运行 LibreTranslate 吗？

可以，下面是使用 Apache2 和 Caddy 配置子域名（带 HTTPS 证书）反向代理到本地 Docker 上运行的 LibreTranslate 的示例：

```bash
sudo docker run -ti --rm -p 127.0.0.1:5000:5000 libretranslate/libretranslate
```

如果你希望从 `domain.tld:5000` 也能访问（除了 `subdomain.domain.tld`），可以去掉上述命令中的 `127.0.0.1`，这有助于排查是否是 Apache2 或 Docker 容器的问题。

如果希望 Docker 容器在开机时自动启动，可加上 `--restart unless-stopped` 参数。

<details>
<summary>Apache 配置示例</summary>
<br>

请将 `[YOUR_DOMAIN]` 替换为你的完整域名，例如 `translate.domain.tld` 或 `libretranslate.domain.tld`。

如需记录请求日志，请取消 `ErrorLog` 和 `CustomLog` 行前的 `#` 注释。

```ApacheConf
# Libretranslate

# HTTP 跳转至 HTTPS
<VirtualHost *:80>
    ServerName http://[YOUR_DOMAIN]
    Redirect / https://[YOUR_DOMAIN]
    # ErrorLog ${APACHE_LOG_DIR}/error.log
    # CustomLog ${APACHE_LOG_DIR}/tr-access.log combined
</VirtualHost>

# HTTPS
<VirtualHost *:443>
    ServerName https://[YOUR_DOMAIN]

    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPreserveHost On

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/[YOUR_DOMAIN]/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/[YOUR_DOMAIN]/privkey.pem
    SSLCertificateChainFile /etc/letsencrypt/live/[YOUR_DOMAIN]/fullchain.pem

    # ErrorLog ${APACHE_LOG_DIR}/tr-error.log
    # CustomLog ${APACHE_LOG_DIR}/tr-access.log combined
</VirtualHost>
```

可以将以上配置添加到已有站点配置中，或新建文件 `/etc/apache2/sites-available/new-site.conf`，然后运行：

```bash
sudo a2ensite new-site.conf
```

如需获取 HTTPS 子域名证书，安装 `certbot`（推荐 snap 包），并运行：

```bash
sudo certbot certonly --manual --preferred-challenges dns
```

输入你的信息（例如 `subdomain.domain.tld`），按提示添加 DNS TXT 记录。证书将保存到 `/etc/letsencrypt/live/{subdomain.domain.tld}/`。

如果暂时不使用 HTTPS，也可以注释掉配置文件中的 SSL 相关行。

</details>

<details>
<summary>Caddy 配置示例</summary>
<br>

请将 `[YOUR_DOMAIN]` 替换为你的完整域名，例如 `translate.domain.tld` 或 `libretranslate.domain.tld`。

```Caddyfile
# Libretranslate
[YOUR_DOMAIN] {
  reverse_proxy localhost:5000
}
```

将此配置加入现有的 Caddyfile，或保存为单独的 `Caddyfile` 文件，并在同一目录中运行：

```bash
sudo caddy reload
```

</details>

<details>
<summary>NGINX 配置示例</summary>
<br>

请将 `[YOUR_DOMAIN]` 替换为你的完整域名，例如 `translate.domain.tld` 或 `libretranslate.domain.tld`。

如需禁用日志记录，请移除 `access_log` 和 `error_log` 行前的 `#`。

```NginxConf
server {
  listen 80;
  server_name [YOUR_DOMAIN];
  return 301 https://$server_name$request_uri;
}

server {
  listen 443 http2 ssl;
  server_name [YOUR_DOMAIN];

  #access_log off;
  #error_log off;

  # SSL Section
  ssl_certificate /etc/letsencrypt/live/[YOUR_DOMAIN]/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/[YOUR_DOMAIN]/privkey.pem;

  ssl_protocols TLSv1.2 TLSv1.3;

  # Using the recommended cipher suite from: https://wiki.mozilla.org/Security/Server_Side_TLS
  ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384';

  ssl_session_timeout 10m;
  ssl_session_cache shared:MozSSL:10m;  # about 40000 sessions
  ssl_session_tickets off;

  # Specifies a curve for ECDHE ciphers.
  ssl_ecdh_curve prime256v1;
  # Server should determine the ciphers, not the client
  ssl_prefer_server_ciphers on;


  # Header section
  add_header Strict-Transport-Security  "max-age=31536000; includeSubDomains; preload" always;
  add_header Referrer-Policy            "strict-origin" always;

  add_header X-Frame-Options            "SAMEORIGIN"    always;
  add_header X-XSS-Protection           "1; mode=block" always;
  add_header X-Content-Type-Options     "nosniff"       always;
  add_header X-Download-Options         "noopen"        always;
  add_header X-Robots-Tag               "none"          always;

  add_header Feature-Policy             "microphone 'none'; camera 'none'; geolocation 'none';"  always;
  # Newer header but not everywhere supported
  add_header Permissions-Policy         "microphone=(), camera=(), geolocation=()" always;

  # Remove X-Powered-By, which is an information leak
  fastcgi_hide_header X-Powered-By;

  # Do not send nginx server header
  server_tokens off;

  # GZIP Section
  gzip on;
  gzip_disable "msie6";

  gzip_vary on;
  gzip_proxied any;
  gzip_comp_level 6;
  gzip_buffers 16 8k;
  gzip_http_version 1.1;
  gzip_min_length 256;
  gzip_types text/xml text/javascript font/ttf font/eot font/otf application/x-javascript application/atom+xml application/javascript application/json application/manifest+json application/rss+xml application/x-web-app-manifest+json application/xhtml+xml application/xml image/svg+xml image/x-icon text/css text/plain;

  location / {
      proxy_pass http://127.0.0.1:5000/;
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      client_max_body_size 0;
  }
}

```

将其添加到现有的 NGINX 配置中或将其保存为 `/etc/nginx/site-enabled` 目录中的 `libretranslate` ，然后运行 ​​`sudo nginx -s reload`。

</details>

以下是这部分内容的翻译，保留了原有结构和代码格式：

---

### 我可以使用 systemd 来运行 LibreTranslate（即默认使用 pip/python 安装的版本）吗？

可以，只需在 `/etc/systemd/system` 中创建一个服务文件，并启用它在开机启动。

`.env`（环境变量）文件是否需要取决于你的部署方式。将以下内容写入服务文件（根据需要修改路径等参数），文件命名为 `libretranslate.service`：

```ini
[Unit]
Description=LibreTranslate
After=network.target

[Service]
User=root
Type=idle
Restart=always
Environment="PATH=/usr/local/lib/python3.11/dist-packages/libretranslate"
ExecStart=/usr/bin/python3 /usr/local/bin/libretranslate
EnvironmentFile=/usr/local/lib/python3.11/dist-packages/libretranslate/.env
ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=1

[Install]
WantedBy=multi-user.target
```

保存文件后，运行以下命令以重新加载守护进程并启动服务：

```bash
systemctl daemon-reload
systemctl start libretranslate.service
systemctl enable libretranslate.service
```

---

### 我可以进行批量翻译吗？

可以，将字符串数组而不是单个字符串传递给 `q` 字段即可：

```javascript
const res = await fetch("https://libretranslate.com/translate", {
  method: "POST",
  body: JSON.stringify({
    q: ["Hello", "world"],
    source: "en",
    target: "es",
  }),
  headers: { "Content-Type": "application/json" },
});

console.log(await res.json());
// {
//     "translatedText": [
//         "Hola",
//         "mundo"
//     ]
// }
```

---

## 贡献（Contributing）

欢迎各种贡献！以下是一些建议方向：

- 使用 [Locomotive](https://github.com/LibreTranslate/Locomotive) 训练新的语言模型。例如，我们希望为 [德语](https://community.libretranslate.com/t/help-wanted-improve-en-de-translation/935) 和其他许多语言改进神经网络翻译质量。
- 有能力超越我们现有模型的性能吗？训练一个新的语言模型并来一场性能比拼吧！提交模型的方法是：在 [社区论坛](https://community.libretranslate.com/) 发帖，附上 `.argosmodel` 文件的下载链接，并提供该模型翻译的一些示例文本。
- 从 [issue 列表](https://github.com/LibreTranslate/LibreTranslate/issues) 中选择一个问题着手解决。

---

## 鸣谢（Credits）

本项目主要得益于 [Argos Translate](https://github.com/argosopentech/argos-translate)，它是翻译引擎的核心。

---

## 许可证（License）

本项目遵循 [GNU Affero 通用公共许可证 第三版（AGPL v3）](https://www.gnu.org/licenses/agpl-3.0.en.html)

---

## 商标（Trademark）

请参阅 [商标使用指南](https://github.com/LibreTranslate/LibreTranslate/blob/main/TRADEMARK.md)
