# OctoBot-Evaluators Documentation

## Overview
OctoBot-Evaluators is a component of the OctoBot trading bot ecosystem that provides market data analysis capabilities. It enables algorithmic traders to analyze market conditions and generate trading signals based on various evaluation methods.

The module allows users to:
- Analyze price data using technical indicators
- Execute custom evaluation strategies via scripted evaluators
- Process real-time market data
- Combine multiple evaluation signals into cohesive trading strategies
- Store and retrieve evaluation results through a structured matrix system

## Core Architecture
OctoBot-Evaluators is built around several key abstractions:

### Evaluator Framework
- `AbstractEvaluator`: Base class for all evaluators, defining the evaluation lifecycle
- Specialized evaluator types:
  - `TAEvaluator`: Technical analysis evaluator for price data
  - `ScriptedEvaluator`: User-defined evaluation scripts
  - `StrategyEvaluator`: High-level decision making based on other evaluators
  - `SocialEvaluator`: Evaluator analyzing social signals
  - `RealTimeEvaluator`: Evaluator for real-time data

### Matrix System
- `Matrix`: Core data structure for storing evaluation results
- `MatrixManager`: Utilities for interacting with the matrix
- Hierarchical path-based data organization by exchange, cryptocurrency, symbol, etc.

### Channel System
- `EvaluatorChannel`: For distributing evaluator signals
- `MatrixChannel`: For matrix updates and notifications
- Publisher/subscriber model for asynchronous communication

## Directory Structure
```
octobot_evaluators/
├── api/                  # Public API functions
│   ├── evaluators.py     # API for managing evaluators
│   ├── initialization.py # Setup functions
│   ├── matrix.py         # Matrix API
│   └── inspection.py     # Analysis tools
├── evaluators/           # Core evaluator implementations
│   ├── abstract_evaluator.py     # Base evaluator class
│   ├── TA_evaluator.py           # Technical analysis
│   ├── scripted_evaluator.py     # Custom script evaluator
│   ├── strategy_evaluator.py     # Strategy implementation
│   ├── social_evaluator.py       # Social media analysis
│   ├── realtime_evaluator.py     # Real-time data analysis
│   └── evaluator_factory.py      # Factory for creating evaluators
├── matrix/               # Matrix system components
│   ├── matrix.py         # Core matrix implementation
│   └── matrix_manager.py # Matrix management utilities
└── util/                 # Utility functions
```

## Evaluation Process

1. **Initialization**:
   - Evaluators are created and configured based on user settings
   - Each evaluator registers itself with the matrix
   - Time frames and symbols are established

2. **Data Reception**:
   - Market data is received through channels
   - Data is distributed to the appropriate evaluators

3. **Evaluation**:
   - Evaluators process the data according to their specific algorithms
   - Results are stored in the matrix at the appropriate path

4. **Strategy Application**:
   - Strategy evaluators combine signals from other evaluators
   - Final trading decisions are derived from the combined evaluations

5. **Signal Distribution**:
   - Trading signals are distributed to the trading components

## Evaluator Types

### Technical Analysis Evaluators
These evaluators process OHLCV (Open-High-Low-Close-Volume) price data using technical indicators such as:
- Moving averages
- RSI, MACD, Bollinger Bands
- Custom technical indicators

### Social Evaluators
These analyze data from social sources like:
- Twitter/X sentiment
- Reddit discussions
- News feeds
- Forum activity

### Real-Time Evaluators
These process immediate market data such as:
- Order book changes
- Trade ticks
- Price movements

### Scripted Evaluators
These allow users to define custom logic in Python scripts that:
- Can combine multiple data sources
- Implement proprietary algorithms
- Execute complex decision trees

### Strategy Evaluators
These combine signals from other evaluators to produce final trading decisions:
- Weight different evaluator signals
- Apply risk management rules
- Generate final trading signals

## Matrix System
The matrix is a hierarchical data structure that organizes evaluation results by:
- Exchange
- Cryptocurrency
- Symbol (trading pair)
- Time frame
- Evaluator type and name

This organization allows for efficient retrieval and aggregation of evaluation results.

## Usage Examples

### Creating a Simple Technical Analysis Evaluator
```python
class MyRSIEvaluator(TAEvaluator):
    def __init__(self, tentacles_setup_config):
        super().__init__(tentacles_setup_config)
        self.period = 14
        
    async def eval_impl(self):
        # Get the candles data
        candles = await self.get_exchange_symbol_data(self.exchange_name, 
                                                     self.symbol, 
                                                     self.time_frame)
        
        # Calculate RSI
        close_prices = candles[common_constants.CANDLE_CLOSE]
        rsi_values = tulipy.rsi(close_prices, self.period)
        
        # Determine evaluation based on RSI
        current_rsi = rsi_values[-1]
        if current_rsi < 30:
            self.eval_note = 1  # Strong buy signal
        elif current_rsi > 70:
            self.eval_note = -1 # Strong sell signal
        else:
            self.eval_note = 0  # Neutral
            
        # Publish evaluation
        await self.evaluation_completed(self.cryptocurrency, 
                                       self.symbol, 
                                       self.time_frame)
```

### Creating a Strategy Evaluator
```python
class MySimpleStrategy(StrategyEvaluator):
    def __init__(self, tentacles_setup_config):
        super().__init__(tentacles_setup_config)
        
    async def eval_impl(self):
        # Get evaluations from technical indicators
        rsi_eval = await self.get_evaluator_eval(self.cryptocurrency,
                                                self.symbol,
                                                self.time_frame,
                                                "MyRSIEvaluator")
        
        macd_eval = await self.get_evaluator_eval(self.cryptocurrency,
                                                 self.symbol,
                                                 self.time_frame,
                                                 "MyMACDEvaluator")
        
        # Simple strategy: average the technical signals
        if rsi_eval is not None and macd_eval is not None:
            self.eval_note = (rsi_eval + macd_eval) / 2
        
        # Publish evaluation
        await self.evaluation_completed(self.cryptocurrency, 
                                       self.symbol, 
                                       self.time_frame)
```
