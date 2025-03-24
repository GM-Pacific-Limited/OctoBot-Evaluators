# OctoBot-Evaluators Modules

## Evaluators
The `evaluators` module contains all evaluator implementations:
- `abstract_evaluator.py`: Base evaluator class and common functionality
- `TA_evaluator.py`: Technical analysis evaluator
- `scripted_evaluator.py`: Custom script evaluator
- `strategy_evaluator.py`: Strategy implementation
- `social_evaluator.py`: Social media analysis
- `realtime_evaluator.py`: Real-time data analysis
- `evaluator_factory.py`: Factory for creating evaluator instances

## Matrix
The `matrix` module provides the data storage system:
- `matrix.py`: Core matrix implementation
- `matrix_manager.py`: Matrix management utilities
- `matrices.py`: Singleton class managing multiple matrices

## API
The `api` module offers public interfaces for external integration:
- `evaluators.py`: API for managing evaluators
- `initialization.py`: Setup functions
- `matrix.py`: Matrix API
- `inspection.py`: Analysis tools

## Channels
The `channel` module implements the communication system:
- `evaluator_channel.py`: For evaluator signal distribution
- `matrix_channel.py`: For matrix update notifications

## Utilities
The `util` module provides helper functions:
- `evaluation_util.py`: Evaluation-related utilities
