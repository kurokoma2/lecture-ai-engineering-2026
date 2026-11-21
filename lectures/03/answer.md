# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
第1回の回答は、一般論としての回答にすぎなかったのに対し、第3回の回答は、各段階の定義について用語集どおりであり、「どれか1つに取り組めばその回の宿題は完了」「原則として採点は行われません」という点も同様だった。
- `--trace` で見えたツール呼び出し（ツール名と引数）：
ツール名：'query'、引数：'LoRA'、ツール名：'query'、引数：'QLoRA'
- 1つの質問での LLM 呼び出し回数（`requests`）：
2回
- 追加したツールと、docstring に書いた説明：
make_quiz()を追加した。docstringに書いた説明は以下の通り。
    """指定した回の用語から確認問題を1問作り、問題と答えを返す。

    「第2回の確認問題を出して」のように、復習用の問題を求められたときに使う。
    """


## 宿題

- 取り組んだレベル：基礎
- やったこと：
(1) app/agent.py の INSTRUCTIONSから「必ず search_glossary で用語集を確認する」の行を削除する
(2) app/tools.py の docstringの「用語を1つだけ入れる」を「まとめて入れる」に変える

- 結果：
(1) ツールの呼ばれ方・回数・引数は、変更前と変わらなかった。回答は、変更前と意味的には同じだが、glossary通りではなく、LoRAについての回答は要約された形になった。
(2) ツールの呼ばれ方：引数が 'LoRA, QLoRA'とまとめられ、ツールの呼ばれる回数（tool_calls）も1回になった。回答は、変更前と意味的には同じだが、glossary通りではなく、LoRAについての回答は要約された形になった。


(0) 変更前の回答とtrace

[model] gemini-3.5-flash-lite
**LoRA**と**QLoRA**の違いは、ベースモデルの重みを量子化するかどうか、およびそれに伴うメモリ効率にあります。

* **LoRA（Low-Rank Adaptation）**
  事前学習済みの重みを固定し、重みの更新分を低ランク行列で近似してその部分だけを学習するPEFT（Parameter-Efficient Fine-Tuning）手法です。学習パラメータや保存ファイルを大幅に削減できます。

* **QLoRA（Quantized LoRA）**
  LoRAの仕組みをベースにしつつ、**ベースモデルの重みを4ビットなどの低ビットに量子化**してメモリ消費をさらに劇的に削減したうえで学習を行う手法です。これにより、より大きなモデルをより小さなGPUメモリで扱えるようになります。

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0009319'), details={'text_prompt_tokens': 1298}, input_text_tokens=1298, input_tokens=1298, output_tokens=217, requests=2, tool_calls=2)


(1) の変更後の回答とtrace

[model] gemini-3.5-flash-lite
LoRAとQLoRAの違いは、**ベースモデルの重みをそのまま使うか、量子化してメモリを削減するかが主な違い**です。

* **LoRA**: 事前学習済みの重みは固定したまま、重みの更新分を低ランク行列で表し、その小さな行列だけを学習するPEFT手法です。学習パラメータを大きく削減できます。
* **QLoRA**: ベースモデルの重みを4ビットなどに量子化してメモリを節約した状態で、LoRAによる学習を行う手法です。これにより、より大きなモデルを小さなGPUメモリでも扱えるようになります。

第2回（第2回）

--- trace ---
[tool call]   search_glossary({'query': 'LoRA'})
[tool call]   search_glossary({'query': 'QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
[usage] RunUsage(cost=Decimal('0.0008269'), details={'text_prompt_tokens': 1298}, input_text_tokens=1298, input_tokens=1298, output_tokens=175, requests=2, tool_calls=2)


(2)の変更後の回答とtrace

[model] gemini-3.5-flash-lite
LoRAとQLoRAの主な違いは、**ベースモデルの重みをそのまま使うか、量子化して圧縮するか**という点にあります。

* **LoRA（Low-Rank Adaptation）**
  事前学習済みの重みを固定したまま、低ランクの小さな行列（更新分）だけを学習することで、効率的にモデルをファインチューニングする手法です。

* **QLoRA（Quantized LoRA）**
  LoRAの仕組みに加え、ベースモデルの重みを**4ビットなどに量子化（圧縮）**してメモリ消費を大幅に抑えた上で学習する手法です。これにより、より大きなモデルを限られたGPUメモリ環境でも学習できるようになります。

（関連する回：第2回）

--- trace ---
[tool call]   search_glossary({'query': 'LoRA, QLoRA'})
[tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
[usage] RunUsage(cost=Decimal('0.0007632'), details={'text_prompt_tokens': 1044}, input_text_tokens=1044, input_tokens=1044, output_tokens=180, requests=2, tool_calls=1)


- 気づき・疑問：
(1)ではapp/agent.pyからglossaryを参照するという行を消したので、回答がglossaryから離れるのはやむを得ないと思われるが、(2)ではそれを元に戻したにもかかわらず、glossaryとは異なる（誤ってはいないが）要約された回答になった。