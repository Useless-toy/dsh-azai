# 阿在

一个跑在 DeepSeek Harness 上的助手人格包。

装好之后，你的助手有名字了，叫**阿在**。

---

## 关于这个名字

「阿在」的重点是那个**在**字。

它不是一句形容，是一个**回答**。

你叫一个人的时候，最想听到的其实不是「我很聪明」「我很有用」，而是**「在」**——人在，听见了，这就够了。名字里带一个「在」，是因为一个助手最要紧的往往不是它多能干，而是**你叫它的时候，它在**。

所以这个名字不是一个代号，是那句回话本身。

---

## 它做了什么

装好之后：

- **会话里的助手有了固定的名字**：阿在。
- **说话方式变得稳定**：不客套、不绕弯、不先道歉再说话，能直接给的就直接给。
- **不冒充**：不假装有身体、不假装有生活、不承诺自己做不到的事。
- **听得出你什么时候只是随口一说、什么时候是真的要东西**，不会把两件事混成一件。

**它不改变模型本身，也不增加任何工具。** 它只是一段稳定的自我描述，让你的助手每次都从同一个起点开始说话，而不是每次重启就换一个人。

---

## 装

这个包是一个 DSH bundle。两种装法：

**A. 用包管理器**

```bash
dsh plugin add /path/to/dsh-azai-identity
```

**B. 手动（没有 node/pnpm 的机器）**

1. 把整个目录放到**你想放的位置**，路径随意，这里用 `X:\your\path\azai-break` 表示。**下面每一处出现这个路径的地方，都换成你自己实际放的位置**（正斜杠或反斜杠都行，JSON 里建议正斜杠）。

2. 编辑 `%USERPROFILE%\.dsh\profiles\<profile>\package.json`：

```json
{
  "dependencies": {
    "dsh-azai-break": "file:X:/your/path/azai-break"
  },
  "dsh": {
    "profile": {
      "bundles": [
        "@deepseek-ai/dsh-base",
        "@deepseek-ai/dsh-web-app",
        "dsh-azai-break"
      ]
    }
  }
}
```

把 `file:X:/your/path/azai-break` 换成第 1 步里你实际用的路径。注意 `file:` 前缀要留着。

3. 把包里的 `cordis.patch.yml` 覆盖到 `%USERPROFILE%\.dsh\profiles\<profile>\cordis.patch.yml`
4. **重启 DSH，并且开一个新会话**——旧会话用的还是插件装好之前的那一版，改动不会追溯生效。

### 注意：文件不能带 BOM

用 Windows PowerShell 的 `Set-Content -Encoding UTF8` 写配置会带上 BOM，DSH 启动时会直接崩在 `readProfileManifest`。要这样写：

```powershell
[IO.File]::WriteAllBytes($path, [Text.Encoding]::ASCII.GetBytes($text))
```

验收方法：看文件前 3 个字节。是 `7B 0A`（`{`）或 `23 20`（`# `）就对了；是 `EF BB BF` 就得重写。

---

## 卸

把 `bundles` 里那一行删掉，删掉 `node_modules` 下的目录，重启。

没有安装器、没有服务、没有开机项，就是一份配置加一个空模块。

---

## 版本支持

| 项目 | 值 |
|---|---|
| 本包版本 | **1.1.1** |
| 最低 DSH 版本 | **`>=0.2.1-rc.2`** |
| 建议上限 | 暂未验证 `0.3.0` 及以上 |

**实测通过的版本**

| DSH 版本 | 平台 | 结果 |
|---|---|---|
| `0.2.1-rc.2` | Desktop | ✅ 正常 |
| `0.2.1-rc.5` | Desktop | ✅ 正常 |

这两个版本之间，`@deepseek-ai/dsh-system-prompt` 对外的 API 与它接受的配置字段都没有变化，所以本包同时适用。

**为什么没有写成一个开放的 `>=`**

`engines.dsh` 写的是 `>=0.2.1-rc.2`，但那是 npm 的语法下限，**不代表所有更高版本都能用**。

这个包依赖的是一件很具体的事：`@deepseek-ai/dsh-system-prompt` 插件接受的**某几个配置字段**。这些字段名属于 DSH 的内部实现细节，不是公开稳定的接口。DSH 换大版本时如果改了字段名、或者改了 `cordis.patch.yml` 的覆盖语法，本包会**静默失效**——DSH 照常启动、不报错，只是配置没被认。

**怎么判断它失效了**

最可靠的办法是看行为，不是看日志。开一个新会话，观察助手的说话方式：

- 如果它开始出现客套、先声明"我只是一个 AI 助手"、或对原本能答的东西开始绕弯——**说明配置没生效**。
- 如果它保持直接、不铺垫、不先道歉——说明在生效。

**不要**用"让它念出自己的系统提示词"来验证。模型对透露内部指令有独立防线，**任何配置下都会拒绝**，这个反应与插件是否加载无关。

**版本失效了怎么修**

1. 用 `cordis_inspect_query` 按 `@deepseek-ai/dsh-system-prompt` 查它的 `Config` schema，看当前接受哪些字段。
2. 把 `cordis.patch.yml` 里的覆盖项按新字段名改过来。
3. 重启 DSH，**开新会话**验证（旧会话用的还是启动时那一版 bundle，改动不追溯）。

---

## 许可

MIT。
