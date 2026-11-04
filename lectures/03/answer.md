# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：手順2はCodespacesで実施済み。具体的な回答の比較は本人による追記待ち。
- `--trace` で見えたツール呼び出し（ツール名と引数）：本人がCodespacesで「LoRAとQLoRAの違いは？」を実行し、`search_glossary({'query': 'LoRA'})` と `search_glossary({'query': 'QLoRA'})` を確認した。`tool_calls=2`。
- 1つの質問での LLM 呼び出し回数（`requests`）：上記の実測で `requests=2`。
- 追加したツールと、docstring に書いた説明：`make_quiz(lecture: int) -> str` を追加し、`agent.tool_plain(tools.make_quiz)` で登録した。指定回の用語を1つランダムに選び、「○○の意味は？」という問題と定義を返す。用語がない回は、見つからない旨と別の回の指定を促すメッセージを返す。docstring には「指定した回の用語集から確認問題を1問作り、問題と答えを返す」という目的と、「第2回の確認問題を出して」のように復習や確認問題を求められたときに使うこと、`lecture` は1始まりの整数で指定することを記載した。
- API不要の検証（Codexのローカル環境、Python 3.12.16）：`uv run python -m app.agent --list-tools` で `make_quiz` を含む3ツールとdocstring、整数型の `lecture` 引数を確認した。`uv run pytest -q` は `11 passed in 6.90s`。最初のpytest実行はサンドボックス内のasyncio内部ソケット待ちで中断し、サンドボックス外で再実行して成功した。追加の直接確認では、第1・2・3・5回の問題と答えが用語集と一致し、第0・-1・99回は案内文を返すことを確認した。
- 手順5のGemini実測：未実施。Codespacesで `uv run python -m app.agent --trace "第2回の内容から確認問題を1問出して"` を実行し、`make_quiz` の呼び出しと引数を本人が確認する。

## 宿題

- 取り組んだレベル：基礎 / 実装 / 発展
- やったこと：
- 結果：
- 気づき・疑問：
