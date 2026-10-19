# 小智环境感知与主动语音播报（毕业设计）

本仓库在自建 `xiaozhi-esp32-server` 基础上，增加两条“环境 → 小智”的链路：

1. **MCP ↔ MQTT 桥接**：让小智通过 MCP 工具读取/发布 MQTT 传感器数据；
2. **MQTT 阈值告警规则引擎**：订阅传感器主题，命中阈值后让小智主动说话，
   并支持冷却、静默时段、设备忙/离线排队。

```text
MQTT 传感器 ──> alert_engine ──HTTP──> xiaozhi-server ──WS/Opus──> ESP32-S3
                    ▲                       ▲
                    │                       │
              网页规则配置             MCP / TTS / ASR / LLM
```

## 快速启动

```bash
cd /home/fwtree/server-xiaozhi

./start-all.sh      # 小智服务端 + 规则引擎
./stop-all.sh       # 全部停止
```

| 服务 | 地址 |
| --- | --- |
| 小智服务端状态页 | <http://127.0.0.1:8003/> |
| 告警规则引擎网页 | <http://127.0.0.1:8010/> |
| OTA（电脑热点） | `http://192.168.137.1:18003/xiaozhi/ota/` |
| WebSocket（电脑热点） | `ws://192.168.137.1:18000/xiaozhi/v1/` |

## 目录

```text
server-xiaozhi/
├── xiaozhi-server/        # 自建小智服务端（ASR/LLM/TTS/MCP/主动播报 API）
├── mcp_mqtt/              # 原 MQTT 桥接器 + stdio MCP 适配器
├── alert_engine/           # 毕设新增：阈值规则引擎 + 网页
├── start-all.sh
└── stop-all.sh
```

## 文档

- [服务端安装、OTA/热点、主动播报接口](xiaozhi-server/README-INSTALL.md)
- [MQTT 桥接器说明](mcp_mqtt/README.md)
- [规则引擎说明、API、测试](alert_engine/README.md)

## 已验证

- 真实 MQTT 发布 `35.6C` → 规则命中 → 模拟 ESP32 收到 TTS 与 Opus 音频；
- 冷却、静默、设备忙排队、设备离线等待上线；
- 30 项规则引擎单元测试通过。

## 安全提醒

`mcp_mqtt/config.ini` 和 `xiaozhi-server/data/.config.yaml` 含真实账号、
密码/Token，提交论文、截图或公开仓库前请脱敏并轮换。# xiao-server
# xiao-server
