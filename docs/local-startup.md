# 本地启动说明

这份文档记录当前项目在本机的推荐启动方式，方便后续查看和迁移到新电脑。

## 当前本地启动参数

- 访问地址：`http://127.0.0.1:8886`
- 访问密码：`123123`
- 启动模式：`--reload`

当前推荐启动命令：

```powershell
.\.venv\Scripts\python.exe webui.py --host 127.0.0.1 --port 8886 --reload --access-password 123123
```

## 为什么不要直接用 `python`

当前机器里的系统 `python` 是 3.9，项目要求 3.10+。

如果直接运行：

```powershell
python webui.py
```

可能会遇到 `FlowState | None` 这类 3.10+ 语法报错。

因此请始终使用项目自己的虚拟环境：

```powershell
.\.venv\Scripts\python.exe
```

或者先激活：

```powershell
.\.venv\Scripts\activate
python webui.py --host 127.0.0.1 --port 8886 --reload --access-password 123123
```

## 首次环境准备

如果新电脑还没有 `.venv`，可以按下面步骤准备：

```powershell
uv venv .venv --python 3.12
uv pip install -r requirements.txt --python .venv\Scripts\python.exe
```

## LuckMail 说明

LuckMail SDK 已经内置到仓库，不需要再单独去 `LuckMailSdk-Python` 目录执行 `pip install .`。

当前项目会直接使用仓库根目录下的：

```text
luckmail/
```

另外，运行依赖里已经补齐了 `httpx`，所以换电脑后只需要安装当前项目依赖即可。

## 日志位置

当前本地启动日志：

- 标准输出日志：`logs\webui-8886-stdout.log`
- 错误日志：`logs\webui-8886-stderr.log`

## 常用命令

启动：

```powershell
.\.venv\Scripts\python.exe webui.py --host 127.0.0.1 --port 8886 --reload --access-password 123123
```

查看 8886 端口上的项目进程：

```powershell
Get-CimInstance Win32_Process | Where-Object {
    $_.Name -match '^python(\.exe)?$' -and
    $_.CommandLine -match 'webui\.py' -and
    $_.CommandLine -match '8886' -and
    $_.CommandLine -match 'codex-console'
}
```

停止当前项目进程：

```powershell
$pids = Get-CimInstance Win32_Process | Where-Object {
    $_.Name -match '^python(\.exe)?$' -and
    $_.CommandLine -match 'webui\.py' -and
    $_.CommandLine -match '8886' -and
    $_.CommandLine -match 'codex-console'
} | Select-Object -ExpandProperty ProcessId

if ($pids) {
    Stop-Process -Id $pids -Force
}
```

## 当前状态检查

浏览器打开：

```text
http://127.0.0.1:8886
```

如果服务正常，页面会跳转到登录页。

用命令快速探活：

```powershell
curl.exe -I http://127.0.0.1:8886/
```

返回 `405 Method Not Allowed` 也属于正常现象，因为根路由不支持 `HEAD`，浏览器使用 `GET` 访问即可。
