## モデルの訓練

### 前提条件

- PreparedData がすでに用意できていること
- `brew install libomp` などで LightGBM が内部的に必要とする libomp.dylib (OpenMP ライブラリ) がインストールされていること

### モデルの生成

以下のように引数に PreparedData のパスを与えてスクリプトを実行する

```bash
$ python scripts/training.py data/prepared/20240622_084705/prepared_data.csv
[?] Select a strategy for training: 
 > high_payout
   placement

[?] Select a library for training: 
 > xgboost
   lightgbm

Selected strategy: high_payout
Selected library: xgboost
2024-06-22 12:47:44,915 - metaboatrace.ml.config.logging - INFO - Training set distribution: {0: 1715, 1: 1715, 2: 1715}
2024-06-22 12:47:44,915 - metaboatrace.ml.config.logging - INFO - Testing set distribution: {0: 7584, 1: 1091, 2: 416}
2024-06-22 12:47:44,916 - metaboatrace.ml.config.logging - INFO - Race ID overlap check: False
Fitting 5 folds for each of 96 candidates, totalling 480 fits
2024-06-22 13:23:33,621 - metaboatrace.ml.config.logging - INFO - Accuracy on Test set: 0.4685
2024-06-22 13:23:33,621 - metaboatrace.ml.config.logging - INFO - Confusion Matrix:
2024-06-22 13:23:33,621 - metaboatrace.ml.config.logging - INFO - [[3643 1957 1984]
 [ 317  450  324]
 [ 149  101  166]]
2024-06-22 13:23:33,621 - metaboatrace.ml.config.logging - INFO - Classification Report:
2024-06-22 13:23:33,621 - metaboatrace.ml.config.logging - INFO - {
    "0": {
        "precision": 0.8865904112922852,
        "recall": 0.4803533755274262,
        "f1-score": 0.6231078422988112,
        "support": 7584.0
    },
    "1": {
        "precision": 0.17942583732057416,
        "recall": 0.41246562786434465,
        "f1-score": 0.2500694637399278,
        "support": 1091.0
    },
    "2": {
        "precision": 0.06709781729991916,
        "recall": 0.39903846153846156,
        "f1-score": 0.11487889273356401,
        "support": 416.0
    },
    "accuracy": 0.46848531514684855,
    "macro avg": {
        "precision": 0.37770468863759293,
        "recall": 0.43061915497674413,
        "f1-score": 0.32935206625743435,
        "support": 9091.0
    },
    "weighted avg": {
        "precision": 0.7642248333246292,
        "recall": 0.46848531514684855,
        "f1-score": 0.555083629997977,
        "support": 9091.0
    }
}
Model and logs saved in metaboatrace/ml/models/high_payout/prod/20240622_132333
```
