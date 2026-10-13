---
name: jianghuai-td-algorithm
description: TD (Thermal Demand) 热负荷需求算法 - 数学公式、物理含义、标定量详解
trigger: 当用户询问TD算法、TD计算公式、热负荷需求、车内温度计算
---

# TD (Thermal Demand) 热负荷需求算法详解

> 基于江淮汽车 TMS220 供应商技术输入文件  
> **来源**: `TMS220标定量整理表白名单.xlsx` - Sheet: `TD相关（含hi&lo）`

---

## 1. TD 物理含义

### 1.1 定义

**TD (Thermal Demand)** 表示**热负荷需求**，即空调系统需要消除（制冷）或补充（制热）的热量。

### 1.2 物理意义

```
物理意义：让车内温度达到设定温度所需的热量/冷量
单位：W/℃ (每摄氏度对应的瓦数) 或 m² (等效得热面积)
```

### 1.3 状态判断

| TD值 | 状态 | 系统动作 |
|------|------|---------|
| TD > 0 | 正值 | 需要制冷 |
| TD < 0 | 负值 | 需要制热 |
| TD ≈ 0 | 接近0 | 温度适宜，维持现状 |

---

## 2. TD 完整数学公式

### 2.1 总量公式

```
TD_total = TD_inner + TD_outer + TD_comfort + TD_sun + TD_defog + TD_other
```

### 2.2 分量展开

```
TD_total = f(T_cabin) + f(T_ambient) + f(T_set) + f(Solar) + f(Defog) + ...
```

---

## 3. 各分量详解

### 3.1 内温TD (车内温度贡献)

**来源**: `TMS220标定量整理表白名单.xlsx` - `TD相关` Sheet

**物理含义**：车内温度与设定温度的差值所产生的热负荷

**数学公式**：
```
TD_inner = (T_cabin - T_set) × K_inner

其中：
- T_cabin: 车内实际温度 (℃)
- T_set: 用户设定温度 (℃)
- K_inner: 车内温度对应子TD系数 (W/℃)
```

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CalModMgt_HVACMode_TinTd_Linrzd_Curve_X` | 输入车内头部温度 (℃) |
| `CalModMgt_HVACMode_TinTd_Linrzd_Curve_Y` | 室内温度对应子TD (W/℃) |

**数据来源**：
```
观测量: CMM_stDrvrCbnTD_U16 (主驾), CMM_stPassCbnTD_U16 (副驾)
        CMM_st2cdRowTotTD (二排左), CMM_stRi2cdRowTotTD (二排右)
        CMM_st3rdRowTotTD (三排)
```

**示例**：
```
车内温度 30℃，设定温度 24℃，系数 10 W/℃
TD_inner = (30 - 24) × 10 = 60 W/℃ (需要制冷)
```

---

### 3.2 外温TD (环境温度贡献)

**物理含义**：环境温度与车内温度的差值，通过车身传热产生的热负荷

**数学公式**：
```
TD_outer = (T_ambient - T_cabin) × K_outer

其中：
- T_ambient: 环境温度 (℃)
- T_cabin: 车内温度 (℃)
- K_outer: 环境温度对应子TD系数
```

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CalModMgt_HVACMode_ToutTd_Linrzd_Curve_X` | 输入环境温度 (℃) |
| `CalModMgt_HVACMode_ToutTd_Linrzd_Curve_Y` | 环境温度对应子TD (W/℃) |

**数据来源**：
```
观测量: CMM_stAmbTD_U16 (环境温度)
```

---

### 3.3 舒适性TD (设定温度贡献)

**物理含义**：根据用户设定的舒适温度产生的热负荷需求

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CalModMgt_HVACMode_ComTD_Curve_X_C` | 环温 (℃) |
| `CalModMgt_HVACMode_ComTD_Curve_Y_C` | 舒适性设定温度对应TD |
| `CalModMgt_HVACMode_DrvrTSetTd_Linrzd_Curve_X` | 舒适性设定温度 (℃) |
| `CalModMgt_HVACMode_DrvrTSetTd_Linrzd_Curve_Y` | 舒适性设定温度对应子TD (W/℃) |

**数据来源**：
```
观测量: CMM_stCmftTD_U16 (舒适性TD)
```

---

### 3.4 日照TD (太阳辐射贡献)

**物理含义**：太阳辐射通过车窗进入车内的热量

**数学公式**：
```
TD_sun = Solar_Radiation × Effective_Area

其中：
- Solar_Radiation: 日照强度 (W/m²)
- Effective_Area: 等效得热面积 (m²)
```

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CalModMgt_HVACMode_TdLeSun_Linrzd_Curve_X` | 输入光照强度百分比 |
| `CalModMgt_HVACMode_TdLeSun_Linrzd_Curve_Y` | 主驾光照强度对应TD (m²) |
| `CalModMgt_HVACMode_TdReSun_Linrzd_Curve_X` | 输入光照强度百分比 |
| `CalModMgt_HVACMode_TdReSun_Linrzd_Curve_Y` | 副驾光照强度对应TD (m²) |

**后排日照TD**：
| 标定量名称 | 说明 |
|-----------|------|
| `CMM_TdReHvacSun_Linrzd_Curve_Y` | 二排、三排左输入光照强度百分比 |
| `CMM_TdReHvacSun_Linrzd_Curve_X` | 二排、三排左光照强度对应TD (m²) |
| `CMM_TdRiReHvacSun_Linrzd_Curve_Y` | 二排、三排右输入光照强度百分比 |
| `CMM_TdRiReHvacSun_Linrzd_Curve_X` | 二排、三排右光照强度对应TD (m²) |

---

### 3.5 日照修正 (根据环境温度)

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CalModMgt_HVACMode_ReSunCorre_Curve_X` | 光照强度测量值 |
| `CalModMgt_HVACMode_ReSunCorre_Curve_Y` | 外界阳光强度——副驾阳光 |
| `CalModMgt_HVACMode_LeSunCorre_Curve_X` | 光照强度测量值 |
| `CalModMgt_HVACMode_LeSunCorre_Curve_Y` | 外界阳光强度——主驾阳光 |
| `CalModMgt_HVACMode_SunTdCorByAmbT_Curve_Y` | 根据环温修正阳光TD的Y轴——修正系数（前排） |
| `CalModMgt_HVACMode_SunTdCorByAmbT_Curve_X` | 根据环温修正阳光TD的X轴——环境温度 |

**后排**：
| 标定量名称 | 说明 |
|-----------|------|
| `CMM_ReSunTdCorByAmbT_Curve_Y` | 根据环温修正阳光TD的Y轴——修正系数（后排） |
| `CMM_ReSunTdCorByAmbT_Curve_X` | 根据环温修正阳光TD的X轴——环境温度 |

---

### 3.6 除霜除雾TD

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CalModMgt_HVACMode_DefTaTd_Curve_X_C` | 前除霜时环境温度 (℃) |
| `CalModMgt_HVACMode_DefTaTd_Curve_Y_C` | 前除霜时修正TD |

**数据来源**：
```
观测量: CMM_stDefrstTD_U16 (除霜除雾TD)
```

---

## 4. TD 查表计算示例

### 4.1 内温TD查表

```
输入：车内温度 30℃
查表：CalModMgt_HVACMode_TinTd_Linrzd_Curve

X轴 (车内温度℃):  [15, 20, 25, 30, 35, 40]
Y轴 (TD系数W/℃):  [2,  4,  6,  10, 15, 20]

结果：当车内30℃时，TD系数 = 10 W/℃
```

### 4.2 日照TD查表

```
输入：日照强度 80%
查表：CalModMgt_HVACMode_TdLeSun_Linrzd_Curve

X轴 (日照%):     [0, 25, 50, 75, 100]
Y轴 (TD m²):     [0, 0.5, 1.0, 1.5, 2.0]

结果：日照80%时，TD = 1.6 m²
实际热负荷 = 800 W/m² × 1.6 m² = 1280 W
```

---

## 5. TD 与 TAO 的关系

### 5.1 控制链路

```
用户设定 Tset
      ↓
传感器采集：T_cabin, T_ambient, Solar, T_set
      ↓
TD 计算 = 各分量之和
      ↓
      ↓
TAO = Target Air-outlet Temperature (目标出风温度)
      ↓
控制执行器：风机、风门、压缩机、PTC
      ↓
实际出风温度 → 反馈 → 调整TD系数
```

### 5.2 TD → TAO 映射

**数学公式**：
```
TAO = T_cabin - TD_total / Heat_Transfer_Coefficient

或者

TAO = Target_Water_Temp - TD_total / Air_Flow_Rate

其中：
- Target_Water_Temp: 目标水温 (PTC/WTC)
- Air_Flow_Rate: 风机风量 (m³/h)
```

---

## 6. 多温区TD补偿

### 6.1 副驾对主驾补偿

**来源**: `TMS220标定量整理表白名单.xlsx` - `多温区补偿` Sheet

**标定量**：
| 标定量名称 | 说明 |
|-----------|------|
| `CMM_ReHvacCoolDeltaTd4Driv_Curve_data` | 纵轴：主驾-副驾设定温度；横轴：环温；输出：对主驾的TD补偿（加法）——制冷 |
| `CMM_ReHvacHeatDeltaTd4Driv_Curve_data` | 纵轴：主驾-副驾设定温度；横轴：环温；输出：对主驾的TD补偿（加法）——制热 |
| `CMM_ReHvacVentDeltaTd4Driv_Curve_data` | 纵轴：主驾-副驾设定温度；横轴：环温；输出：对主驾的TD补偿（加法）——通风 |

### 6.2 二排对主驾补偿

| 标定量名称 | 说明 |
|-----------|------|
| `CMM_ArdReHvacCoolDeltaTd4Driv_Curve_data` | 二排制冷对主驾TD补偿 |
| `CMM_ArdReHvacHeatDeltaTd4Driv_Curve_data` | 二排制热对主驾TD补偿 |
| `CMM_ArdReHvacVentDeltaTd4Driv_Curve_data` | 二排通风对主驾TD补偿 |

---

## 7. 内温估算相关标定

### 7.1 温度变化速率

**来源**: `TMS220标定量整理表白名单.xlsx` - `内温估算` Sheet

| 标定量名称 | 说明 |
|-----------|------|
| `CMM_HvacCorPrtIncLim_C` | 副驾头部温度计算的出风温度修正部分上升速率 |
| `CMM_HvacCorPrtDecLim_C` | 副驾头部温度计算的出风温度修正部分下降速率 |
| `CMM_SunCorPrtIncLim_C` | 副驾头部温度计算的阳光修正部分上升速率 |
| `CMM_SunCorPrtDecLim_C` | 副驾头部温度计算的阳光修正部分下降速率 |
| `CMM_InrHtCorPrtIncLim_C` | 副驾头部温度计算的内热源修正部分上升速率 |
| `CMM_InrHtCorPrtDecLim_C` | 副驾头部温度计算的内热源修正部分下降速率 |
| `CMM_PassCbnTIncLim_C` | 副驾头部温度上升速率 |
| `CMM_PassCbnTDecLim_C` | 副驾头部温度下降速率 |
| `CMM_PassCbnTUpLim_C` | 副驾头部温度的上限 |
| `CMM_PassCbnTDownLim_C` | 副驾头部温度的下限 |

---

## 8. 典型数值示例

### 8.1 夏季暴晒工况

```
工况：夏季中午，暴晒
- 环境温度 Tam: 38℃
- 车内温度 Tr: 50℃
- 设定温度 Tset: 24℃
- 日照 Ts: 900 W/m² (90%)

计算：
TD_内温 = (50-24) × 12 = 312 W/℃
TD_外温 = (38-50) × 8 = -96 W/℃  (负值表示外界向车内传热)
TD_日照 = 900 × 1.8 = 1620 W
TD_舒适 = f(24) = 50 W/℃

总TD = 312 + (-96) + 1620 + 50 ≈ 1886 W/℃ (强制冷需求)
```

### 8.2 冬季温和工况

```
工况：冬季阴天
- 环境温度 Tam: 5℃
- 车内温度 Tr: 10℃
- 设定温度 Tset: 22℃
- 日照 Ts: 0 W/m²

计算：
TD_内温 = (10-22) × 8 = -96 W/℃ (负值，需要制热)
TD_外温 = (5-10) × 6 = -30 W/℃
TD_日照 = 0
TD_舒适 = f(22) = -40 W/℃

总TD ≈ -166 W/℃ (制热需求)
```

---

## 9. 数据来源汇总

### 9.1 主要来源

| 文件 | Sheet | 内容 |
|------|-------|------|
| TMS220标定量整理表白名单.xlsx | TD相关（含hi&lo） | TD计算核心标定量 |
| TMS220标定量整理表白名单.xlsx | 多温区补偿 | 副驾/二排/三排补偿 |
| TMS220标定量整理表白名单.xlsx | 内温估算 | 温度变化速率 |

### 9.2 关键观测量

| 观测量 | 说明 |
|--------|------|
| CMM_stDrvrCbnTD_U16 | 主驾座舱TD |
| CMM_stPassCbnTD_U16 | 副驾座舱TD |
| CMM_stAmbTD_U16 | 环境温度TD |
| CMM_stCmftTD_U16 | 舒适性TD |
| CMM_stDefrstTD_U16 | 除霜除雾TD |

---

## 10. 快速参考

```
TD = TD_inner + TD_outer + TD_comfort + TD_sun + TD_defog

TD > 0 → 制冷需求
TD < 0 → 制热需求

核心标定量：
- 内温TD: CalModMgt_HVACMode_TinTd_Linrzd_Curve
- 外温TD: CalModMgt_HVACMode_ToutTd_Linrzd_Curve
- 日照TD: CalModMgt_HVACMode_TdLeSun_Linrzd_Curve
- 舒适性TD: CalModMgt_HVACMode_ComTD_Curve
- 除雾TD: CalModMgt_HVACMode_DefTaTd_Curve
```

---

## 参考资料

1. **TMS220标定量整理表白名单.xlsx** - 江淮汽车供应商技术输入文件
2. **TAO算法核心原理** - 自动空调控制基础
3. **热泵系统控制策略** - 冷媒与水温控制
