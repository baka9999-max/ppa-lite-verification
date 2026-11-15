# PPA-Lite Spec Notes

## 项目目标

软件通过 APB 总线写入 packet，硬件将 packet 保存到 SRAM，启动处理核，最后通过 APB 读取处理结果和错误状态。

## 模块职责

| 模块 | 职责 |
|---|---|
| M1 `apb_slave_if` | APB 3.0 从接口、CSR 寄存器、地址译码、PKT_MEM 访问控制、状态和错误响应 |
| M2 `packet_sram` | 8×32-bit 存储器；一个写端口、M1 和 M3 两个读端口；不判断 packet 语义 |
| M3 `packet_proc_core` | IDLE/PROCESS/DONE FSM、packet 解析、长度/类型/校验检查、payload sum/XOR |
| Top `ppa_top` | 连接 M1/M2/M3，分发时钟和复位，不增加额外算法逻辑 |

## APB 规则

- SETUP：`PSEL=1, PENABLE=0`。
- ACCESS：`PSEL=1, PENABLE=1`。
- 本项目 `PREADY` 固定为 1，不插入等待周期。
- 合法访问 `PSLVERR=0`；非法地址、非法写入或 busy 期间写保护返回 `PSLVERR=1`。
- 错误访问不能产生副作用。

## Packet 格式

| 字节 | 字段 | 说明 |
|---|---|---|
| Byte0 | `pkt_len` | 总长度，合法范围 4～32 |
| Byte1 | `pkt_type` | 合法值 `0x01/0x02/0x04/0x08` |
| Byte2 | `flags` | 保留字段，当前为 0 |
| Byte3 | `hdr_chk` | `Byte0 ^ Byte1 ^ Byte2` |
| Byte4 以后 | payload | 长度为 `pkt_len-4` |

## PKT_MEM 映射

`0x040 + 4×N` 对应 SRAM Word N，N 的范围为 0～7。busy=0 时允许写；busy=1 时写入无效并返回 `PSLVERR=1`；读操作可在任意时刻进行。

## M3 状态机

- IDLE：等待 `start_i`。
- PROCESS：读取 SRAM，解析 packet 并计算结果。
- DONE：保持结果和 `done_o=1`，收到新的 `start_i` 后重新处理。

## 测试重点

- 正常：长度 4、普通长度 8、最大长度 32。
- 异常：长度 3/33、非法类型、type_mask 屏蔽、校验错误、校验旁路。
- 边界：连续两帧、busy 期间写 PKT_MEM、完整中断流程、期望长度不一致。

## 待确认问题

1. W1P 写入与 `PSLVERR` 的具体判定需要向助教确认。
2. 同步 SRAM 读数据与 APB `PRDATA` 的时序对齐需要通过仿真确认。
3. 需要确认课程规定的 32-bit word 内 packet 字节序。
