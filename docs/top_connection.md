# PPA-Lite Top Connection

## 模块连接图

```mermaid
flowchart LR
    AM[APB Master] -->|PADDR/PWRITE/PWDATA/PSEL/PENABLE| M1[M1 apb_slave_if]
    M1 -->|apb_wr_en/apb_addr/apb_wr_data| M2[M2 packet_sram]
    M2 -->|apb_rd_data| M1
    M1 -->|start/config| M3[M3 packet_proc_core]
    M3 -->|busy/done/results/errors| M1
    M3 -->|proc_rd_en/proc_rd_addr| M2
    M2 -->|proc_rd_data| M3
    M1 -->|PRDATA/PREADY/PSLVERR| AM
    M1 --> IRQ[irq_o]
```

## 主要信号

### M1 到 M2

- `pkt_mem_we_o`
- `pkt_mem_addr_o`
- `pkt_mem_wdata_o`
- `pkt_mem_re_o`

### M1 到 M3

- `start_o`
- `enable_o`
- `algo_mode_o`
- `type_mask_o`
- `exp_pkt_len_o`

### M3 到 M1

- `busy_o`
- `done_o`
- `format_ok_o`
- `length_error_o`
- `type_error_o`
- `chk_error_o`
- `res_pkt_len_o`
- `res_pkt_type_o`
- `res_payload_sum_o`
- `res_payload_xor_o`

M1 是软件接口，M2 是存储器，M3 是处理核心，`ppa_top` 主要负责连线、时钟和复位分发。
