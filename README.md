# BallPet · Luwi 浮标外观插件

给 Luwi 那颗浮标（ball）换一套画法：一份 `theme/ball.json` + 一组图 = 一套新的浮标外观，
**不写一行代码、也不用装第二个 APK**（`lw-plugin/1` 协议的 `art` 域）。

- **六个状态各一套配色**：待机淡蓝 / 正在听青绿 / 正在想琥珀 / 正在说紫 / 偷看粉 / 出错红
- **「正在听」是帧序列**：24 帧 12 fps 的脉冲动画，光晕一圈圈向外扩再收回，球体同时轻轻起伏
- **素材生成器是纯 Node**：自写 PNG 编码、零第三方依赖，没装 Python 的机器上也能改配色重跑

## 目录

```
.
├─ README.md
├─ LICENSE
├─ tools/build-art.mjs        素材生成器（纯 Node，零依赖）
└─ plugin/                    这个目录就是插件包的内容
   ├─ plugin.json             kind: art，零能力零工具
   ├─ README.md
   ├─ theme/ball.json         六个状态 + ring + frames
   └─ art/                    30 张 PNG（240×240 RGBA）
      ├─ ball-{idle,listening,thinking,speaking,peeked,failed}.png
      └─ listen/frame-00..23.png
```

## 跑一遍

```sh
node tools/build-art.mjs        # 重新生成 plugin/art/ 下的 30 张图
```

配色改 `tools/build-art.mjs` 里的 `STATES`；想换成别的尺寸、别的帧数，改 `SIZE` / `R` / `FRAME_COUNT`。

## 打包签名

签名工具与协议正文都在 [luwi-plugins](https://github.com/yuloong07-star/luwi-plugins) 仓库：

```sh
git clone https://github.com/yuloong07-star/luwi-plugins.git
node luwi-plugins/tools/lw-plugin-sign.mjs sign --dir plugin \
  --key <你自己的私钥> --out signed --zip signed/ballpet-1.0.0.lwp
```

**私钥不进仓库**。发版后把签好的 `.lwp` 放进 luwi-plugins 的 `packages/`
（文件名带版本号，**不要复用**：jsDelivr 对同名文件有很长的边缘缓存）。

## 装完怎么验

1. Luwi → 设置 → 插件 → **「浮标外观」** 那一行里能挑到 **BallPet**
2. 挑中之后球立刻换成琉璃球；说一句话，「正在听」那一档应当动起来（脉冲帧）
3. 换回「内置画法」立刻恢复；**把插件删掉也应当恢复** —— 这一条是 `art` 域的验收判据

## 两条纪律（与 art 域其它样例一致）

- `theme/ball.json` 里的路径不许绝对、不许 `..`、不许反斜杠，解出来必须落在插件目录内
- `art` 域**不带任何代码、不声明任何能力**；读不到资源一律回退内置画法，不许把球画没

## 相关

- 协议正文与样例集：[luwi-plugins](https://github.com/yuloong07-star/luwi-plugins)
- 同域另一份样例（青瓷 · 5 状态静态图）：
  [samples/art-ball-theme](https://github.com/yuloong07-star/luwi-plugins/tree/main/samples/art-ball-theme)
