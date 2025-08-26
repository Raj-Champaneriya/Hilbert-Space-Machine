# Complete Instructions for Coding Agent: Minimalist Hilbert Space Framework

## Core Principles
- **Use existing solutions** rather than building custom implementations
- **Docker Compose orchestration** for local development
- **Minimal viable product** approach with clear separation of concerns
- **CPU-based** implementation (no GPU required initially)

## Project Structure

```
hilbert_framework/
├── docker-compose.yml
├── Dockerfile
├── requirements.txt
├── README.md
├── src/
│   ├── __init__.py
│   ├── data_ingestion.py
│   ├── spectral_transform.py
│   ├── operator_learning.py
│   ├── reasoning_integration.py
│   └── pipeline.py
├── notebooks/
│   └── demo.ipynb
├── tests/
│   ├── __init__.py
│   ├── test_data_ingestion.py
│   ├── test_spectral_transform.py
│   ├── test_operator_learning.py
│   └── test_reasoning.py
├── data/
│   └── samples/
└── config/
    └── config.yaml
```

## Docker Setup

### docker-compose.yml
```yaml
version: '3.8'

services:
  hilbert-framework:
    build: .
    container_name: hilbert_framework
    volumes:
      - .:/app
      - ./data:/app/data
      - ./notebooks:/app/notebooks
    ports:
      - "8888:8888"  # Jupyter
      - "8000:8000"  # Optional API
    environment:
      - PYTHONPATH=/app/src
    command: jupyter lab --ip=0.0.0.0 --port=8888 --no-browser --allow-root --NotebookApp.token=''
    
  redis:
    image: redis:alpine
    container_name: hilbert_redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  redis_data:
```

### Dockerfile
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    gcc \
    g++ \
    && rm -rf /var/lib/apt/lists/*

# Copy requirements first for better caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Set Python path
ENV PYTHONPATH=/app/src

EXPOSE 8888 8000

CMD ["python", "src/pipeline.py"]
```

### requirements.txt
```txt
# Core scientific computing (existing solutions)
numpy>=1.21.0
scipy>=1.7.0
scikit-learn>=1.0.0
pandas>=1.3.0

# Spectral transforms (existing solutions)
pywavelets>=1.1.1
librosa>=0.8.1  # Audio spectral analysis
opencv-python>=4.5.0  # Image processing

# Machine learning (existing solutions)
xgboost>=1.5.0
lightgbm>=3.3.0
catboost>=1.0.0

# Optimization (existing solutions)
cvxpy>=1.2.0  # Convex optimization
optuna>=2.10.0  # Hyperparameter optimization

# Visualization
matplotlib>=3.4.0
seaborn>=0.11.0
plotly>=5.0.0

# Development
jupyter>=1.0.0
jupyterlab>=3.0.0
pytest>=6.0.0
black>=21.0.0
flake8>=3.9.0

# Optional: Advanced ML (existing solutions)
jax[cpu]>=0.3.0  # For differentiable programming
torch>=1.10.0  # PyTorch for neural operators

# Caching and persistence
redis>=4.0.0
joblib>=1.1.0

# Configuration
pyyaml>=6.0
hydra-core>=1.1.0
```

## Implementation Guidelines

### 1. Data Ingestion (Leverage Existing Solutions)

```python
# src/data_ingestion.py
import numpy as np
import pandas as pd
from sklearn.datasets import fetch_openml, make_classification
from sklearn.preprocessing import StandardScaler
import librosa
import cv2

class DataIngestion:
    """Use existing datasets and preprocessing tools"""
    
    def __init__(self):
        self.scaler = StandardScaler()
    
    def load_standard_dataset(self, name: str = 'mnist'):
        """Use scikit-learn's built-in datasets"""
        if name == 'mnist':
            return fetch_openml('mnist_784', version=1, return_X_y=True)
        elif name == 'synthetic':
            return make_classification(n_samples=1000, n_features=20)
    
    def load_audio(self, file_path: str):
        """Use librosa for audio processing"""
        return librosa.load(file_path, sr=22050)
    
    def load_image(self, file_path: str):
        """Use OpenCV for image processing"""
        return cv2.imread(file_path, cv2.IMREAD_GRAYSCALE)
```

### 2. Spectral Transform (Use Existing Libraries)

```python
# src/spectral_transform.py
import pywt
import librosa
from scipy.fft import fft, fftfreq
from sklearn.decomposition import PCA, FastICA

class SpectralTransform:
    """Leverage existing spectral analysis libraries"""
    
    def fourier_transform(self, data: np.ndarray):
        """Use scipy's optimized FFT"""
        return fft(data)
    
    def wavelet_transform(self, data: np.ndarray, wavelet='db4'):
        """Use PyWavelets for wavelet decomposition"""
        return pywt.wavedec(data, wavelet, level=4)
    
    def mel_spectrogram(self, audio: np.ndarray, sr: int = 22050):
        """Use librosa for audio spectral features"""
        return librosa.feature.melspectrogram(y=audio, sr=sr)
    
    def scattering_transform(self, data: np.ndarray):
        """Simplified scattering using existing wavelets"""
        # Use existing wavelet + modulus operations
        coeffs = self.wavelet_transform(np.abs(data))
        return np.concatenate([c.flatten() for c in coeffs])
```

### 3. Operator Learning (Use Existing ML Libraries)

```python
# src/operator_learning.py
from sklearn.kernel_ridge import KernelRidge
from sklearn.gaussian_process import GaussianProcessRegressor
import xgboost as xgb
import optuna

class OperatorLearning:
    """Use existing ML libraries for operator approximation"""
    
    def __init__(self):
        self.models = {
            'kernel_ridge': KernelRidge(),
            'gaussian_process': GaussianProcessRegressor(),
            'xgboost': xgb.XGBRegressor()
        }
    
    def optimize_hyperparameters(self, X, y, model_name='kernel_ridge'):
        """Use Optuna for hyperparameter optimization"""
        def objective(trial):
            if model_name == 'kernel_ridge':
                alpha = trial.suggest_loguniform('alpha', 1e-6, 1e-1)
                gamma = trial.suggest_loguniform('gamma', 1e-6, 1e-1)
                model = KernelRidge(alpha=alpha, gamma=gamma)
            
            # Cross-validation score
            from sklearn.model_selection import cross_val_score
            scores = cross_val_score(model, X, y, cv=3)
            return scores.mean()
        
        study = optuna.create_study(direction='maximize')
        study.optimize(objective, n_trials=50)
        return study.best_params
    
    def estimate_operator(self, X, y, model_name='kernel_ridge'):
        """Use existing ML models for operator learning"""
        model = self.models[model_name]
        return model.fit(X, y)
```

### 4. Docker Compose Commands

```bash
# Start the framework
docker-compose up -d

# Access Jupyter Lab
# Navigate to http://localhost:8888

# Run tests
docker-compose exec hilbert-framework pytest tests/

# Execute pipeline
docker-compose exec hilbert-framework python src/pipeline.py

# Stop services
docker-compose down
```

### 5. Configuration Management

```yaml
# config/config.yaml
data:
  dataset: "mnist"
  batch_size: 32
  validation_split: 0.2

spectral:
  wavelet: "db4"
  levels: 4
  use_scattering: true

operator:
  model: "kernel_ridge"
  optimize_hyperparams: true
  cv_folds: 5

reasoning:
  composition_depth: 2
  modulation_strength: 0.1

logging:
  level: "INFO"
  file: "logs/hilbert_framework.log"
```

### 6. Main Pipeline (Orchestrate Existing Solutions)

```python
# src/pipeline.py
import hydra
from omegaconf import DictConfig
from data_ingestion import DataIngestion
from spectral_transform import SpectralTransform
from operator_learning import OperatorLearning
from reasoning_integration import ReasoningIntegration

@hydra.main(config_path="../config", config_name="config")
def main(cfg: DictConfig):
    """Main pipeline using existing solutions"""
    
    # Initialize components
    ingestion = DataIngestion()
    transform = SpectralTransform()
    learning = OperatorLearning()
    reasoning = ReasoningIntegration()
    
    # Load data using existing datasets
    X, y = ingestion.load_standard_dataset(cfg.data.dataset)
    
    # Transform using existing libraries
    X_transformed = transform.scattering_transform(X)
    
    # Learn operators using existing ML libraries
    operator = learning.estimate_operator(X_transformed, y, cfg.operator.model)
    
    # Apply reasoning
    results = reasoning.compose_and_reason(operator, X_transformed)
    
    return results

if __name__ == "__main__":
    main()
```

### 7. Testing with Existing Frameworks

```python
# tests/test_pipeline.py
import pytest
from src.pipeline import main
from hydra import initialize, compose

def test_end_to_end_pipeline():
    """Test complete pipeline using existing solutions"""
    with initialize(config_path="../config"):
        cfg = compose(config_name="config")
        results = main(cfg)
        assert results is not None
        assert len(results) > 0

def test_with_different_datasets():
    """Test pipeline with different existing datasets"""
    datasets = ['mnist', 'synthetic']
    for dataset in datasets:
        with initialize(config_path="../config"):
            cfg = compose(config_name="config", overrides=[f"data.dataset={dataset}"])
            results = main(cfg)
            assert results is not None
```

## Quick Start Commands

```bash
# Clone and setup
git clone <repository>
cd hilbert_framework

# Start with Docker Compose
docker-compose up -d

# Access Jupyter Lab
open http://localhost:8888

# Run the demo notebook
# Navigate to notebooks/demo.ipynb in Jupyter

# Run tests
docker-compose exec hilbert-framework pytest

# Stop services
docker-compose down
```
