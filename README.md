# 企业真实命题赛道 - 弦养 - 锻五音

### 弦养 · 弦上养生

> **跟练八段锦，做一个对的动作就拨响一根古琴弦；练完一套八段锦 = 弹完一曲古琴。**

手机浏览器 H5。八段锦 8 式 × 古琴七弦：前 7 式各一弦，第 8 式「背后七颠百病消」
= 七弦齐鸣。

| 步 | 用户动作 | 屏幕反应 |
|---|---|---|
| 1 | 打开网址 | 正中一个大按钮「开始练」 |
| 2 | 点它 | 授权摄像头 → 实时画面 + 白色骨架在动 |
| 3 | 做「双手托天理三焦」 | 做到位 → 弦位亮起 + 一声古琴响 |
| 4 | 做完 8 式 | 第 8 式 → 七弦齐鸣 →「一曲完成」 |
| 5 | 回首页 | 能翻到今天这条打卡 |
| 6 | 拔网线再来一遍 | 照样跑（资源全部本地化） |

**技术栈**：Vite 6 + Vue 3.5 + vue-router 4 ｜ MediaPipe tasks-vision（33 点，本地 wasm + 模型）
｜ 自研八式判定器 + 骨骼稳定性滤波 ｜ 真实古琴散音采样（CC BY 4.0）｜ localStorage 打卡

---

## 文档地图

| 文档 | 什么时候看 |
|---|---|
| **`docs/QA清单.md`** | **先看这个**。D1–D10 逐项验收、谁验、证据文件、还剩哪几条没绿 |
| `docs/演示脚本.md` | 上台前 30 分钟：逐句台词 + 掐表表 + 合规红线 + 开演检查单 |
| `docs/真机实测记录.md` | 调阈值之前：6039 帧实测、滤波对比、阈值标定改法、真机问题清单 |
| `docs/手机访问指引.md` | 要拿手机开之前：https 端口、iOS 装证书 + 手动信任、D10 四人截图清单 |
| `certs/手机端访问指南.md` | iPhone 装证书的完整分步图（iOS 最细，别重写） |
| `SETUP.md` | 环境搭建与本机 npm 坑（`NODE_OPTIONS=""`、`--ignore-scripts`） |

---

## 起服务

```bash
export NODE_OPTIONS=""            # ★ 本机必须，否则文件删除被 safe-delete 拦
npm install --ignore-scripts      # ★ 本机必须，否则 esbuild postinstall 回滚 node_modules

npm run dev            # http://0.0.0.0:5173   —— 只有电脑自己能开摄像头
npm run dev:https      # https://0.0.0.0:5174  —— ★ 手机演示走这条
npm run build && npm run preview
```

手机开摄像头**必须 https**：访问 `https://192.168.1.209:5174`（换网请重查 IP，
见 `docs/手机访问指引.md`）。iPhone 装证书后还要在
「设置 → 通用 → 关于本机 → 证书信任设置」里**手动打开信任**。

---

## 自测

```bash
npm run test:all       # 判定器 / SSR 渲染 / 音色 / 兜底切换 / 节点表 / 净化器
npm run build
```

| 脚本 | 管什么 |
|---|---|
| `src/engine/judge.test.mjs` | 八式判定器（正例 / 盲测 / 消融 / 复杂度定级） |
| `scripts/render.test.mjs` | 真 SSR 渲染断言 + 内容合规（禁词）+ 兜底接线回归 |
| `scripts/fallback.test.mjs` | 兜底第一级状态机：断识别 → 预录 ≤3s、video 缺席要炸出来 |
| `scripts/verify-cleaner.mjs` | 净化器 + 静立零误触发 |
| `scripts/tune-filter.mjs` | 滤波参数寻优（需 6039 帧原始数据） |

---

## 现场三级兜底

| 级 | 兜底 | 状态 |
|---|---|---|
| 一 | 识别失败 / 无人入镜 / 加载超时 → **3 秒内切 60s 预录视频**，式号计时打卡不中断 | 接线已完成；**视频文件待真人拍摄**（`public/assets/fallback.mp4`） |
| 二 | 换摄像头设备（`CamSettings.vue`） | 已实现 |
| 三 | 跳过本式 / 点按也算响 / 切手动模式 | 已实现 |

---

## 范围（刻意没有的东西）

没有后端、没有数据库、没有登录、没有 CDN、没有云同步、没有实时动作纠正、
没有第二套拳种。**「没有后端」是设计决定，不是没做完。**（音色已于 2026-10-02 换为真实古琴采样）
改动前先读 `docs/QA清单.md` 末尾的「范围冻结」。

---

## 仓库分工与补充资料（2026-11-10 整理）

本仓是**弦养（八段锦 × 古琴）的唯一代码仓库**。原先的合并仓 `xianyang-duanwuyin` 已归档停用（只读，最终删除待仓库管理员执行），电子衣柜项目独立在 `yida-wardrobe`；合并仓里剩下的队友互补资料已迁入本仓，不留第三份副本。

| 内容 | 位置 |
|---|---|
| 队友互补资料快照 | `supplementary/liaoyu-system/`（来源 `1660536805-spec/liaoyu-system`，说明见 `supplementary/README.md`） |
| 文化内核实验室（可插拔处方） | `src/culture/` + 路由 `#/culture` |
| 宣纸插画卡七步问卷 | `src/views/OnboardingV2.vue` + 路由 `#/onboarding-v2`（URL 带 `?ia=art` 时自动分流） |
| 最终版 UI 原型壳 | `public/prot/` + 路由 `#/prot` |
| 设计交付位 | `design/{ui,tokens,spec,motion}/` |
| 交付包 | `package-for-team/` |

**2026-11-10 修复记录**：以 `07e764e`「最终版设计稿 × 真引擎融合」为准补回 27 个文件，恢复 `/culture`、`/onboarding-v2`、`/prot` 三条路由与 `?ia=art` 分流；`public/prot/art/` 的 12 张重复素材改为引用 `public/art/`，省 2.58 MiB。
