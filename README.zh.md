<div align="center">

# 📺 屏宝 (Pingbao / 핑바오)
### 企业级云端数字标牌集中管理平台 (CMS)

**屏宝 (Pingbao)** 是一款专为大规模商用智能显示屏量身定制的企业级数字标牌管理平台。  
支持多终端远程深度控制、时间轴可视化排期编排以及断网永续自主离线播放。

[![Language](https://img.shields.io/badge/Language-简体中文%20|%20English%20|%20한국어-brightgreen.svg)](#)
[![Release](https://img.shields.io/github/v/release/pingbaosignage/pingbao-community?color=blue)](https://github.com/pingbaosignage/pingbao-community/releases)
[![Issues](https://img.shields.io/github/issues/pingbaosignage/pingbao-community)](https://github.com/pingbaosignage/pingbao-community/issues)
[![License](https://img.shields.io/badge/license-Proprietary-orange.svg)](#)

<br/>

**[ 简体中文 ]** | **[ English ](README.en.md)** | **[ 한국어 ](README.md)**

<br/><br/>

<img src="docs/assets/zh/dashboard.png" alt="屏宝系统仪表盘" width="100%" />
<sub><b>▲ 屏宝 (Pingbao) 运行仪表盘：屏幕在线率、排期下发进展及系统资源总览</b></sub>

</div>

---

## 🌟 核心特性 (Key Features)

* **硬件标准化与底层深度远控**
  * 原生深度兼容三星智能商用标牌 (Tizen 6.5 / SSSP 10+)
  * 支持屏幕背光面板远程开/关、音量调节、定时软硬件重启及实时播放画面回传截屏
* **断网无忧·永续自主离线轮播 (Autonomous Offline Loop)**
  * 现场网络长期中断时，终端凭借本地机身闪存缓存按排期计划 24/7/365 永续无缝轮播
  * 支持 24 小时兜底常态播放 (Base Fallback Loop) 与特定高峰时段高优先级插播排期
* **高性能媒体资产与播放列表管道**
  * 支持超高清大容量视频 (MP4, WebM) 及图像素材流式并发上传
  * 具备魔数 (Magic Bytes) 二进制签名校验，杜绝损坏文件下发
* **响应式管理控制台 & 三国语言实时切换**
  * 完美适配大屏中央控制室大屏与一线巡检工程师移动端操作界面
  * **简体中文、English、한국어** 3 种语言毫秒级无缝切换
* **经过生产检验的轻量高并发架构**
  * 基于 PostgreSQL + Redis + MinIO/S3 组合
  * 支持私有化物理部署、群晖 NAS (Docker) 及主流公有云环境

---

## 📸 核心系统界面全景 (中文操作界面)

| 运行仪表盘 (Dashboard) | 设备集中管理 (Fleet Management) |
| :---: | :---: |
| <img src="docs/assets/zh/dashboard.png" width="100%" /> | <img src="docs/assets/zh/device-management.png" width="100%" /> |
| **实时运维监看 (Operations / Monitoring)** | **时间轴排期调度 (Timeline Scheduling)** |
| <img src="docs/assets/zh/monitoring.png" width="100%" /> | <img src="docs/assets/zh/schedules.png" width="100%" /> |

---

## 📖 文档中心 (Documentation)

* 📘 [用户使用手册 (User Guide)](docs/zh/user-guide.md) - 设备登记绑定、媒体上传、播放列表制作、时间轴排期
* 🛠️ [管理员与私有化部署指南 (Admin Guide)](docs/zh/admin-guide.md) - Docker 环境要求、环境变量配置、平滑升级与回退
* ❓ [常见问题解答 (FAQ)](docs/zh/faq.md) - 断网离线播放原理、支持硬件型号与故障排查
* 📜 [版本更新记录 (Changelog)](CHANGELOG.md) - 历史版本发布日志与改进详情

---

## 💡 提交缺陷与需求建议 (Issues & Requests)

如果您在使用屏宝过程中发现任何软件缺陷，或有任何新的业务功能建议，欢迎随时提交 Issue（**支持中文、English、한국어**）：

* [🐛 提交缺陷反馈 (Bug Report)](https://github.com/pingbaosignage/pingbao-community/issues/new?template=bug_report.md)
* [💡 提交功能建议 (Feature Request)](https://github.com/pingbaosignage/pingbao-community/issues/new?template=feature_request.md)

---

## 🚀 官方版本下载 (Releases)

前往 **[Releases](https://github.com/pingbaosignage/pingbao-community/releases)** 页面查看版本发布说明并下载最新升级包。
