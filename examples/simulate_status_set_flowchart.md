# simulate_status_set 函数流程图

## 函数说明

**文件位置**: `MTK_SIP/libsipua/src/simutask/simulate_main.c:78`

**函数签名**: 
```c
static void simulate_status_set(call_simulate_param_t* simu_param, int line_id, int statusCode)
```

**功能**: 根据状态码查找对应的状态字符串，并更新配置和状态信息。

## 流程图

![simulate_status_set 流程图](https://mdn.alipayobjects.com/one_clip/afts/img/rVi3R42pIRUAAAAARxAAAAgAoEACAQFr/original)

## 流程说明

### 1. 函数入口
- 接收参数：`simu_param`（模拟参数结构体指针）、`line_id`（线路ID）、`statusCode`（状态码）

### 2. 初始化
- 获取全局上下文：`simu_cntx = &gSimuContext`
- 初始化指针：`pEntry = &g_emulate_status_tbl[0]`（指向状态表首元素）

### 3. 循环查找
- **循环条件**: `pEntry->statusCode != SIMULATE_STATUS_TEST_MAX`
- **匹配判断**: `pEntry->statusCode == statusCode`
  - **匹配**: 执行状态设置操作
  - **不匹配**: `pEntry++`，继续查找下一个条目

### 4. 状态设置（找到匹配后）
- 设置配置：`sc_conf_set(SIMU_TEST_STATUS, simu_cntx->simuLineIdx, pEntry->statusStr)`
- 条件编译处理：
  - 如果定义了 `TCSUPPORT_CT_JOYME2`，复制状态字符串到 `dbusProp[Status]`
- 更新状态：`simu_param->lineState = statusCode`
- 记录日志：`VOIP_LOG_DBG`
- 跳出循环：`break`

### 5. 函数返回
- 无论是否找到匹配，函数都会返回

## 状态表映射

函数在以下状态表中查找匹配的状态码：

| statusCode | statusStr | 说明 |
|-----------|-----------|------|
| SIMULATE_STATUS_IDLE | "Idle" | 空闲状态 |
| SIMULATE_STATUS_OFFHOOK | "Off-hook" | 摘机状态 |
| SIMULATE_STATUS_DIAL_TONE | "Dialtone" | 拨号音状态 |
| SIMULATE_STATUS_RECEIVING | "Receiving" | 接收中状态 |
| SIMULATE_STATUS_RECEIVE_END | "ReceiveEnd" | 接收结束状态 |
| SIMULATE_STATUS_RINGING_BACK | "Ringing-back" | 回铃状态 |
| SIMULATE_STATUS_CONNECTED | "Connected" | 已连接状态 |
| SIMULATE_STATUS_TEST_END | "Testend" | 测试结束状态 |
| SIMULATE_STATUS_TEST_MAX | "default" | 结束标记（用于终止循环） |

## 关键点

1. **线性查找**: 使用简单的线性查找算法遍历状态表
2. **配置更新**: 找到匹配后更新系统配置和状态结构
3. **条件编译**: 根据编译选项决定是否更新 D-Bus 属性
4. **日志记录**: 记录状态变更的调试信息，便于问题追踪

## 调用场景

该函数在以下场景中被调用：
- 测试结束：`SIMULATE_STATUS_TEST_END`
- 回铃状态：`SIMULATE_STATUS_RINGING_BACK`
- 连接状态：`SIMULATE_STATUS_CONNECTED`
- 接收结束：`SIMULATE_STATUS_RECEIVE_END`
- 空闲状态：`SIMULATE_STATUS_IDLE`
- 摘机状态：`SIMULATE_STATUS_OFFHOOK`
- 拨号音状态：`SIMULATE_STATUS_DIAL_TONE`
- 接收中状态：`SIMULATE_STATUS_RECEIVING`

---

**生成时间**: 2026-01-14  
**生成工具**: MCP Server Chart (Flow Diagram)
