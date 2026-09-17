# Mainland China sourcing and fabrication

[简体中文版](china-sourcing.zh-CN.md)

This is the recommended prototype route for a builder in mainland China. Do
not place the PMW3610 holder order until its STL has been prototyped and marked
ready in this repository.

## 1. Keyboard PCBs through 嘉立创/JLCPCB

Download these four production archives directly from the upstream
`keyball39/design_data` directory:

- `Keyball39_BallSide_MiddlePCB.zip`
- `Keyball39_BallSide_TopPCB.zip`
- `Keyball39_NoBallSide_MiddlePCB.zip`
- `Keyball39_NoBallSide_TopPCB.zip`

Source: <https://github.com/Yowkees/keyball/tree/main/keyball39/design_data>

Upload each ZIP as a separate two-layer PCB order. Do not combine the four ZIP
files and do not upload the KiCad source ZIP as production data. Use these
starting selections unless the Gerber viewer or fabrication review indicates
otherwise:

- FR-4, two copper layers;
- 1.6 mm board thickness;
- 1 oz copper;
- black solder mask;
- white silkscreen;
- HASL lead-free or ENIG surface finish;
- no PCB assembly service.

Inspect all four jobs in the online Gerber viewer. Confirm the board outline,
internal cutouts, non-plated holes, and dimensions before paying. Select the
production-file confirmation option if available. Fabricators manufacture
from the Gerbers and will not infer requirements from PDFs or notes bundled
beside them.

The top PCBs are structural switch plates, while the middle PCBs contain the
electrical circuit. A normal prototype order will leave spare copies because
the fabrication minimum is usually greater than one.

## 2. Acrylic through 淘宝 laser cutting

Use these two upstream files:

- `Keyball39_MiddleAcryl_3mm_98x187mm.dxf`
- `Keyball39_BottomAcryl_2mm_136x185mm.dxf`

Useful marketplace search terms:

- `亚克力 激光切割 DXF 来图定制`
- `黑色磨砂亚克力板 激光切割`

Tell the vendor:

> DXF 按毫米单位 1:1 切割，不缩放；中间层 3mm，底板 2mm。请保留全部孔位和内部轮廓，先发排版图确认。

Ask for an image of the nested cutting layout before production. The thickness
is part of the mechanical stack and should not be substituted without also
changing the spacer lengths.

## 3. Electronic and mechanical parts

Useful 淘宝/1688 search phrases are listed below. Verify dimensions and pinout
from the listing rather than ordering by title alone.

| Part | Search phrase | Acceptance check |
| --- | --- | --- |
| Controller | `nice nano v2 nRF52840 ProMicro ZMK` | Pro Micro footprint, UF2 bootloader, battery pads |
| Controller sockets | `圆孔排母 2.54 低矮 12P` | Correct pin diameter and sufficient clearance for battery |
| Socket pins | `康斯特针 2.54 键盘 nice nano` | Fits the selected round sockets |
| Battery | `301230 3.7V 100mAh 带保护 锂聚合物` | Protection circuit, dimensions, and marked polarity |
| Power switch | `贴片拨动开关 SPST 键盘` | Mechanically accessible and latching |
| OLED | `0.91寸 OLED 12832 SSD1306 I2C 4针 3.3V` | Address 0x3C and correct VCC/GND/SCL/SDA order |
| PMW3610 board | `PMW3610 成品模块 已焊接 已测试 LM18-LSI 3.3V ZMK` | Sensor and lens installed; VIN/GND/SCLK/SDIO/nCS/MOTION exposed; seller supplies pinout and dimensions |
| Ball | `34mm 轨迹球 球体` | Actual diameter 34 mm and patterned surface |
| Bearings | `2mm 氧化锆 陶瓷球` | Grade and diameter consistent; buy several spares |
| MX sockets | `凯华 MX 热插拔轴座` | Kailh-style PCB hot-swap footprint |
| Diodes | `1N4148W SOD-123` | SOD-123 package with visible cathode marking |
| Switches | `MX 机械键盘轴 5脚` | 39 matching switches plus spares |
| Keycaps | `1U 等高 MX 键帽 39键` | MX cross stem; uniform profile preferred |
| Fasteners | `M2 铜柱 7mm 9mm M2螺丝` | Female-female spacers and matching short screws |

Prefer genuine nice!nano v2 controllers for the first build. If clones are
used, verify their battery-pad polarity, charger circuit, bootloader, GPIO
mapping, and physical component clearance before connecting a LiPo.

Do not buy listings described as `裸板`, `散件`, `传感器+镜头`, or `需自行焊接`.
Ask the seller to confirm that the PMW3610 and LM18-LSI are installed and that
motion output has been tested at 3.3 V. Request clear photos of both sides, the
pinout, PCB dimensions, mounting-hole positions, and lens-to-PCB height.

A populated compact ufan module is preferred, but another fully assembled
board is acceptable after its electrical interface has been checked. Do not
order the printed sensor carrier before choosing the exact board: its outline,
mounting holes, and lens height determine the carrier geometry.

## 4. Trackball holder through 嘉立创3D打印

The finished holder will be uploaded as an STL. For the first dimensional
prototype, order one SLA resin part. The design should use at least 1.5 mm
around clips, bearing pockets, and screw features. Measure the returned part
and test ball movement and sensor tracking before ordering another material.

Once the geometry is proven, MJF PA12 nylon is preferable for a tougher final
part, but its dimensional tolerance is looser than SLA. Bearing pockets and
sensor height must therefore include intentional clearance rather than relying
on a press fit.

Suggested order note:

> 关键装配件，请勿自动缩放。孔位和传感器高度为功能尺寸；支撑请尽量放在非配合面，后处理不要打磨轴承孔和传感器定位面。

Do not select automatic model repair if the service reports a geometry error;
fix the source model instead so functional dimensions are not silently
changed.

## 5. Tools

Minimum useful bench equipment:

- temperature-controlled soldering iron with a small chisel tip;
- 0.5-0.8 mm electronics solder and flux;
- fine tweezers and flush cutters;
- digital multimeter with continuity mode;
- USB-C data cable;
- Kapton tape or other electrical insulation;
- safety glasses;
- small Phillips driver and M2 hardware tools.

Do not buy a PMW3360 sensor board or Pro Micro ATmega32U4 controllers for this
wireless build. Do not install the original TRRS sockets or RGB LEDs.
