# PPA-Lite Testplan

| 编号 | 类型 | 测试场景 | 输入 | 期望结果 | 状态 |
|---|---|---|---|---|---|
| N-1 | 正常 | 最小合法包 | `pkt_len=4` | `done=1, format_ok=1` | TODO |
| N-2 | 正常 | 普通合法包 | `pkt_len=8` | 结果正确 | TODO |
| N-3 | 正常 | 最大合法包 | `pkt_len=32` | 处理完成 | TODO |
| E-1 | 异常 | 包长下溢 | `pkt_len=3` | `length_error=1` | TODO |
| E-2 | 异常 | 包长上溢 | `pkt_len=33` | `length_error=1` | TODO |
| E-3 | 异常 | 非法 packet type | `pkt_type=0x03` | `type_error=1` | TODO |
| E-4 | 异常 | type_mask 屏蔽 | 当前类型被屏蔽 | `type_error=1` | TODO |
| E-5 | 异常 | 头校验错误 | `hdr_chk` 错误 | `chk_error=1` | TODO |
| E-6 | 异常 | 校验旁路 | `algo_mode=0` | `chk_error=0` | TODO |
| B-1 | 边界 | DONE 后再次启动 | 连续两帧 | 两帧结果正确 | TODO |
| B-2 | 边界 | busy 期间写 PKT_MEM | `busy=1` 时写入 | `PSLVERR=1`，内容不变 | TODO |
| B-3 | 边界 | 完整中断流程 | 使能 `done_irq` | `irq_o` 可置位和清除 | TODO |
| B-4 | 边界 | 期望长度不一致 | 实际长度不等于配置值 | `length_error=1` | TODO |

## 通过标准

- 每个场景都有对应 testcase；
- 结果由自动检查器判断；
- 失败日志包含 testcase 名称和必要的输入信息；
- 后续 Lab4 将在此表中把状态更新为 PASS 或 FAIL。
