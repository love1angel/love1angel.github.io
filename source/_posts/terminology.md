---
title: terminology
date: 2024-05-16 02:59:59
categories:
- terminology
tags:
---

常用术语。车载领域

<!-- more -->

NCAP: 新车评价规范

## IDL

Franca IDL: Interface Definition Language. 接口定义语言，fidl

## 汽车: automotive

燃油车的三大件：发动机，变速箱和底盘
电车的几大核心：电池，电机，电控和底盘

### common

OEM: Original Equipment Manufacturer. 整车厂：宝马，蔚小理
Tier1: 一极供应商：博世

SoC: System on Chip. 片上系统，座舱芯片，通常有高通 8155，8295

ESP: Electronic Stability Program. [车身电子稳定控制系统](https://baike.baidu.com/item/%E8%BD%A6%E8%BA%AB%E7%94%B5%E5%AD%90%E7%A8%B3%E5%AE%9A%E7%B3%BB%E7%BB%9F/706067?fromtitle=esp&fromid=9251372)
EBD: Electrical Brake Distribution. 电子刹车分配力系统
ABS: Antilock Brake System. 防抱死制动系统
TCS: Traction Control System. 循迹控制系统
VDC: Vehicle Dynamic Control. 车辆动态控制系统

HU: Head Unit
BCM: Body Control Module 车身控制模块。例如车窗，门锁等等

- EMS: Engine Mangement System. 发动机管理系统（燃油车）
- VCU: Vehicle Control Unit. 整车控制器（电车）
- HCU: Hybrid Control Unit. 混动车

- GNSS: Global Navigation Satellite System. 全球卫星导航系统。有 GPS，北斗
- T-BOX: telematic box. 负责车联网的通信组件

---
燃油车

TCU: Transmision Control Unit. 自动变速箱控制单元

---
电车

BMS: Battery Management System. 电池管理系统

### 协议

#### 软件

BSW: basic software

ODX(ISO 22901-1:2008): Open Diagnostic Data Exchange. （车辆）开放式诊断数据交换协议

PDX:

#### 诊断

OBD: On Board Diagnostics 口，通常在主驾舱下方，用来诊断

#### 以太网

some/ip

dds

doip

#### 功能安全

1. 主动安全
2. 被动安全

ASIL: Automotive Safety Integrity Level.

#### autosar

[aas](https://www.autosar.org/standards/adaptive-platform): Adaptive Autosar

[cas](https://www.autosar.org/standards/classic-platform): classic Autosar

[IVI](https://wayland.pages.freedesktop.org/weston/toc/ivi-shell.html): in vehicle infotainment

### 组织

COVESA(前身是 GENIVI): Connected Vehicle Systems Alliance。是一个非营利性的汽车行业联盟，致力于开发集成网联汽车中的操作系统和中间件以及相关云服务的参考方法。开发了知名的 vsomeip, capi, dlt 等

[github 地址](https://github.com/COVESA)

### 部件

MCU: Micro Controller Unit 微控制单元

->

ECU: Electronic Control Unit 电子控制单元，通常放在铝盒里

## 自动驾驶 autonomous driving

TOPS: Tera Operations Per Second。一秒钟进行一万亿次 10^12 次方

英伟达 orin -> thor

- CC(CCS): Cruise Control (System). 定速巡航（保持定速行驶）
- ACC(ACCS): Adaptive Cruise Control (System). 自适应巡航
- LCC(LCCS): Intelligent Cruise Control (System). 智能领航系统

LKA: Lane Keeping Assist. 车道保持辅助

AEB: Autonomous Emergency Braking. 自动紧急制动

## 软件工程

backward compatibility 向后兼容，回溯兼容。兼容旧版本

forward compatibility 向前兼容，前瞻兼容。考虑未来还能不能用

E2E: exchange to exchange

E2E test: end to end test

SMP (Symmetric multiprocessing) : 对称多处理

## 图像，FFMPEG 雷神

RGB (Red Green Blue):

- 加色法
- 常用于图像处理和显示
- 每个像素由红绿蓝三个分量表示
- 表示通常用 8 位 [0, 255] 表示强度或亮度，([0, 1] 表示了相对强度或亮度)
- 每个像素点连续存储 R, G, B 的值，一般采取行优先存储

YUV (YCbCr)

- 亮度-色度编码
- 常用于视频压缩和传输中
- 将图像分解为亮度 Luminance（Y）和色度 Chrominance（U、V）两个分量，亮度表示图像的明暗信息，而色度表示颜色信息，这种分离有助于有效地压缩视频数据，因为亮度通常变化较小，而色度变化较大

其中 U V 分量有不同的采样率，Y 分量先存储，UV 分量交叉存储，或者分别存储为两个平面

YUV420, NV12, NV21
