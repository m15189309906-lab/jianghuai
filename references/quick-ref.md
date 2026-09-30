# 热管理标定 SOP - 快速参考

## 全栈数据流监控（必检）

### 环境输入层
- Tam / Env_Temp (外温)
- Tr / In_Temp (车内温度)
- Ts / Sunload (日照强度) ← 重点观察突变斜率
- Vsp / Veh_Speed (车速)
- Tset (设定温度)

### 控制计算层
- TAO ← 绝对核心
- TD_Sun (日照温差修正)
- TD_Vsp (车速修正)
- Target_Teo (目标蒸发器温度) ← 热泵节能灵魂

### 执行机构层
- Blow_Volt_Act (风机电压)
- Mode_Pos_Act (模式风门)
- Blend_Pos_Act (混合风门)
- P_Comp_Act (压缩机功率)
- P_PTC_Act (PTC功率)

## 春秋季问题解决

### 问题一：林荫道忽冷忽热
| 标定量 | 趋势 |
|--------|------|
| SunUpLimIncRate_C | ↓ 调小 |
| SunDownLimIncRate_C | ↓ 调小 |
| TdSunDiffMax_C | ↓ 调小 |
| Mode_Hysteresis | ↑ 调大 (1.5-3℃) |
| TAO→Blow曲线18-25℃区间 | 拉平 |

### 问题二：日照突变体感闷热
- 调整 SunTdCorNum_Curve 曲线
- 600 W/m² 处增加负向修正
- 目标：风量从 4.5V 升至 6.0V+

### 问题三：热泵换季节能
- TAO→Target_Teo 联动
- 春秋季 TAO 18-22℃ 时，Teo 设为 12-15℃
- 避免过低 Teo 导致混热风

## 三级除雾策略

| 级别 | 条件 | 动作 |
|------|------|------|
| 一级除湿 | RH>70% | 外循环+最大风机+满功率制冷 |
| 二级过渡 | RH 50-70% | 混合模式+中速 |
| 三级维持 | RH<50% | 内循环+低速+节能 |

## 能量优先级

免费热源 > 热泵 > PTC
- 电机余热 (优先)
- 电池余热
- 热泵 (COP 2-4)
- PTC (COP≈1, 最后手段)

## 露点计算

```
Td = T - (100 - RH) / 5

例: 20℃, 80% RH → Td = 20 - 4 = 16℃
玻璃温度 < 16℃ 即起雾
```
