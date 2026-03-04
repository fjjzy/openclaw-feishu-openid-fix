# OpenClaw 飞书插件 Open ID Cross 错误修复

## 问题描述

飞书插件出现 `open id cross` 错误，通常发生在以下场景：
- 修改了 OpenClaw 配置
- 升级了 OpenClaw 版本
- 切换了飞书应用或租户

## 错误表现

```
飞书插件报错: open id cross
消息发送失败
用户 ID 不匹配
```

## 解决方案

### 快速修复步骤

1. **停止 Gateway**
   ```bash
   openclaw gateway stop
   ```

2. **删除 Session 缓存**
   
   找到对应 agent 的 session 目录并删除：
   ```bash
   # 默认路径
   rm -rf ~/.openclaw/agents/<agent-name>/session
   
   # 示例
   rm -rf ~/.openclaw/agents/default/session
   ```

3. **重新启动 Gateway**
   ```bash
   openclaw gateway start
   ```

### 完整命令（一键执行）

```bash
openclaw gateway stop && \
rm -rf ~/.openclaw/agents/default/session && \
openclaw gateway start
```

> ⚠️ 注意：将 `default` 替换为你的实际 agent 名称

## 原理说明

飞书插件会缓存用户的 `open_id` 到 session 中。当以下情况发生时，缓存的 ID 可能与新环境不匹配：

- **配置修改**：更换了 `app_id` 或 `app_secret`
- **版本升级**：飞书 API 或插件逻辑变更
- **租户切换**：飞书应用切换到不同租户

删除 session 目录会强制插件重新获取最新的用户 open_id，解决 cross 错误。

## 预防措施

1. **修改配置前**：先停止 gateway
2. **版本升级后**：清理 session 缓存
3. **多租户切换**：为不同租户使用不同 agent 配置

## 相关路径

| 路径 | 说明 |
|------|------|
| `~/.openclaw/agents/` | 所有 agent 配置目录 |
| `~/.openclaw/agents/<name>/session/` | 指定 agent 的 session 缓存 |
| `~/.openclaw/openclaw.json` | 主配置文件 |

## 参考

- [OpenClaw 文档](https://docs.openclaw.ai)
- [飞书开放平台 - 用户 ID 说明](https://open.feishu.cn/document/home/user-identity-introduction)
