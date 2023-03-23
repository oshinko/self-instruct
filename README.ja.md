# Self-Instruct: 自己生成命令とLMの整列

このリポジトリには、[Self-Instruct 論文](https://arxiv.org/abs/2212.10560)のためのコードとデータが含まれており、事前学習済み言語モデルと命令の整列方法です。

## 導入

Self-Instruct は、言語モデルが自然言語の命令に従う能力を向上させるためのフレームワークです。これは、モデル自体が生成した命令データを大量に作成することによって実現されます。Self-Instruct を使えば、手動でのアノテーションに頼ることなく、言語モデルの命令に従う能力を向上させることが可能です。

### 背景

近年、多様なタスクを実行するための自然言語命令に従うモデルを構築することに対する関心が高まっています。これらのモデルは「命令調整型」言語モデルとして知られており、新しいタスクに一般化する能力を示しています。ただし、そのパフォーマンスは、訓練に使用される人間が書いた命令データの質と量に大きく依存しており、多様性と創造性に限りがあります。この制限を克服するためには、命令調整型モデルの監視方法や命令に従う能力を向上させるための代替手段を開発することが重要です。

### Self-Instruct はどのように動作するか？

Self-Instruct のプロセスは、手書きの命令の種セットから始め、それを使って言語モデルに新しい命令と対応する入力-出力インスタンスを生成させる反復ブートストラッピングアルゴリズムです。これらの生成物は、低品質なものや類似したものを除去するためにフィルタリングされ、結果として得られたデータがタスクプールに戻されます。このプロセスは複数回繰り返すことができ、効果的に命令に従う言語モデルを微調整するために使用できる大量の命令データが得られます。

Self-Instruct の概要:

![言語モデル自体から命令データを生成するためのパイプライン。](docs/pipeline.JPG)

## 使用法

\* **この作業はまだ進行中です。進展に応じてコードとデータを更新するかもしれません。バージョン管理に注意してください。**

### Self-Instruct データを使用した命令調整

私たちは、52kの命令と82Kのインスタンス入力と出力がペアになったデータセットをリリースします。この命令データは、言語モデルの命令調整を行い、言語モデルが命令により良く従うようにするために使用できます。モデル生成データ全体は、`data/gpt3-generations/batch_221203/all_instances_82K.jsonl` にアクセスできます。このデータ（+175の種タスク）は、`data/finetuning/self_instruct_221203` に整形されたクリーンなGPT3ファインチューニング形式（プロンプト+完成）で入力されます。このデータをGPT3にファインチューニングするには、[`./scripts/finetune_gpt3.sh`](./scripts/finetune_gpt3.sh) のスクリプトを使用できます。

**Note**: このデータは言語モデル（GPT-3）によって生成されており、必然的にいくつかのエラーやバイアスが含まれています。私たちの論文では、200のランダムな命令についてデータ品質を分析し、データポイントの46％に問題があるかもしれないことが分かりました。ユーザーには、このデータを注意深く使用し、不完全さをフィルタリングまたは改善する新しい方法を提案することを勧めます。

### 命令に従う能力の評価

また、ユーザー志向のアプリケーション（従来のNLPタスクではなく）に基づいた252の専門家が書いたタスクとその命令の新しいセットをリリースします。このデータは、[the self-instruct 論文](https://arxiv.org/abs/2212.10560) の人間評価セクションで使用されます。詳細については、[人間評価の README](human_eval/README.md) を参照してください。

### 最初から Self-Instruct データを生成する

独自の種タスクや他のモデルを使用して Self-Instruct データを生成するために、ここでパイプライン全体のスクリプトをオープンソース化します。現在のコードは、[OpenAI API](https://beta.openai.com/docs/models/gpt-3) 経由でアクセス可能なGPT3モデルでのみテストされています。

データを生成するためのスクリプトは以下の通り:

```bash
# 1. Generate instructions from the seed tasks
./scripts/generate_instructions.sh

# 2. Identify whether the instruction represents a classification task or not
./scripts/is_clf_or_not.sh

# 3. Generate instances for each instruction
./scripts/generate_instances.sh

# 4. Filtering, processing, and reformatting
./scripts/prepare_for_finetuning.sh
```

## 引用

もし、Self-Instruct フレームワークやデータを使用する場合は、遠慮なく引用してください。

```bibtex
@misc{selfinstruct,
  title={Self-Instruct: Aligning Language Model with Self Generated Instructions},
  author={Wang, Yizhong and Kordi, Yeganeh and Mishra, Swaroop and Liu, Alisa and Smith, Noah A. and Khashabi, Daniel and Hajishirzi, Hannaneh},
  journal={arXiv preprint arXiv:2212.10560},
  year={2022}
}
```
