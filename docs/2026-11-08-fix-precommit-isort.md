# 修复记录：pre-commit isort hook 环境构建失败（2026-11-08）

## 失败信息
- 仓库：wiseism/Squeezeformer
- Workflow：pre-commit
- 失败 run：[42426756043](https://github.com/wiseism/Squeezeformer/actions/runs/42426756043)（2026-11-07）、[42013876784](https://github.com/wiseism/Squeezeformer/actions/runs/42013876784)（2026-11-05）

## 错误日志摘录
```
CalledProcessError: command: ('/home/runner/.cache/pre-commit/repoz8bq9ya9/py_env-python3/bin/python',
  '-mpip', 'install', '.')
RuntimeError: The Poetry configuration is invalid:
  - tool.poetry.extras.pipfile_deprecated_finder[2] must match pattern ^[a-zA-Z-_.0-9]+$
error: metadata-generation-failed
##[error]Process completed with exit code 3.
```
（早先 run 42013876784 另一失败原因：`The version '3.8' ... was not found for Ubuntu 26.04`，
workflow 已改为 3.10。）

## 原因分析
`.pre-commit-config.yaml` 中 isort hook 钉在 `rev: 5.13.2`。isort 5.13.2 的 pyproject.toml
使用旧版 poetry 声明（`pipfile_deprecated_finder` extras 模式非法），新版
poetry-core 严格校验导致 `pip install .` 构建元数据失败。

## 修复内容
- `.pre-commit-config.yaml`：isort `rev: 5.13.2 → 9.0.2`（同时统一仓库 URL 大小写）。
  `--profile black -l 120` 参数保留。

## 验证
| run id | 结果 |
| --- | --- |
| 42441545393 | ✅ success |

## 复核（2026-11-13）
- 仓库由 private 转 public（Free 分钟额度耗尽会被 billing 拦截）。
- docs 推送触发的新 run 重跑后 ✅ success。
