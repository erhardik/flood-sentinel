# AI & Machine Learning

## Machine Learning Framework

This document outlines the AI and machine learning approach for Flood Sentinel's predictive models, including architecture selection, training methodology, and deployment strategy.

## Problem Formulation

### Supervised Learning Approach

**Objective**: Predict water level and flood probability at hyperlocal locations 1-6 hours in advance.

**Inputs (Features)**:
- Historical water level readings (past 24-48 hours)
- Rainfall measurements (current and past 24 hours)
- Soil moisture levels
- Temperature and humidity
- Atmospheric pressure
- Upstream water level data (if available)
- GIS features (elevation, slope, proximity to water body, building type)
- Temporal features (hour of day, day of week, season)

**Outputs (Targets)**:
- **Regression**: Predicted water level (continuous, meters)
- **Classification**: Flood probability (binary: flood/no-flood, or multi-class: low/medium/high/critical risk)

**Time Horizon**: 1-hour, 3-hour, 6-hour ahead predictions (initially focus on 1-3 hours)

### Data Characteristics
- **Frequency**: 5-15 minute intervals from sensors
- **Volume**: ~100-1000 readings per sensor per day
- **Temporal Dependencies**: Strong (time-series nature)
- **Spatial Dependencies**: Moderate (neighboring sensors matter)
- **Class Imbalance**: Floods are rare events (~1-5% of time)
- **Missing Data**: Common (sensor failures, communication loss)

---

## Model Architectures

### Phase 1: Baseline Models

#### 1.1 LSTM (Long Short-Term Memory) Networks
**Why LSTM?**
- Captures long-term dependencies in time-series
- Handles variable-length sequences
- Proven effective for rainfall-runoff prediction
- Interpretable with attention mechanisms

**Architecture**:
```
Input (T, F) → LSTM(64) → LSTM(32) → Dropout(0.2)
→ Dense(16) → Dense(1) → Output
```
- Input shape: (timesteps=24, features=10)
- 2 stacked LSTM layers
- Dropout for regularization
- Single output (water level) or probability

**Hyperparameters**:
- Learning rate: 0.001 (Adam optimizer)
- Batch size: 32-64
- Epochs: 50-100 with early stopping
- Loss function: MSE (regression) or binary crossentropy (classification)

**Expected Performance**:
- RMSE: 5-10 cm for 1-hour prediction
- Accuracy: 70-80% for flood/no-flood classification
- Inference time: <100 ms per location

#### 1.2 Random Forest Baseline
**Why RF?**
- Interpretable feature importance
- Robust to missing data
- Fast inference
- Good baseline for comparison

**Configuration**:
- 100-200 trees
- Max depth: 15-20
- Min samples split: 5
- Feature importance ranking

**Expected Performance**:
- RMSE: 8-15 cm
- Accuracy: 65-75% for classification
- Fast inference (<10 ms)

#### 1.3 Physics-Informed Baseline
**Why PINN?**
- Incorporates domain knowledge
- Better generalization to new regions
- Interpretable predictions
- Provides uncertainty estimates

**Approach**:
- Simple rainfall-runoff model (SCS or antecedent precipitation index)
- Combine with LSTM for refinement
- Constrain outputs to physically plausible ranges

---

### Phase 2: Advanced Models

#### 2.1 CNN-LSTM Hybrid
**Architecture**:
```
Input → Conv1D(32, kernel=3) → MaxPool → 
         Conv1D(64, kernel=3) → MaxPool →
         Flatten → LSTM(64) → Dense(1)
```
- CNN captures spatial patterns in sensor readings
- LSTM captures temporal dependencies
- Useful when incorporating multiple sensor streams

#### 2.2 Transformer Models
**Why Transformers?**
- Parallel processing (faster than LSTM)
- Multi-head attention for interpretability
- Good for capturing long-range dependencies
- State-of-the-art in time-series

**Architecture**:
```
Input → MultiHeadAttention(8 heads) → FFN →
        MultiHeadAttention(8 heads) → FFN →
        Dense(1)
```

#### 2.3 Ensemble Methods
**Approach**:
- Combine LSTM, Random Forest, Physics-based model
- Weighted voting or stacking
- Uncertainty quantification from ensemble disagreement

---

### Phase 3+: Research Models

#### 3.1 Physics-Informed Neural Networks (PINNs)
- Embed hydrological equations in loss function
- Trade-off: data fidelity vs. physics constraints
- Better for data-sparse regions

#### 3.2 Graph Neural Networks (GNNs)
- Model spatial dependencies between sensors as graph
- Each sensor is a node, proximity/watershed determines edges
- Propagate information across network

#### 3.3 Bayesian Deep Learning
- Quantify uncertainty via posterior distributions
- Dropout-based (MC Dropout) or variational approaches
- Important for risk assessment and alert thresholds

---

## Data Preparation

### Data Cleaning
1. **Missing Value Imputation**:
   - Forward-fill for short gaps (<2 hours)
   - Linear interpolation for medium gaps (<6 hours)
   - Discard sequences with >6 hour gaps
   - Separate handling for each sensor type

2. **Outlier Detection**:
   - Statistical (IQR method)
   - ML-based (Isolation Forest)
   - Domain-based (e.g., water level >5m in non-flood zone)
   - Manual review for extreme events

3. **Data Validation**:
   - Check sensor against neighbors (sudden divergence = fault)
   - Rainfall sanity checks (skip erratic tipping bucket clicks)
   - Soil moisture bounds (0-100%)

### Feature Engineering
1. **Temporal Features**:
   - Hour of day, day of week, month (cyclical encoding)
   - Days since last heavy rainfall
   - Antecedent rainfall index (weighted past 7 days)

2. **Derived Features**:
   - Rainfall rate (mm/hr)
   - Water level rate of change (mm/min)
   - Cumulative rainfall (last 1/6/12/24 hours)
   - Soil moisture anomaly (difference from climatology)

3. **Spatial Features** (GIS):
   - DEM elevation, slope, aspect
   - Distance to water body
   - Urban density, impervious surface fraction
   - Drainage area upstream

4. **Lagged Features**:
   - Previous 1, 3, 6, 12, 24 hour values
   - Rolling statistics (mean, std, min, max)

### Train/Validation/Test Split
- **Temporal split** (not random):
  - Train: First 80% of time period
  - Validation: Next 10%
  - Test: Last 10% (unseen future data)
- **Per-location split** (if multiple sensors):
  - Train on some sensors, test on others (transfer learning)

### Class Imbalance Handling
- **Flood class**: Rare (~1-5% of samples)
- **Techniques**:
  - Weighted loss function (higher weight for flood class)
  - Oversampling minority class (SMOTE)
  - Focal loss (emphasizes hard examples)
  - Separate models for each lead time (1h, 3h, 6h)

---

## Model Training & Validation

### Training Pipeline
```
Raw Data → Cleaning → Feature Engineering → Normalization
→ Train/Val/Test Split → Model Training → Validation → Testing
```

### Hyperparameter Tuning
- **Grid Search** (exhaustive): For small search spaces
- **Random Search**: For high-dimensional spaces
- **Bayesian Optimization**: More efficient sampling
- **Cross-Validation**: K-fold (k=5) on training set

### Early Stopping
- Monitor validation loss
- Stop if no improvement for 10 epochs
- Save best model checkpoint

### Regularization Techniques
- Dropout (0.2-0.5)
- L1/L2 regularization
- Batch normalization
- Data augmentation (mixup, noise injection)

### Loss Functions

**For Water Level Prediction**:
```
MSE: mean_squared_error(y_true, y_pred)
MAE: mean_absolute_error(y_true, y_pred)
Custom: Weighted by impact (larger errors penalized more)
```

**For Flood Classification**:
```
Binary Crossentropy: With class weights for imbalance
Focal Loss: Better for class imbalance
f(pt) = -αt(1-pt)^γ log(pt)
where pt is predicted probability, γ=2 (focusing parameter)
```

---

## Model Evaluation

### Regression Metrics
- **RMSE** (Root Mean Squared Error): Penalizes large errors
- **MAE** (Mean Absolute Error): Robust to outliers
- **MAPE** (Mean Absolute Percentage Error): For relative error
- **R²**: Proportion of variance explained

### Classification Metrics
- **Accuracy**: (TP+TN)/(TP+TN+FP+FN)
- **Precision**: TP/(TP+FP) - Of predicted floods, how many real?
- **Recall**: TP/(TP+FN) - Of actual floods, how many detected?
- **F1-Score**: Harmonic mean of precision & recall
- **AUC-ROC**: Area under receiver operating characteristic curve
- **Confusion Matrix**: Visual breakdown of predictions vs. ground truth

### Flood-Specific Metrics
**True Positive Rate (Sensitivity)**: Critical for safety (detect floods)
**False Positive Rate**: Important for operational burden (false alarms)
**Lead Time**: Hours of advance warning before actual flood
**Critical Threshold**: ROC curve to select operating point

### Cross-Region Validation
- Train on Region A, test on Region B (transfer learning)
- Measure performance degradation
- Fine-tune for Region B with limited data

---

## Uncertainty Quantification

### Why Uncertainty Matters
- Predictions without confidence intervals are dangerous
- Operational decisions need risk assessment
- Identify when model is uncertain (system out-of-distribution)

### Methods
1. **Ensemble Uncertainty**: Variance across ensemble members
2. **MC Dropout**: Variational approximation of Bayesian posterior
3. **Conformal Prediction**: Distribution-free prediction intervals
4. **Quantile Regression**: Predict confidence intervals directly

### Output Format
```
Prediction: 1.5 meters (median)
Confidence Interval (95%): [1.2, 1.8] meters
Uncertainty: σ=0.15 meters
Alert Threshold: 2.0 meters
Risk Level: MEDIUM (95% CI below threshold)
```

---

## Model Deployment

### Model Registry
- Version control for all models (MLflow, DVC)
- Track training date, dataset, hyperparameters
- Store model artifacts and ONNX export
- Document performance metrics

### Inference Pipeline
```
Input Data → Validation → Feature Engineering →
Normalization → Model Inference → Post-Processing →
Alert Logic → Output
```

### Edge Deployment
- Convert models to ONNX or TensorFlow Lite
- Deploy on edge devices (Raspberry Pi, Arduino)
- Local inference with minimal network dependency
- Fallback to cloud if edge fails

### Cloud Deployment
- Docker containerization
- FastAPI for REST endpoints
- Horizontal scaling with Kubernetes
- Model versioning and A/B testing

### Inference SLA
- Latency: <5 seconds for alert decision
- Throughput: 100 requests/sec
- Availability: 99.5% uptime

---

## Monitoring & Continuous Improvement

### Model Monitoring
- Track prediction accuracy in production
- Detect data drift (input distribution changes)
- Detect label drift (actual outcomes shift)
- Retraining triggers (accuracy drops >5%)

### Metrics Dashboard
- RMSE by location and lead time
- Flood detection rate, false alarm rate
- Model latency and throughput
- Data freshness and completeness

### Retraining Strategy
- Weekly: Retrain on latest data
- Monthly: Hyperparameter tuning
- Quarterly: Major model updates, new architectures
- As-needed: After major flooding events (capture patterns)

### Active Learning
- Identify high-uncertainty predictions
- Request human review/labeling for uncertain cases
- Retrain with new labels
- Improve model on edge cases

---

## Ethical Considerations

### Fairness
- Evaluate model across different geographic regions
- Monitor for spatial bias (better predictions in wealthy areas?)
- Ensure equal warning lead time for all populations

### Explainability
- Feature importance (SHAP, LIME)
- Attention visualization for LSTM/Transformer
- Counterfactual explanations ("What if rainfall were X?")
- Communicate uncertainty to non-technical audiences

### Accountability
- Document all model decisions
- Provide explainability for failed predictions
- Regular audits by domain experts
- Transparent communication of limitations

### Privacy
- Anonymize location data where possible
- Federated learning to avoid central data repository
- Differential privacy on shared datasets
- Consent framework for using flood impact data

---

## Baselines & Benchmarks

### Simple Baselines
- **Persistence**: Assume water level stays at current value
- **Climatology**: Use historical average for this time of year
- **Linear Regression**: Fit linear model on lagged features

### Physics-Based Baselines
- **SCS Curve Number**: Rainfall-runoff conversion
- **HEC-HMS**: Hydrologic modeling system (complex)
- **Simple Recession**: Water level decays exponentially

### Expected Performance Bar
- LSTM should beat baselines by >20% on RMSE
- Flood detection should achieve >80% recall at <10% false alarm rate

---

## Computational Requirements

### Training
- **GPU**: NVIDIA RTX 3060 or better (recommended)
- **Memory**: 8-16 GB RAM
- **Storage**: 100 GB for datasets
- **Time**: 1-2 hours per model per experiment

### Inference
- **CPU**: Modern multi-core sufficient
- **Memory**: <500 MB for model + data
- **Latency**: <100 ms per prediction
- **Throughput**: 100+ predictions/sec

---

## Code Architecture

### Repository Structure
```
ml/
├── data/
│   ├── preprocessing.py    # Data cleaning, feature engineering
│   ├── loaders.py          # Data loading, batching
│   └── validation.py       # Train/val/test splits
├── models/
│   ├── lstm.py             # LSTM model definition
│   ├── random_forest.py    # RF baseline
│   ├── physics_informed.py # Physics-based models
│   └── ensemble.py         # Ensemble methods
├── training/
│   ├── train.py            # Training loop
│   ├── evaluate.py         # Evaluation metrics
│   └── hyperopt.py         # Hyperparameter tuning
├── inference/
│   ├── predict.py          # Production inference
│   ├── onnx_export.py      # Model conversion
│   └── edge_deploy.py      # Edge device setup
└── tests/
    ├── test_models.py      # Unit tests
    ├── test_data.py        # Data pipeline tests
    └── test_inference.py    # Inference tests
```

---

## References & Further Reading

### Key Papers
- Hochreiter & Schmidhuber (1997): LSTM networks
- Goodfellow et al. (2016): Deep Learning textbook
- Raissi et al. (2019): Physics-informed neural networks
- LeCun et al. (2015): Deep learning review

### Datasets for Benchmarking
- [NOAA National Water Center](https://water.noaa.gov/)
- [UCI Machine Learning Repository](https://archive.ics.uci.edu/)
- [Zenodo Research Data](https://zenodo.org/)
- [Google Dataset Search](https://datasetsearch.research.google.com/)

### Tools & Frameworks
- **TensorFlow/Keras**: High-level deep learning
- **PyTorch**: Research-friendly, dynamic graphs
- **XGBoost/LightGBM**: Gradient boosting
- **Scikit-learn**: Classical ML, preprocessing
- **MLflow**: Model registry and tracking
- **Ray/Optuna**: Hyperparameter optimization

---

**Status**: Phase 0 - Architecture and methodology definition (implementation begins Phase 1)  
**Last Updated**: July 2026

Have ideas for ML improvements? Open an issue with your proposal!
