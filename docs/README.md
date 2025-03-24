# OctoBot-Evaluators ドキュメント

## 概要
OctoBot-Evaluatorsは、OctoBot取引ボットエコシステムのコンポーネントで、市場データ分析機能を提供します。アルゴリズム取引者が市場状況を分析し、様々な評価方法に基づいて取引シグナルを生成することを可能にします。

このモジュールは、ユーザーに以下の機能を提供します：
- テクニカル指標を使用した価格データの分析
- スクリプト化された評価器を通じたカスタム評価戦略の実行
- リアルタイム市場データの処理
- 複数の評価シグナルを一貫した取引戦略に統合
- 構造化されたマトリックスシステムを通じた評価結果の保存と取得

## コアアーキテクチャ
OctoBot-Evaluatorsはいくつかの重要な抽象化を中心に構築されています：

### 評価器フレームワーク
- `AbstractEvaluator`：すべての評価器の基本クラスで、評価ライフサイクルを定義
- 専門化された評価器タイプ：
  - `TAEvaluator`：価格データのテクニカル分析評価器
  - `ScriptedEvaluator`：ユーザー定義の評価スクリプト
  - `StrategyEvaluator`：他の評価器に基づく高レベルの意思決定
  - `SocialEvaluator`：ソーシャルシグナルを分析する評価器
  - `RealTimeEvaluator`：リアルタイムデータの評価器

### マトリックスシステム
- `Matrix`：評価結果を保存するためのコアデータ構造
- `MatrixManager`：マトリックスと対話するためのユーティリティ
- 取引所、暗号通貨、シンボルなどによる階層的なパスベースのデータ編成

### チャネルシステム
- `EvaluatorChannel`：評価器シグナルの配信用
- `MatrixChannel`：マトリックス更新と通知用
- 非同期通信のためのパブリッシャー/サブスクライバーモデル

## ディレクトリ構造
```
octobot_evaluators/
├── api/                  # 公開API関数
│   ├── evaluators.py     # 評価器管理用API
│   ├── initialization.py # セットアップ関数
│   ├── matrix.py         # マトリックスAPI
│   └── inspection.py     # 分析ツール
├── evaluators/           # コア評価器実装
│   ├── abstract_evaluator.py     # 基本評価器クラス
│   ├── TA_evaluator.py           # テクニカル分析
│   ├── scripted_evaluator.py     # カスタムスクリプト評価器
│   ├── strategy_evaluator.py     # 戦略実装
│   ├── social_evaluator.py       # ソーシャルメディア分析
│   ├── realtime_evaluator.py     # リアルタイムデータ分析
│   └── evaluator_factory.py      # 評価器作成ファクトリー
├── matrix/               # マトリックスシステムコンポーネント
│   ├── matrix.py         # コアマトリックス実装
│   └── matrix_manager.py # マトリックス管理ユーティリティ
└── util/                 # ユーティリティ関数
```

## 評価プロセス

1. **初期化**：
   - 評価器はユーザー設定に基づいて作成・設定される
   - 各評価器はマトリックスに自身を登録する
   - 時間枠とシンボルが確立される

2. **データ受信**：
   - 市場データはチャネルを通じて受信される
   - データは適切な評価器に配信される

3. **評価**：
   - 評価器は特定のアルゴリズムに従ってデータを処理する
   - 結果は適切なパスのマトリックスに保存される

4. **戦略適用**：
   - 戦略評価器は他の評価器からのシグナルを組み合わせる
   - 最終的な取引決定は組み合わされた評価から導き出される

5. **シグナル配信**：
   - 取引シグナルは取引コンポーネントに配信される

## 評価器タイプ

### テクニカル分析評価器
これらの評価器は、OHLCV（始値-高値-安値-終値-出来高）価格データを以下のようなテクニカル指標を使用して処理します：
- 移動平均
- RSI、MACD、ボリンジャーバンド
- カスタムテクニカル指標

### ソーシャル評価器
これらは以下のようなソーシャルソースからのデータを分析します：
- Twitter/Xのセンチメント
- Redditのディスカッション
- ニュースフィード
- フォーラム活動

### リアルタイム評価器
これらは以下のような即時市場データを処理します：
- オーダーブックの変更
- 取引ティック
- 価格変動

### スクリプト評価器
これらはユーザーがPythonスクリプトでカスタムロジックを定義することを可能にします：
- 複数のデータソースを組み合わせることができる
- 独自のアルゴリズムを実装する
- 複雑な決定木を実行する

### 戦略評価器
これらは他の評価器からのシグナルを組み合わせて最終的な取引決定を生成します：
- 異なる評価器シグナルに重みを付ける
- リスク管理ルールを適用する
- 最終的な取引シグナルを生成する

## マトリックスシステム
マトリックスは、評価結果を以下によって整理する階層的なデータ構造です：
- 取引所
- 暗号通貨
- シンボル（取引ペア）
- 時間枠
- 評価器タイプと名前

この編成により、評価結果の効率的な取得と集約が可能になります。

## 使用例

### シンプルなテクニカル分析評価器の作成
```python
class MyRSIEvaluator(TAEvaluator):
    def __init__(self, tentacles_setup_config):
        super().__init__(tentacles_setup_config)
        self.period = 14
        
    async def eval_impl(self):
        # キャンドルデータの取得
        candles = await self.get_exchange_symbol_data(self.exchange_name, 
                                                     self.symbol, 
                                                     self.time_frame)
        
        # RSIの計算
        close_prices = candles[common_constants.CANDLE_CLOSE]
        rsi_values = tulipy.rsi(close_prices, self.period)
        
        # RSIに基づく評価の決定
        current_rsi = rsi_values[-1]
        if current_rsi < 30:
            self.eval_note = 1  # 強い買いシグナル
        elif current_rsi > 70:
            self.eval_note = -1 # 強い売りシグナル
        else:
            self.eval_note = 0  # ニュートラル
            
        # 評価の公開
        await self.evaluation_completed(self.cryptocurrency, 
                                       self.symbol, 
                                       self.time_frame)
```

### 戦略評価器の作成
```python
class MySimpleStrategy(StrategyEvaluator):
    def __init__(self, tentacles_setup_config):
        super().__init__(tentacles_setup_config)
        
    async def eval_impl(self):
        # テクニカル指標からの評価を取得
        rsi_eval = await self.get_evaluator_eval(self.cryptocurrency,
                                                self.symbol,
                                                self.time_frame,
                                                "MyRSIEvaluator")
        
        macd_eval = await self.get_evaluator_eval(self.cryptocurrency,
                                                 self.symbol,
                                                 self.time_frame,
                                                 "MyMACDEvaluator")
        
        # シンプルな戦略：テクニカルシグナルの平均
        if rsi_eval is not None and macd_eval is not None:
            self.eval_note = (rsi_eval + macd_eval) / 2
        
        # 評価の公開
        await self.evaluation_completed(self.cryptocurrency, 
                                       self.symbol, 
                                       self.time_frame)
```
