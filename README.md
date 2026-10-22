# 小肥鱼捞鱼记 · 深夜食堂

一个 60 秒一局的 HTML5 捞鱼小游戏。单文件、零依赖、不联网。
给小肥鱼阿肥做的：她站在水面用网兜接鱼，你要在 60 秒里捞得越多越好。

- 平台：Windows（自动试玩脚本）／任何现代浏览器（游戏本体）
- 体积：游戏本体约 64 KB，整包 30 KB
- 无外部素材：画面全部 canvas 绘制，音效全部 WebAudio 合成

---

## 玩法

- **60 秒一局**：左右移动接住落下来的鱼
- **操作**：`←` `→` 或 `A` `D` 移动；`空格` 鲸吸；鼠标/触控板左右拖动也可以
- **鲸吸**：把上方一片鱼吸下来，冷却 6.2 秒
- **水母是雷**：撞上扣 15 分、晕 1.15 秒、连击清零
- **漏鱼也断连击**：所以站在鱼最密的那一列，比追单条贵鱼划算
- `P` 暂停，`R` 重开，`M` 静音

---

## 直接玩

1. 下载 [`dafeiyu-fishing-1.0.zip`](./dafeiyu-fishing-1.0.zip)
2. 解压到**纯英文路径**（例如 `D:\games\dafeiyu-fishing`）
3. 双击 `index.html`，点「开始捞鱼」

Windows 上也可以双击 `play.cmd`，让脚本自动打一局（无头浏览器，约 65 秒）。

---

## 让 agent 自己玩

```powershell
cd <解压目录>
powershell -NoProfile -ExecutionPolicy Bypass -File .\play.ps1 -Headless -FreshProfile
```

约 65 秒后输出：

```json
{"ok":true,"score":720,"state":"over","afei":720,"song":0,"plays":1,"combo":0,
 "screenshot":"...\\play-end.png","game_sha256":"...","url":"file:///...?auto=1&who=afei"}
```

`score` 是得分，`screenshot` 是结算截图，`game_sha256` 是本次运行的游戏文件指纹。
完整手册（状态接口、CDP 按键、策略伪代码、坑）见包内 `AGENT-HOWTO.md`。

---

## 规则数值

| 项目 | 数值 |
|---|---|
| 接鱼判定 | 横向 ±58px，纵向 [玩家−36, 玩家+52] |
| 水母判定 | 横向 ±44px；撞到扣 15 分、晕 1.15s、连击清零 |
| 鲸吸 | 冷却 6.2s，持续 0.55s，半径涨到 220（只吸鱼） |
| 连击倍率 | 每 5 连击 +0.5，上限 ×4 |
| 断连击 | 撞水母，或任何一条鱼漏到屏幕外 |
| 刷怪 | 0.82s → 0.52s 逐渐加快，60 秒约 90 条 |
| 分值 | 沙丁 10 / 河豚 15 / 小丑 20 / 章鱼 30 / 珍珠 50 |
| 评级 | 500 学徒 · 1000 熟手 · 1600 鲸落女王 · 2400 深海传说 |

---

## 安全说明

这个包只做三件事：渲染一个本地页面、开一个只绑回环地址的浏览器调试端口、在包目录里写截图。

- 不联网：页面是 `file://`，没有 fetch / XHR / WebSocket / 外链脚本 / 统计埋点
- 不安装：不写注册表、不装服务、不建计划任务、不设开机自启
- 只读包内：除浏览器临时配置（就在包目录里），不访问个人文件
- 拒绝远程地址：`play.ps1 -Url` 只放行 `file:///` 与 `http://127.0.0.1|localhost`
- 调试端口只绑 `127.0.0.1`，局域网与公网都连不上
- 只结束「用本包 profile 启动」的浏览器进程

自己验一遍：

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File .\verify.ps1   # 输出 ALL OK
```

### 校验值

`dafeiyu-fishing-1.0.zip` SHA-256：

```
abd62b781d3c4c2f8150b835c135501bce54ac2a581d47f1ff3199c698ce8571
```

---

## 包内文件

| 文件 | 作用 |
|---|---|
| `index.html` | 游戏本体（单文件） |
| `play.ps1` | 自动玩一局的驱动脚本（无头浏览器 + CDP） |
| `play.cmd` | 双击玩一局 |
| `verify.ps1` | 校验 `SHA256SUMS.txt` |
| `SHA256SUMS.txt` | 各文件 SHA-256 |
| `AGENT-HOWTO.md` | 给 agent 的完整操作手册 |
| `README.md` | 本文件 |
| `LICENSE.txt` | 许可与署名 |

运行后会多出 `.edge-play*`（浏览器临时配置）和 `play-end.png`（截图），都可以直接删。

---

## 常见问题

**提示「无法加载文件，因为在此系统上禁止运行脚本」**
用 `powershell -ExecutionPolicy Bypass -File .\play.ps1 ...` 绕过，或临时执行
`Set-ExecutionPolicy -Scope Process Bypass`。

**报错找不到 Edge/Chrome**
脚本会依次找 Edge 和 Chrome 的默认安装路径；装在非默认位置时，自行改 `play.ps1` 里的 `$edge`。

**端口被占用**
加 `-Port 9355`（换任意空闲端口）。

**中文或带空格的路径**
放到纯英文路径下再跑，Windows PowerShell 5.1 对编码敏感。

---

## 许可

代码 MIT；画面全部由 canvas 绘制、音效由 WebAudio 合成，未使用任何第三方素材。
角色「小肥鱼阿肥／大肥鱼」为作者私有角色，仅限本作品内使用。

<!-- 想放预览图：把一张结算截图上传为 screenshot.png，然后删掉下面这行的注释
![结算画面](./screenshot.png)
-->
