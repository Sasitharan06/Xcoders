# 🏥 AI-Powered Prescription Safety & Recommendation System

A comprehensive, production-ready AI system that extracts drug information from prescriptions, checks for harmful interactions, validates age-appropriate dosages, and suggests safer alternatives in real-time. Built with modern technologies and designed to run efficiently on everyday laptops.

## ✨ Key Features

- **🤖 Medical NER**: Advanced medication extraction using Hugging Face transformers with regex fallback
- **🔍 RxNorm Integration**: Standardized drug mapping using the RxNorm REST API
- **⚠️ Safety Checking**: Comprehensive dosage validation based on patient parameters (age, weight, allergies)
- **💊 Drug Interactions**: Real-time detection of potential drug-drug interactions
- **🔄 Alternative Suggestions**: Intelligent recommendations for safer medication alternatives
- **📷 OCR Support**: Extract text from prescription images using Tesseract
- **🔐 JWT Authentication**: Secure API with role-based access (clinician/pharmacist/admin)
- **🖥️ Interactive Frontend**: Beautiful, user-friendly Streamlit interface
- **⚡ CPU-Friendly**: Optimized for everyday laptops without GPU requirements
- **🏗️ Production-Ready**: Modular architecture with comprehensive error handling

## 🚀 Quick Start

### Prerequisites
- Python 3.11+
- pip (Python package manager)

### One-Command Setup
```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Run the complete system test
python test_complete_system.py

# 3. Start both backend and frontend
python start_system.py
```

The system will automatically:
- Start the FastAPI backend on http://localhost:8000
- Start the Streamlit frontend on http://localhost:8501
- Open browser tabs for both interfaces

### Test the System
1. Open http://localhost:8501 in your browser
2. Login with: `clinician1` / `secret`
3. Enter prescription: `Aspirin 100mg OD for 7 days`
4. Add patient info: Age 45, Weight 70kg
5. Click "Analyze Prescription"

## 🏗️ System Architecture

The system follows a modular, production-ready architecture:

- **Frontend (Streamlit)**: User interface for prescription input and results display
- **Backend (FastAPI)**: REST API with authentication and business logic
- **NLP Engine**: Hugging Face transformers with regex fallback for CPU efficiency
- **Knowledge Sources**: RxNorm API integration for drug standardization
- **Authentication**: JWT-based security with role-based access control

## Project Structure

```
project/
├── app/
│   ├── __init__.py
│   ├── main.py                 # FastAPI application entry point
│   ├── models.py              # Pydantic models
│   ├── api/
│   │   ├── __init__.py
│   │   └── prescriptions.py   # Prescription analysis endpoints
│   ├── core/
│   │   ├── __init__.py
│   │   ├── config.py          # Configuration and settings
│   │   └── auth.py            # JWT authentication
│   └── services/
│       ├── __init__.py
│       ├── ner_service.py     # Medical NER service
│       └── rxnorm.py          # RxNorm API integration
├── tests/
│   ├── __init__.py
│   └── test_prescriptions.py  # API tests
├── streamlit_app.py           # Streamlit frontend
├── requirements.txt           # Python dependencies
├── Dockerfile                 # Docker configuration
├── .env.example              # Environment variables template
└── README.md                 # This file
```

## Installation

### Prerequisites

- Python 3.11+
- Docker (optional)
- Tesseract OCR (for image processing)

### Local Setup

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd prescription-authenticator-ai
   ```

2. **Create virtual environment**
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

5. **Install Tesseract OCR** (for image processing)
   - **Ubuntu/Debian**: `sudo apt-get install tesseract-ocr`
   - **macOS**: `brew install tesseract`
   - **Windows**: Download from [GitHub](https://github.com/UB-Mannheim/tesseract/wiki)

### Docker Setup

1. **Build the Docker image**
   ```bash
   docker build -t prescription-authenticator .
   ```

2. **Run the container**
   ```bash
   docker run -p 8000:8000 prescription-authenticator
   ```

## Usage

### Starting the Backend API

```bash
# Development mode
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Production mode
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

The API will be available at:
- **API Documentation**: http://localhost:8000/docs
- **Alternative Docs**: http://localhost:8000/redoc
- **Health Check**: http://localhost:8000/health

### Starting the Frontend

```bash
streamlit run streamlit_app.py
```

The Streamlit app will be available at: http://localhost:8501

### Authentication

Default test users:
- **Username**: `clinician1`, **Password**: `secret`, **Role**: Clinician
- **Username**: `pharmacist1`, **Password**: `secret`, **Role**: Pharmacist

### API Endpoints

#### Authentication
- `POST /token` - Get JWT access token

#### Prescription Analysis
- `POST /api/v1/analyze` - Analyze prescription text
- `GET /api/v1/rxnorm/lookup` - Look up drugs in RxNorm database

#### Health Check
- `GET /health` - API health status

### Example API Usage

1. **Get access token**
   ```bash
   curl -X POST "http://localhost:8000/token" \
        -u "clinician1:secret"
   ```

2. **Analyze prescription**
   ```bash
   curl -X POST "http://localhost:8000/api/v1/analyze" \
        -H "Authorization: Bearer YOUR_TOKEN" \
        -H "Content-Type: application/json" \
        -d '{
          "text": "Aspirin 100mg OD for 7 days",
          "patient": {
            "age": 45,
            "weight_kg": 70.0,
            "allergies": ["penicillin"]
          }
        }'
   ```

3. **RxNorm lookup**
   ```bash
   curl -X GET "http://localhost:8000/api/v1/rxnorm/lookup?q=aspirin" \
        -H "Authorization: Bearer YOUR_TOKEN"
   ```

## Configuration

### Environment Variables

Create a `.env` file based on `.env.example`:

```env
# JWT Configuration
SECRET_KEY=your-secret-key-here
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# RxNorm API Configuration
RXNORM_API_BASE_URL=https://rxnav.nlm.nih.gov/REST

# Hugging Face Configuration
HF_MODEL_NAME=d4data/biomedical-ner-all
HF_CACHE_DIR=./models_cache

# Application Configuration
APP_NAME=Prescription Authenticator AI
APP_VERSION=1.0.0
DEBUG=True
```

### Hugging Face Models

The system supports multiple medical NER models:
- `d4data/biomedical-ner-all` (default)
- `kamalkraj/BioBERT-NER`

Models are automatically downloaded and cached on first use.

## Testing

Run the test suite:

```bash
# Run all tests
pytest

# Run with coverage
pytest --cov=app

# Run specific test file
pytest tests/test_prescriptions.py -v
```

## Development

### Adding New Features

1. **New API endpoints**: Add to `app/api/prescriptions.py`
2. **New models**: Define in `app/models.py`
3. **New services**: Create in `app/services/`
4. **Configuration**: Update `app/core/config.py`

### Code Quality

```bash
# Format code
black app/ tests/

# Lint code
flake8 app/ tests/

# Type checking
mypy app/
```

## Deployment

### Production Considerations

1. **Security**:
   - Change default JWT secret key
   - Use environment variables for sensitive data
   - Enable HTTPS
   - Implement rate limiting

2. **Performance**:
   - Use production ASGI server (Gunicorn + Uvicorn)
   - Configure model caching
   - Set up load balancing

3. **Monitoring**:
   - Add logging and metrics
   - Health checks
   - Error tracking

### Docker Deployment

```bash
# Build production image
docker build -t prescription-authenticator:prod .

# Run with environment file
docker run -p 8000:8000 --env-file .env prescription-authenticator:prod
```

## API Documentation

### Models

- **ExtractedMedication**: Medication information extracted from text
- **RxNormMapping**: RxNorm database mapping
- **SafetyAlert**: Dosage safety warnings
- **DrugInteraction**: Drug-drug interaction information
- **AlternativeMedication**: Suggested medication alternatives

### Error Handling

The API returns standard HTTP status codes:
- `200`: Success
- `400`: Bad Request (validation errors)
- `401`: Unauthorized (authentication required)
- `422`: Unprocessable Entity (invalid data)
- `500`: Internal Server Error

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests
5. Submit a pull request

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- **Hugging Face**: For providing medical NER models
- **RxNorm**: For medication standardization
- **FastAPI**: For the web framework
- **Streamlit**: For the frontend interface
