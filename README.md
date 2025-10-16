# AIDTIS - AI-Driven Intrusion Detection & Threat Intelligence System

A comprehensive machine learning-based intrusion detection system that supports multiple datasets (NSL-KDD, CICIDS2017/2018, UNSW-NB15, Bot-IoT) and provides real-time threat detection, visualization, and automated response capabilities. The system includes supervised and unsupervised ML models, a REST API, interactive dashboard, and containerized deployment options.

## Quick Start (3 Steps)

1. **Setup Environment**: `make setup` (creates conda environment with GPU support)
2. **Download & Process Data**: `make extract` (downloads datasets and creates unified flow format)
3. **Train & Serve**: `make train && make serve` (trains models and starts API + dashboard)

**📋 For complete step-by-step instructions, see [QUICKSTART.md](QUICKSTART.md)**

## ⚠️ Safety & Legal Disclaimer

**IMPORTANT**: This system is designed for educational and research purposes only. All traffic generation, attack simulation, and automated response features must ONLY be used within isolated Docker networks or virtual machines. Never run active traffic generation or automated blocking on production networks or host systems. Users are responsible for compliance with local laws and network policies.

## Features

- **Multi-Dataset Support**: NSL-KDD, CICIDS2017/2018, UNSW-NB15, Bot-IoT
- **Unified Flow Processing**: PCAP to flow extraction with standardized schema
- **ML Models**: RandomForest, XGBoost, Autoencoder, IsolationForest, LSTM
- **Real-time API**: FastAPI service with batch and single prediction endpoints
- **Interactive Dashboard**: Streamlit app with live monitoring and visualization
- **Automated Response**: Demo scripts for isolated network blocking
- **Containerized Deployment**: Docker and Kubernetes ready
- **GPU Acceleration**: CUDA support for RTX 4060 and similar GPUs

## System Requirements

- **Hardware**: NVIDIA RTX 4060 (or compatible CUDA GPU)
- **OS**: Ubuntu 20.04+ (Windows 10/11 with WSL2 recommended)
- **Memory**: 16GB RAM minimum, 32GB recommended
- **Storage**: 50GB free space for datasets and models
- **Software**: Docker, Python 3.9+, CUDA 11.8+

## Repository Structure

```
AIDTIS/
├── src/                          # Source code
│   ├── api/                      # FastAPI service
│   ├── models/                   # ML model training
│   ├── utils/                    # Utilities and feature engineering
│   └── data/                     # Data processing and extraction
├── notebooks/                    # Jupyter notebooks and tutorials
├── tests/                        # Unit tests
├── config/                       # Configuration files
├── scripts/                      # Utility scripts
├── docker/                       # Docker configurations
├── k8s/                          # Kubernetes manifests
├── .github/workflows/            # CI/CD pipelines
├── requirements.txt              # Python dependencies
├── environment.yml               # Conda environment
├── Dockerfile                    # Docker image definition
├── docker-compose.yml            # Multi-service deployment
├── Makefile                      # Build and deployment commands
├── README.md                     # This file
├── QUICKSTART.md                 # Step-by-step guide
└── LICENSE                       # MIT License
```

## Key Components

### 1. Data Processing (`src/data/`)
- **Flow Extractor**: PCAP to flow conversion using Scapy/dpkt
- **Dataset Parser**: Unified parsing for multiple intrusion detection datasets
- **Data Balancer**: Train/validation/test splitting and balancing

### 2. Feature Engineering (`src/utils/`)
- **Feature Engineer**: Advanced feature extraction and preprocessing
- **Time Features**: Hour, day-of-week, business hours detection
- **Network Features**: IP entropy, port analysis, protocol statistics
- **Statistical Features**: Rolling statistics, polynomial features

### 3. Machine Learning (`src/models/`)
- **Supervised Models**: RandomForest, XGBoost, LightGBM
- **Unsupervised Models**: IsolationForest, Autoencoder, LOF, OCSVM
- **Model Training**: Hyperparameter tuning with Optuna
- **Evaluation**: Cross-dataset performance analysis

### 4. API Service (`src/api/`)
- **FastAPI**: RESTful API with automatic documentation
- **Endpoints**: Single/batch prediction, CSV upload, PCAP ingestion
- **Authentication**: Configurable security and rate limiting
- **Monitoring**: Health checks and performance metrics

### 5. Dashboard (`src/streamlit_app.py`)
- **Real-time Monitoring**: Live threat detection visualization
- **Interactive Analysis**: Single flow and batch prediction interface
- **Model Comparison**: Performance metrics and confusion matrices
- **Alert Management**: Threat timeline and attacker analysis

### 6. Response Automation (`src/utils/response_automation.py`)
- **Demo Scripts**: Isolated network blocking (Docker only)
- **Cloud Integration**: Mock AWS WAF, Cloudflare, Azure WAF
- **Safety Checks**: Multiple validation layers for production safety

## Usage Examples

### API Usage

```bash
# Health check
curl http://localhost:8000/health

# Single prediction
curl -X POST "http://localhost:8000/predict" \
  -H "Content-Type: application/json" \
  -d '{"flow": {...}, "model_name": "random_forest"}'

# Batch prediction
curl -X POST "http://localhost:8000/predict/batch" \
  -H "Content-Type: application/json" \
  -d '{"flows": [...], "model_name": "random_forest"}'
```

### Python Integration

```python
import requests

# Predict single flow
response = requests.post("http://localhost:8000/predict", json={
    "flow": {
        "src_ip": "192.168.1.100",
        "dst_ip": "10.0.0.1",
        "src_port": 12345,
        "dst_port": 80,
        "protocol": 6,
        # ... other flow features
    },
    "model_name": "random_forest"
})

prediction = response.json()
print(f"Prediction: {prediction['prediction']}")
print(f"Confidence: {prediction['confidence']}")
```

## Deployment Options

### 1. Docker Compose (Recommended for Development)

```bash
# Start all services
make docker-up

# Access services
# API: http://localhost:8000
# Dashboard: http://localhost:8501
# Jupyter: http://localhost:8888
```

### 2. Kubernetes (Production)

```bash
# Deploy to Kubernetes
kubectl apply -f k8s/deployment.yaml

# Access via ingress
# API: https://api.aidtis.example.com
# Dashboard: https://dashboard.aidtis.example.com
```

### 3. Manual Deployment

```bash
# Start API
python -m uvicorn src.api.api:app --host 0.0.0.0 --port 8000

# Start Dashboard
streamlit run src/streamlit_app.py --server.port 8501
```

## Development

### Running Tests

```bash
# Unit tests
python -m pytest tests/ -v

# With coverage
python -m pytest tests/ -v --cov=src --cov-report=html

# Linting
flake8 src/ tests/
black src/ tests/
mypy src/
```

### Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Run the test suite
6. Submit a pull request

## Performance

- **Single Prediction**: < 100ms
- **Batch Processing**: 1000+ flows/second
- **Memory Usage**: 2-4GB for full pipeline
- **GPU Acceleration**: 3-5x speedup with CUDA

## Security Considerations

- **Input Validation**: All API inputs are validated
- **Rate Limiting**: Configurable request limits
- **Authentication**: Ready for OAuth/JWT integration
- **Network Isolation**: Docker network isolation
- **Audit Logging**: Comprehensive logging and monitoring

## Troubleshooting

### Common Issues

1. **CUDA not found**: Install CUDA 11.8 and compatible drivers
2. **Out of memory**: Reduce batch sizes or use smaller datasets
3. **Port conflicts**: Change ports in configuration files
4. **Model loading errors**: Check model files exist in models/ directory

### Getting Help

- Check logs: `make docker-logs`
- Run tests: `python -m pytest tests/ -v`
- Check API health: `curl http://localhost:8000/health`
- Review documentation: `QUICKSTART.md`

## License

MIT License - See [LICENSE](LICENSE) file for details.

## Citation

If you use AIDTIS in your research, please cite:

```bibtex
@software{aidtis2024,
  title={AIDTIS: AI-Driven Intrusion Detection & Threat Intelligence System},
  author={AIDTIS Contributors},
  year={2024},
  url={https://github.com/your-org/aidtis},
  license={MIT}
}
```

## Acknowledgments

- NSL-KDD, CICIDS2017/2018, UNSW-NB15, Bot-IoT dataset creators
- Scapy, FastAPI, Streamlit, and other open-source libraries
- NVIDIA for CUDA support and GPU acceleration

---

**🛡️ Protecting Networks with AI-Powered Intrusion Detection**
