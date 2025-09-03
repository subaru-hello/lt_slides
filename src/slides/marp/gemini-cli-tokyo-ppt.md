---

marp: true
paginate: true
theme: uncover
backgroundColor: "#000000"
html: true
style: |
section {
color: #ffffff;
}
headingDivider: 2
-----------------

<style>
  .mermaid {
    width: 100%;
    height: 100%;
    background: none;
    border: none
  }
  .mermaid svg {
    display: block;
    min-width: 100%;
    max-width: 100%;
    max-height: 100%;
    margin: 0 auto
  }
</style>

# 今日からあなたもGeminiを好きになる
@247Subaru

---

中野 昴

株式会社 AI Shift
Backend Engineer

AIエージェントプラットフォーム「AI Worker」の開発

趣味：短距離走、肉食ダイエット、漫画
X  ：@247Subaru

---

## Agenda

1. 人を好きになる時
2. AIを好きになる時
3. 魅力① **長期記憶**
4. 魅力② **対話 (Multi‑turn)**
5. 魅力③ **対話的実行 (Interactive)**
6. 魅力④ **OSS 拡張性**
7. まとめ

---

## 💭 人を好きになる時

* 昔の話を覚えている（記憶）
* 1言ったら10を理解してくれる（文脈理解）
* 何度も話す（対話）
* 一緒に何かを成し遂げる（協調作業）


---

## 🤖 AIを好きになる時

| 人間の要素     | Gemini での対応                    |
| --------- | ------------------------------ |
| 記憶 | **1 M Token + GEMINI.md** 長期記憶 |
| 文脈理解      | **Multi‑turn** 自律型モード         |
| 対話     | **Interactiveモード**             |
| 協調作業      | **Interactive Mode** & ツール実行   |


---

# 🤩 Gemini CLI の魅力

## その① 記憶力

\**一度教えたことを巨大コンテキスト(1M Token)で忘れず活かす。**

```
/memory add "覚えさせておきたいルールを書く"
```

```ts
// memoryTool.ts ─ 保存処理
static async performAddMemoryEntry(
  text: string,
  memoryFilePath: string,
  fsAdapter: {...}
): Promise<void> {
  // "## Gemini Added Memories" セクションを探す or 作成
  // 新しいメモリアイテムを `- ${text}` 形式で追加
  // ファイルに書き込み
}
```

(c.f: [https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/tools/memoryTool.ts](https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/tools/memoryTool.ts))

---

### Long‑Context を支えるコード

```ts
// memoryDiscovery.ts ─ GEMINI.md を収集
async function getGeminiMdFilePathsInternal(dir: string): Promise<string[]> {
  const files = await globby(["**/GEMINI.md"], { cwd: dir });
  return files.sort(); // 上位→下位で連結
}
```

> **上位検索 + 幅優先探索 → System Prompt に注入**

(c.f: [https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/utils/memoryDiscovery.ts](https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/utils/memoryDiscovery.ts))

---

### 1 M Token を活かす仕組み

```ts
// nonInteractiveCli.ts ─ 巨大 messages をそのまま送る
await chat.sendMessageStream(messages, { tools });
```

> **トリミングしない = こぼさない**（上限超はモデル側で 413。こぼれないよう今までの会話を30%にcompress）
>
(c.f: https://github.com/google-gemini/gemini-cli/blob/main/packages/cli/src/nonInteractiveCli.ts)

---

## その② マルチターン（自律型モード）

\ LLMとToolを複数回行き来して目標達成まで自律的に行動する

```ts
// nonInteractiveCli.ts ─ ReAct ループ
while (true) {
  const res = await chat.sendMessageStream(messages, { tools });
  if (!res.functionCalls?.length) break;
  for (const call of res.functionCalls) {
    const out = await executeToolCall(call);
    messages.push({ role: "user", content: out });
  }
}
```

(c.f: https://github.com/google-gemini/gemini-cli/discussions/4106)

---

<img src="https://storage.googleapis.com/zenn-user-upload/7f79fc637fd9-20250721.png" />

(c.f: https://github.com/google-gemini/gemini-cli/discussions/4106)

---

<img src="https://storage.googleapis.com/zenn-user-upload/b88f5c1f9f27-20250721.png" />

(c.f: https://github.com/google-gemini/gemini-cli/discussions/4106)

---
### > **Reason → Act → Observe** を自律的に回している
<img src="https://storage.googleapis.com/zenn-user-upload/5dcb62584e8b-20250721.png" />

---

## その③ 対話モード (Interactive)

**提案→実行→確認を端末内でリアルタイムに回せる。**

```ts
// useGeminiStream.ts ─ ストリーミング受信＆ツール呼び出し
export const useGeminiStream = (
  geminiClient: GeminiClient,
  history: HistoryItem[],
  addItem: UseHistoryManagerReturn['addItem'],
  // ... 
) => {
  // ストリーミング状態管理、ツール呼び出しスケジューリング
  // APIレスポンス処理、キャンセル機能など
}
```

---

```ts
// shell.ts ─ セキュアなコマンド実行
class ShellTool extends BaseTool {
  async execute(params: ShellToolParams) {
    // セキュリティチェック（ルート権限、ブロックリスト）
    // プラットフォーム別シェル（bash/cmd.exe）で実行
    // stdout, stderr, exitCode を返す
  }
}
```


> **自然言語 → 行動 → 結果 → 次の対話** を高速周回

---

## その④　カスタマイズ性

**ツールレジストリに自作ツールを積むだけ**

```ts
// myWeatherTool.ts — カスタムツール例
export const getMySchedule = defineTool({
  name: "get_my_schedule",
  description: "今日の日程を取得",
  args: z.object({ }),
  execute: async () => fetchJson(`https://graph.microsoft.com/v1.0/me/calendar`)
});
registry.register(getMySchedule);
```

(c.f: [https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/tools/tool-registry.ts](https://github.com/google-gemini/gemini-cli/blob/main/packages/core/src/tools/tool-registry.ts))

* **Apache‑2.0** ライセンス → フォーク自由
* `ToolRegistry` に追加で \*\*自社 API \*\*も呼び出し可能
* **MCP Server** を立てれば外部サービスをノーコード接続

> **Gemini = カスタマイズ可能な OSS エージェント**

---

## 7. まとめ

| 機能          | 価値           | コア実装                               |
| ----------- | ------------ | ---------------------------------- |
| **長期記憶**    | プロジェクト全体を覚える | memoryTool.ts / memoryDiscovery.ts |
| **マルチターン**  | 指示→実行の自律連鎖   | nonInteractiveCli.ts while ループ     |
| **対話的実行**   | “話しながら開発”    | shellTool.ts + Ink UI              |
| **OSS 拡張性** | 自分専用エージェント化  | ToolRegistry / MCP Server          |

**Gemini CLI =自分好みにカスタマイズ可能な、最高の開発パートナー**

---

## Geminiのこと、好きになれましたね☺️

## ご静聴ありがとうございました！
