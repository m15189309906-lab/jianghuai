---
name: jianghuai-tms220-calibration
description: 江淮汽车 TMS220 热管理系统标定数据 - 热泵控制、TD计算、风机水泵标定量
trigger: 当用户询问TMS220标定量、江淮热泵边界参数、TD算法相关标定
---

# Jianghuai TMS220 热管理系统标定数据

> 基于 TMS220 供应商技术输入文件

## 1. 标定数据概览

| Sheet名称 | 标定内容 | 行数 |
|-----------|---------|------|
| TD相关 | TD计算核心参数 | 163 |
| 热泵边界相关 | 热泵进入/退出条件 | 21 |
| 目标温度 | WTC/PTC水温控制 | 179 |
| 鼓风机 | 风机占空比控制 | 59 |
| 水泵 | 电池/电驱水泵控制 | 65 |
| 自动除雾 | 除雾策略 | 104 |
| 多温区补偿 | 副驾/二排/三排补偿 | 54 |
| 出风温度修正 | 风道温度修正 | 34 |
| 压缩机保护 | 停机保护标志位 | 32 |

---

## 2. TD (Thermal Demand) 热负荷计算

### 2.1 TD 组成

TD (Thermal Demand) 表示热负荷需求，由以下分量组成：

| TD分量 | 标定量 | 说明 |
|--------|--------|------|
| 内温TD | `CalModMgt_HVACMode_TinTd_Linrzd_Curve` | 车内温度对应子TD |
| 外温TD | `CalModMgt_HVACMode_ToutTd_Linrzd_Curve` | 环境温度对应子TD |
| 舒适性TD | `CalModMgt_HVACMode_ComTD_Curve` | 设定温度对应子TD |
| 日照TD | `CalModMgt_HVACMode_TdLeSun_Linrzd_Curve` | 日照辐射对应子TD |
| 除霜除雾TD | `CalModMgt_HVACMode_DefTaTd_Curve` | 前除霜时修正TD |

### 2.2 日照TD修正

| 标定量 | 说明 |
|--------|------|
| `CalModMgt_HVACMode_ReSunCorre_Curve` | 阳光修正（副驾） |
| `CalModMgt_HVACMode_LeSunCorre_Curve` | 阳光修正（主驾） |
| `CalModMgt_HVACMode_SunTdCorByAmbT_Curve` | 根据环温修正阳光TD |

---

## 3. 热泵边界控制

### 3.1 热泵进入条件

| 标定量 | 说明 |
|--------|------|
| `EMM_bCooltModReqByCabnBattByManSwt_C` | 热泵开关：0进，1退 |
| `EMM_tHpPermitTeEvnLimtLow_C` | 环境温度-极低温边界 |
| `EMM_tHpPermitTeEvnLimtLowDetaT_C` | 环境温度滞回-低温 |
| `EMM_tHpPermitTeEvnLimtHiUp_C` | 环温上边界 |
| `EMM_tHpPermitTeEvnLimtHiDwn_C` | 环温上边界滞回 |

### 3.2 余热回收边界

| 标定量 | 说明 |
|--------|------|
| `EMM_tHpPermitMotInLimHighForCoolg_M1d` | 余热回收和散热边界 |
| `EMM_tHpMotInHeatgReqMotInByTevn_degC_M` | 余热回收和吸热边界 |

---

## 4. 鼓风机控制

| 标定量 | 说明 |
|--------|------|
| `CalCtrlMgt_HVACCtrl_dytMaxBlr_C` | 鼓风机占空比上限 |
| `CalCtrlMgt_HVACCtrl_dytMinBlr_C` | 鼓风机占空比下限 |
| `CalCtrlMgt_HVACCtrl_dytTgtBlrUpLim_C` | 自动状态上升斜率 |
| `CalCtrlMgt_HVACCtrl_dytTgtBlrDownLim_C` | 自动状态下降斜率 |

---

## 5. 水泵控制

| 标定量 | 说明 |
|--------|------|
| `CalCtrlMgt_FrontCtrl_EWPBTardyt_Curve_Data` | BMS电池水流量请求 |
| `CCC_EWPBTgtDty4Heatg_Curve_Data` | 电池加热时TDU电池需求基础流量 |
| `CCC_EWPBTgtDty4Coolg_Curve_Data` | 电池冷却时TDU电池需求基础流量 |

---

## 6. 自动除雾

| 标定量 | 说明 |
|--------|------|
| `CalCH_AutDef_TempDiff_T1` ~ `T5` | 各风险等级温度差值 |
| `CalCH_AutDefLvl4Fan_C` | L4级风速 |
| `CalCH_AutDefLvl3Fan_C` | L3级风速 |

---

## 7. 多温区补偿

| 标定量 | 说明 |
|--------|------|
| `CMM_ReHvacCoolDeltaTd4Driv_Curve_data` | 副驾制冷对主驾TD补偿 |
| `CMM_ReHvacHeatDeltaTd4Driv_Curve_data` | 副驾制热对主驾TD补偿 |
| `CMM_ArdReHvacCoolDeltaTd4Driv_Curve_data` | 二排制冷补偿 |

---

## 参考

- TMS220 供应商技术输入文件
- TAO 算法核心原理
