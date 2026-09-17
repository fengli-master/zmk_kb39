# 无线 Keyball39 硬件方案

[English version](README.md)

本目录说明了如何制作与此仓库 ZMK 配置相匹配的第一版无线
Keyball39。

中国大陆的加工方式及电商搜索关键词请参阅
[中国大陆采购与加工指南](china-sourcing.zh-CN.md)。

## 整体架构

使用原版 Keyball39 Rev1 PCB 和定位板结构作为机械及按键矩阵基础。
将两个 Pro Micro 控制器更换为使用插座安装的 nice!nano v2，不安装
TRRS 连接器和 RGB LED，并使用 3.3 V PMW3610 成品模块代替 PMW3360
传感器板。

原版 PCB 与本固件使用相同的 Pro Micro 矩阵和 OLED 引脚，因此可以
兼容：

| 功能 | Pro Micro 标记 | nice!nano GPIO |
| --- | --- | --- |
| 第 0～5 列 | D4、D5、D6、D7、D8、D9 | P0.22、P0.24、P1.00、P0.11、P1.04、P1.06 |
| 第 0～3 行 | D21、D20、D19、D18 | P0.31、P0.29、P0.02、P1.15 |
| OLED SDA | D2 | P0.17 |
| OLED SCL | D3 | P0.20 |

右半边是 ZMK 分体键盘的中央端，并装有轨迹球。左右两边各自使用一块
电池和一个电源开关，通过 BLE 通信。不要安装或连接 TRRS 线。

## 设计源文件

原项目的 `keyball39/design_data` 目录包含可编辑的 KiCad 工程、生产用
Gerber 文件以及亚克力 DXF：

<https://github.com/Yowkees/keyball/tree/main/keyball39/design_data>

可打印的外壳 STL 位于：

<https://github.com/Yowkees/keyball/tree/main/keyball39/3D_Printer_STL>

这些文件采用 GPLv3 许可证。重新发布修改版本时，必须保留原作者署名并
遵守 GPLv3 条款。

必须使用已经完整焊接并完成功能测试的 PMW3610 成品模块。模块应包含
PMW3610 传感器、LM18-LSI 镜头和全部外围元件；本方案不接受裸 PCB 或
需要自行安装的传感器与镜头散件。优先选择已组装的 ufan 紧凑型模块，
因为它约 17 × 24.7 mm 的外形及建议的 1 mm PCB 厚度，比大型通用模块
更适合原版 Keyball 的垂直传感器空间：

<https://github.com/ufan/pmw3610_breakout>

卖家必须确认模块可使用 3.3 V，并引出 VIN、GND、SCLK、SDIO、nCS 和
MOTION。下单前取得带尺寸的图纸和引脚定义。不同 PMW3610 模块的机械
尺寸并不通用，因此必须先确定实际购买的模块，之后才能定稿轨迹球支架。
第一版仍使用独立传感器模块，无需重新设计键盘主 PCB。

## PMW3610 接线

固件引脚定义位于
`config/boards/shields/keyball_nano/keyball39_right.overlay`。

| PMW3610 模块 | nice!nano 引脚 | nRF52840 GPIO | 原版 PCB 信号 |
| --- | --- | --- | --- |
| VIN | VCC | 3.3 V | VCC |
| GND | GND | GND | GND |
| SCLK | D15 | P1.13 | SCLK |
| SDIO | D16 | P0.10 | MOSI |
| nCS | D10 | P0.09 | NCS |
| MOTION | D14 | P1.11 | MISO |

PMW3610 使用三线 SPI，因此 overlay 中 MOSI 和 MISO 特意使用同一个
P0.10 引脚。`MOTION` 是 P1.11 上的独立中断输入。本固件不使用传感器的
RESET 信号；请根据模块说明设置 RESET 跳线。

原版轨迹球侧 PCB 选择 ball-right 跳线配置后，可以通过七针传感器接口
传递以下信号：

| 七针接口针脚 | 第一版用途 |
| --- | --- |
| 1 | MOTION（PCB 网络标记为 MISO） |
| 2 | SDIO（PCB 网络标记为 MOSI） |
| 3 | GND |
| 4 | 3.3 V VCC |
| 5 | GND |
| 6 | nCS |
| 7 | SCLK |

连接传感器前，使用万用表通断档核对接口的每一个针脚与上表中的
nice!nano 引脚。原版 PCB 可以正反使用，跳线选择会改变接口走线，因此
不能只依赖丝印判断。

## 电源

第一版每边使用一块带保护板的 3.7 V、100～110 mAh、301230 锂聚合物
电池。控制器安装在圆孔排母中，使电池可以放在其下方且不会接触尖锐的
针脚。在电池正极线上串联实体电源开关。开关后的正极连接 nice!nano
`B+`，电池负极连接 `B-`。

左右两边分别通过各自的 nice!nano USB-C 接口充电。切勿将电池直接连接
到 `RAW`、`VCC` 或 GPIO。插入控制器前使用万用表确认极性。不要使用
鼓包、破损、没有保护板或极性相反的电池。

OLED 会缩短续航。为兼容当前固件应先保留 OLED；如果之后更看重续航，
可以在固件中将其禁用。

## 轨迹球机械模块

不能假定原版 PMW3360 支架能够正确定位 PMW3610。第一版需要定制打印
转接支架或球托，并在最终组装前确认：

- 34 mm 球体由三个 2 mm 陶瓷球轴承支撑；
- LM18-LSI 镜头对准轨迹球最低点；
- 传感器模块与光轴保持垂直；
- 镜头至球体的距离符合模块和镜头说明；
- 为 PCB、接口、导线以及取出球体留出空间；
- 使用 Keyball39 现有的两个轨迹球安装孔固定，且不让传感器 PCB 承受
  结构载荷。

不要在测试一个原型之前批量生产打印件。传感器高度最有可能需要反复
调整。

## 组装顺序

1. 使用上游生产文件订购一套原版 Keyball39 中层 PCB、顶层 PCB 和板材，
   并购买 [中文 BOM](bom.zh-CN.csv) 中的元件。
2. 第一阶段仅焊接二极管、热插拔轴座、复位按键、控制器插座、OLED
   插座、电池线和电源开关。不要安装 RGB 和 TRRS 元件。
3. 安装电池前为两块 nice!nano 刷入固件。
4. 短接轴座触点，分别测试左右两边的按键矩阵。
5. 配对 BLE 分体连接并测试全部 39 个按键。
6. 通过右侧 PCB 的七针接口连接 PMW3610 模块，在工作台上可靠支撑模块，
   然后测试指针移动。
7. 打样并调整轨迹球转接支架。
8. 所有电气测试通过后，再安装轴体、键帽、电池和底盖。

## 刷写固件

固件通过 GitHub Actions 构建，而不是在本地构建。工作流会生成左侧、
右侧以及 settings-reset UF2 文件。双击 nice!nano 的复位按钮，使其进入
引导盘模式，然后复制对应的 UF2 文件。

新控制器对的刷写顺序：

1. 两边都先刷入 `settings_reset`。
2. 左侧控制器刷入 left UF2。
3. 右侧控制器刷入 right UF2。
4. 关闭并重新打开两边的电源，然后让右半边与主机配对。

在确认控制器方向、电源轨通断以及没有短路之前，不要连接电池或传感器。
