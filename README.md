# Chart Pattern Prediction Model

This repository contains a machine learning model for predicting chart patterns in cryptocurrency price data. The model uses a hybrid CNN-LSTM architecture to analyze both technical indicators and candlestick images.

## Recent Improvements

The codebase has been modified to separate the data preparation and model training steps, which provides several benefits:

1. **Faster Iterations**: No need to re-fetch and re-process 8 years of data every time you want to train the model
2. **Consistent Training**: Using the same prepared data ensures consistent model training results
3. **Reduced API Usage**: Fewer calls to exchange APIs, reducing the risk of rate limiting
4. **Incremental Updates**: Ability to update the model with new data without retraining from scratch

### New Features

1. **Automatic Daily Training**: The model can now be scheduled to automatically train daily with new data
2. **Multiple Cryptocurrency Pairs**: Support for preparing data for multiple trading pairs at once
3. **Enhanced Scheduling**: Configurable training time and immediate or scheduled training options

## Files Overview

- `chart_pattern_prediction_model.py`: The core model implementation
- `train_8years.py`: Script to prepare and save 8 years of historical data
- `predict_realtime.py`: Script to train the model and make real-time predictions
- `run_chart_pattern_model.py`: New script that combines all steps in a single command

## How to Use

### Option 1: Using the All-in-One Script

The simplest way to use this model is with the new `run_chart_pattern_model.py` script:

```bash
# Basic usage (uses default settings: BTCUSDT, 1d timeframe, 8 years of data)
python run_chart_pattern_model.py

# Specify a different trading pair
python run_chart_pattern_model.py --symbol ETHUSDT

# Specify a different timeframe
python run_chart_pattern_model.py --timeframe 4h

# Use fewer years of data
python run_chart_pattern_model.py --years 4

# Force data preparation even if data exists
python run_chart_pattern_model.py --force-prepare

# Force model retraining even if model exists
python run_chart_pattern_model.py --force-retrain

# Skip training and only run predictions
python run_chart_pattern_model.py --predict-only

# Prepare data for multiple cryptocurrency pairs
python run_chart_pattern_model.py --symbols BTCUSDT,ETHUSDT,BNBUSDT,ADAUSDT,SOLUSDT

# Schedule automatic daily training
python run_chart_pattern_model.py --schedule-daily

# Schedule automatic daily training at a specific time (24-hour format)
python run_chart_pattern_model.py --schedule-daily --training-time 04:30
```

### Option 2: Step-by-Step Approach

If you prefer to run each step separately:

1. **Prepare Data**:
   ```bash
   # Prepare data for a single trading pair
   python train_8years.py --symbol BTCUSDT --timeframe 1d --years 8
   
   # Prepare data for multiple trading pairs
   python train_8years.py --symbols BTCUSDT,ETHUSDT,BNBUSDT --timeframe 1d --years 8
   ```

2. **Train Model**:
   ```bash
   python predict_realtime.py
   ```

3. **Make Predictions**:
   ```bash
   python predict_realtime.py --predict-only
   ```

4. **Schedule Daily Training**:
   ```bash
   # In Python code:
   from chart_pattern_prediction_model import ChartPatternPredictor
   
   # Initialize the predictor
   predictor = ChartPatternPredictor(symbol="BTC/USDT", timeframe="1d")
   
   # Load an existing model
   predictor.load_model()
   
   # Schedule daily training at midnight
   predictor.schedule_daily_training(training_time="00:00")
   
   # Keep the script running
   import time
   while True:
       time.sleep(60)
   ```

## Data and Model Storage

- Prepared data is stored in directories named `chart_pattern_data_{SYMBOL}_{YEARS}years`
- Trained models are stored in directories named `chart_pattern_model_{SYMBOL}_{YEARS}years`
- Prediction results and visualizations are stored in the `prediction_results` directory
- Training logs are stored in the `training_logs` directory within each model directory

## Automatic Daily Training

The automatic daily training feature allows the model to stay up-to-date with the latest market data. When enabled:

1. The model checks for new data at the scheduled time each day
2. If sufficient new data is available, it incrementally trains the model
3. Training logs are saved to track the model's performance over time

For production use, consider setting up a system service or cron job to ensure the scheduler runs continuously.

## Requirements

- Python 3.7+
- TensorFlow 2.x
- pandas
- numpy
- matplotlib
- mplfinance
- scikit-learn
- Binance API client
- schedule (for automatic daily training)

## Troubleshooting

- If you encounter errors related to missing data, run with the `--force-prepare` flag
- If you encounter errors related to model loading, run with the `--force-retrain` flag
- For API rate limit issues, reduce the frequency of predictions or use a different API key
- If automatic daily training is not working, check that the script is running continuously and that the `schedule` package is installed

## License

This project is licensed under the MIT License - see the LICENSE file for details. 