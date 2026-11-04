# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：手順2はCodespacesで実施済み。第1回の素のLLMの回答を比較対象として確認できていないため、具体的な回答の違いは未確認。
- `--trace` で見えたツール呼び出し（ツール名と引数）：本人がCodespacesで「LoRAとQLoRAの違いは？」を実行し、`search_glossary({'query': 'LoRA'})` と `search_glossary({'query': 'QLoRA'})` を確認した。`tool_calls=2`。
- 1つの質問での LLM 呼び出し回数（`requests`）：上記の実測で `requests=2`。
- 追加したツールと、docstring に書いた説明：`make_quiz(lecture: int) -> str` を追加し、`agent.tool_plain(tools.make_quiz)` で登録した。指定回の用語を1つランダムに選び、「○○の意味は？」という問題と定義を返す。用語がない回は、見つからない旨と別の回の指定を促すメッセージを返す。docstring には「指定した回の用語集から確認問題を1問作り、問題と答えを返す」という目的と、「第2回の確認問題を出して」のように復習や確認問題を求められたときに使うこと、`lecture` は1始まりの整数で指定することを記載した。
- API不要の検証（Codexのローカル環境、Python 3.12.16）：`uv run python -m app.agent --list-tools` で `make_quiz` を含む3ツールとdocstring、整数型の `lecture` 引数を確認した。`uv run pytest -q` は `11 passed in 6.90s`。最初のpytest実行はサンドボックス内のasyncio内部ソケット待ちで中断し、サンドボックス外で再実行して成功した。追加の直接確認では、第1・2・3・5回の問題と答えが用語集と一致し、第0・-1・99回は案内文を返すことを確認した。
- 手順5のGemini実測：Codespacesの `lec03` で `ec6a54f` を取得後、本人が `uv run python -m app.agent --trace "第2回の内容から確認問題を1問出して"` を実行した。`make_quiz({'lecture': 2})` が呼ばれ、「教師ありファインチューニング（SFT）」の意味を問う問題と定義が返った。エージェントはその問題を出題し、「第2回」と表示して利用者の回答を待った。実測は `tool_calls=1`、`requests=2`。

## 宿題

- **取り組んだレベル：** 基礎

- **やったこと：** `app/agent.py` の `INSTRUCTIONS` から「用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。」という指示の1行だけを削除した。その他の指示・ツール登録・docstring・モデル設定は変更せず、Codespacesで変更前後に同じ `uv run python -m app.agent --trace "LoRAとQLoRAの違いは？"` を実行し、ツールの呼び出しと回答内容を比較した。モデルは `gemini-3.5-flash-lite`。

- **結果：** 変更前は `make_quiz` 追加後の基準コミット `ec6a54f`、変更後は指示の1行を削除したコミット `a3e77cd` で比較した。本人が確認した実測結果は次のとおり。

  | 項目 | 変更前 | 変更後 |
  |---|---|---|
  | ツール名と引数 | `search_glossary({'query': 'LoRA'})` | `search_glossary({'query': 'LoRA'})`、`search_glossary({'query': 'QLoRA'})` |
  | ツール呼び出し回数（`tool_calls`） | 1 | 2 |
  | LLM呼び出し回数（`requests`） | 2 | 2 |
  | 回答内容 | LoRAの低ランク行列による追加学習と、QLoRAのベースモデル量子化による省メモリ化を概ね適切に説明した | LoRAとQLoRAの違いを概ね適切に説明した |
  | 関係する回の表示 | 第2回 | 第2回 |

- **気づき・疑問：** 検索を必須とする指示を削除しても、検索ツールは使用された。ツールのdocstringにも検索を促す説明が残っているため、エージェントの動作は全体の指示だけでなく、ツールの説明文にも影響される可能性があると考えた。ただし、今回の比較だけでdocstringの影響を個別に確認できたわけではない。また、変更前の別の実行でも `tool_calls=2`、`requests=2` だった。これは指示変更前からの実行間変動として扱い、今回の1回ずつの比較だけでは、検索回数の違いが指示の変更によるものとは断定しない。
