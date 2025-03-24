# OctoBot-Evaluators モジュール

## 評価器
`evaluators`モジュールはすべての評価器実装を含みます：
- `abstract_evaluator.py`：基本評価器クラスと共通機能
- `TA_evaluator.py`：テクニカル分析評価器
- `scripted_evaluator.py`：カスタムスクリプト評価器
- `strategy_evaluator.py`：戦略実装
- `social_evaluator.py`：ソーシャルメディア分析
- `realtime_evaluator.py`：リアルタイムデータ分析
- `evaluator_factory.py`：評価器インスタンス作成のファクトリー

## マトリックス
`matrix`モジュールはデータストレージシステムを提供します：
- `matrix.py`：コアマトリックス実装
- `matrix_manager.py`：マトリックス管理ユーティリティ
- `matrices.py`：複数のマトリックスを管理するシングルトンクラス

## API
`api`モジュールは外部統合のための公開インターフェースを提供します：
- `evaluators.py`：評価器管理用API
- `initialization.py`：セットアップ関数
- `matrix.py`：マトリックスAPI
- `inspection.py`：分析ツール

## チャネル
`channel`モジュールは通信システムを実装します：
- `evaluator_channel.py`：評価器シグナル配信用
- `matrix_channel.py`：マトリックス更新通知用

## ユーティリティ
`util`モジュールはヘルパー関数を提供します：
- `evaluation_util.py`：評価関連ユーティリティ
