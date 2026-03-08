# 手机节点坑

> 权限 / 后台运行

手机端作为节点（Node）连接 OpenClaw 时的常见问题。

---

## 一、节点应用需在前台

### 现象

```
NODE_BACKGROUND_UNAVAILABLE
```

### 原因

部分系统限制后台应用保持连接，节点 App 退到后台后可能断开。

### 处理

- 将节点 App 加入系统「后台运行」白名单（若支持）
- 或保持 App 在前台/画中画
- 部分场景可考虑用云端 Gateway + 手机仅作客户端（通过 Telegram 等收发）

---

## 二、权限未授予

### 现象

```
*_PERMISSION_REQUIRED
LOCATION_PERMISSION_REQUIRED
CAMERA_PERMISSION_REQUIRED
```

### 处理

- 在系统设置中为节点 App 授予所需权限（摄像头、麦克风、位置等）
- 若之前拒绝过，需到应用详情中手动开启
- 重启 App 使权限生效

---

## 三、配对与审批

### 现象

节点已安装但工具调用失败，提示 `approval required`。

### 处理

```bash
openclaw nodes status
openclaw approvals get --node <id>
openclaw pairing list <channel>
```

在 Dashboard 或 CLI 中批准节点配对及 exec 审批请求。

---

## 四、网络与连接

### 现象

节点频繁掉线、延迟高。

### 处理

- 手机与 Gateway 需网络互通（同一 WiFi 或通过 Tailscale 等）
- 若 Gateway 在家庭网络，外出时需内网穿透才能连接
- 检查防火墙是否拦截节点与 Gateway 的通信端口

---

## 五、白名单与能力

### 现象

`SYSTEM_RUN_DENIED: allowlist miss`，命令被拒绝。

### 处理

- 在 `tools.exec.allowedBinaries` 中加入所需命令
- 或通过 exec 审批流程手动批准
- 节点端可能有额外能力限制，需查看节点文档

---

## 延伸阅读

- [多终端适配](/openclaw-knowledge/module-01-basics/02-deploy/05-multi-device)
- [执行工具](/tutorials/tools/exec)
- [网关故障排除](/tutorials/gateway/troubleshooting)
