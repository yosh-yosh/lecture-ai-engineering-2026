
# 第6回 解答

## 演習

- STEP 1 のツールの名前：
  - agent.py：search_materials
  - mcp_agent.py：glossary_search_glossary

- STEP 2 の [tool call]：

  ```text
  [tool call]   glossary_search_glossary({'query': '3レベル'})
  ```

- STEP 3 の [tool call]：

  ```text
  [tool call]   fetch_fetch({'url': 'https://modelcontextprotocol.io/'})
  ```

- STEP 4 の [tool call]：

  ```text
  [tool call]   aws_aws___search_documentation({'topics': ['general'], 'search_phrase': 'Amazon S3 バージョニング 概要'})
  [tool call]   aws_aws___search_documentation({'topics': ['general'], 'search_phrase': 'Using versioning S3 developer guide'})
  [tool call]   glossary_search_glossary({'query': 'S3'})
  ```

- 気づき・疑問：

  MCPを利用することで、エージェント本体のコードを変更せず、設定ファイルへの追記によって外部ツールを追加できることが確認できた。

  また、AWSのドキュメント検索では、日本語の検索語で検索した後、英語の検索語に変更して再検索する動作が確認できた。

  入力トークン数はSTEP 2で715、STEP 3で2,230、STEP 4で16,153であった。質問内容や検索結果、モデルの応答回数も異なるため単純比較はできないが、ツールの追加や検索結果の取得によるコンテキストの増加にも注意が必要だと感じた。

## 宿題

- 取り組んだレベル：実装

- やったこと：

  第3回の宿題で作成したmake_quiz関数を再利用し、app/tools.pyに追加した。指定された講義回の用語集からランダムに1問を選択し、問題と答えを返す関数である。

  範囲外の講義回が指定された場合には、出題対象外であることに加え、実際に出題可能な講義回を用語集から取得して返すように変更した。

  app/mcp_server.pyに以下のコードを追加し、MCPツールとして公開した。

  ```python
  server.tool()(tools.make_quiz)
  ```

- 結果：

  ツール一覧にglossary_make_quizが追加され、利用可能なツール数が7個から8個になった。

  正常入力として「第2回のクイズを作って」を実行した。

  ```text
  [tool call]   glossary_make_quiz({'lecture': 2})
  ```

  「Hugging Face Hub」の意味を問う確認問題と、用語集に基づく回答が生成された。

  次に、範囲外の入力として「第99回のクイズを作って」を実行した。

  ```text
  [tool call]   glossary_make_quiz({'lecture': 99})
  ```

  ツールから以下の内容が返された。

  ```text
  第99回は出題対象外です。出題可能な講義は第1回、第2回、第3回、第4回、第6回です。有効な講義回を指定してください。
  ```

  モデルは存在しない講義回の確認問題を生成せず、出題可能な講義回を提示した。

  また、pytestによる既存テストも21件すべて合格した。

- 気づき・疑問：

  ツールのdocstringや型ヒントが、モデルによるツールの選択や引数の指定に利用されることを確認できた。

  特に、範囲外の入力に対して単にエラーを返すのではなく、利用可能な選択肢を具体的に返すことで、モデルが次の行動をユーザーに案内できる点が興味深かった。

  医療分野でAIを利用する場合にも、すべての判断をLLMに任せるのではなく、プログラム側で入力の妥当性を確認し、その結果をLLMに伝える仕組みが、安全性を考えるうえで重要になると考えた。
