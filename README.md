# 🌾 Sugarcane Pest Detection System

A full-stack AI-powered application for detecting sugarcane pests using computer vision and machine learning. The system combines YOLOv8 image detection with TabNet questionnaire analysis to provide accurate pest predictions for **Dead Heart** and **Tiller** pests.

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Python](https://img.shields.io/badge/python-3.9+-blue.svg)
![FastAPI](https://img.shields.io/badge/FastAPI-0.104+-green.svg)
![React](https://img.shields.io/badge/React-18+-blue.svg)

## 📜 Project Description

Sugarcane Pest Detection is an end-to-end, production-ready system that helps farmers and agronomists identify two critical sugarcane pests—Dead Heart and Tiller—early and accurately. The platform combines:
- Image intelligence with YOLOv8 (segmentation for Dead Heart, detection for Tiller) to localize symptoms on plant photos and render visual overlays.
- Structured plant assessment via a 15-question symptom questionnaire processed by a TabNet model for each pest.
- Weighted fusion (default 0.6 image, 0.4 questionnaire) to compute a final pest score and label, configurable through environment variables.
- Weather-based risk assessment that uses your location (GPS/manual state/city) to estimate current pest risk levels and provide preventive recommendations.

Why it matters
- Early detection and timely advice reduce crop loss and improve yield/ROI for farmers.
- Visual explanations (masks/boxes) make results trustworthy and actionable.
- Offline-friendly design choices (simple JSON questionnaire, small APIs) support constrained connectivity environments.

Who it’s for
- Farmers, agri-extension workers, co-ops, and research teams who want a simple and explainable field tool.
- Developers and students seeking a reference-quality, beginner-friendly full-stack AI project.

How it works (high level)
1) Upload a clear plant photo and answer a short pest-specific questionnaire.
2) The FastAPI backend:
   - Runs YOLOv8 on the image and extracts masks/boxes and confidence scores.
   - Loads the pest-specific TabNet model (via joblib) and predicts probability from features derived from answers.
   - Fuses both signals into a final score and label; returns the overlay image (base64), structured detections, and all scores.
3) The React frontend:
   - Renders the overlay, scores, detections table, and localized UI (English/Hindi/Tamil/Telugu).
   - Provides a Weather & Pest Risk section with location-based guidance.
4) Results are displayed with clear visuals and recommendations.

Built by Team Deepcrop during the Agrithon conducted at VIT, the solution emphasizes:
- Beginner-friendly, well-commented code.
- Configurability (model paths, weights, thresholds).
- Robustness (fallback logic, clear error messages, health checks).
- Deployability (Docker, docker-compose).

## 🚀 Features

- **Dual Pest Detection**: Supports both Dead Heart (segmentation) and Tiller (detection) pests
- **Multi-Modal AI**: Combines image analysis (YOLOv8) with questionnaire data (TabNet)
- **Weather-Based Risk Assessment**: Real-time pest risk analysis based on weather conditions
- **Real-time Predictions**: Fast API responses with confidence scores
- **Interactive Web Interface**: User-friendly React frontend with multi-language support
- **Comprehensive Questionnaires**: 15 specific questions for each pest type
- **Visual Results**: Overlay images showing detected areas with confidence scores
- **Fusion Scoring**: Weighted combination of image and questionnaire predictions
- **Location-Based Services**: GPS and manual location selection for Indian regions
- **Docker Support**: Easy deployment with Docker containers

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   React Frontend│    │  FastAPI Backend│    │   AI Models     │
│                 │    │                 │    │                 │
│ • Image Upload  │◄──►│ • REST API      │◄──►│ • YOLOv8 (Image)│
│ • Questionnaire │    │ • Model Loading │    │ • TabNet (Data) │
│ • Results View  │    │ • Fusion Logic  │    │ • Confidence    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

## 🛠️ Technology Stack

### Backend
- **FastAPI**: Modern Python web framework
- **YOLOv8**: State-of-the-art object detection/segmentation
- **TabNet**: Deep learning for tabular data
- **OpenCV**: Image processing
- **PyTorch**: Deep learning framework
- **Uvicorn**: ASGI server

### Frontend
- **React 18**: Modern UI library
- **Vite**: Fast build tool
- **CSS3**: Responsive styling
- **Fetch API**: HTTP client

### DevOps
- **Docker**: Containerization
- **Docker Compose**: Multi-container orchestration
- **Nginx**: Production web server

## 📋 Prerequisites

- **Python 3.9+**
- **Node.js 16+**
- **Git**
- **Docker** (optional, for containerized deployment)

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/sugarcane-disease-detection.git
cd sugarcane-disease-detection
```

### 2. Set Up Environment Variables

```bash
cp .env.example .env
# Edit .env with your model paths and configuration
```

### 3. Backend Setup

```bash
cd backend

# Create virtual environment
python -m venv venv

# Activate virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 4. Add Your Models

Place your trained models in the `backend/models/` directory:
- `yolov_deadheart.pt` - YOLOv8 segmentation model for dead heart
- `yolov_tiller.pt` - YOLOv8 detection model for tiller
- `tabnet_deadheart.joblib` - TabNet model for dead heart questionnaire
- `tabnet_tiller.joblib` - TabNet model for tiller questionnaire

### 5. Start Backend Server

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

### 6. Frontend Setup

Open a new terminal:

```bash
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev
```

### 7. Access the Application

- **Frontend**: http://localhost:5173
- **Backend API**: http://localhost:8000
- **API Documentation**: http://localhost:8000/docs

## 🐳 Docker Deployment

### Quick Start with Docker Compose

```bash
# Build and start all services
docker-compose up --build

# Run in background
docker-compose up -d
```

### Individual Container Builds

```bash
# Backend
cd backend
docker build -t sugarcane-backend .

# Frontend
cd frontend
docker build -t sugarcane-frontend .
```

## 📖 API Documentation

### Endpoints

#### Health Check
```http
GET /health
```

#### Dead Heart Prediction
```http
POST /predict/deadheart
Content-Type: multipart/form-data

Parameters:
- image: Image file (JPEG/PNG)
- questions: JSON string with questionnaire answers
```

#### Tiller Prediction
```http
POST /predict/tiller
Content-Type: multipart/form-data

Parameters:
- image: Image file (JPEG/PNG)
- questions: JSON string with questionnaire answers
```

### Response Format

```json
{
  "image_confidence": 0.87,
  "tabnet_prob": 0.73,
  "final_score": 0.80,
  "final_label": "deadheart",
  "detections": [
    {
      "box": [100, 100, 300, 300],
      "score": 0.87,
      "class": "deadheart",
      "type": "segmentation"
    }
  ],
  "overlay_image_base64": "data:image/png;base64,..."
}
```

## 🧪 Testing

### Backend Tests

```bash
cd backend
python -m pytest tests/ -v
```

### Frontend Tests

```bash
cd frontend
npm test
```

### Example API Call

```bash
curl -X POST "http://localhost:8000/predict/deadheart" \
  -H "Content-Type: multipart/form-data" \
  -F "image=@path/to/image.jpg" \
  -F "questions={\"boreholes_plugged_excreta\":\"yes\",\"central_whorl_dry_withered\":\"no\"}"
```

## 📊 Model Information

### Dead Heart Pest (Segmentation)
- **Questions**: 15 specific yes/no questions about symptoms
- **Detection**: YOLOv8 segmentation with polygon masks
- **Visualization**: Green overlay on affected areas

### Tiller Pest (Detection)
- **Questions**: 15 specific yes/no questions about symptoms
- **Detection**: YOLOv8 object detection with bounding boxes
- **Visualization**: Red bounding boxes around affected areas

### Fusion Scoring
- **Image Weight**: 0.6 (configurable via `IMAGE_WEIGHT` env var)
- **TabNet Weight**: 0.4 (configurable via `TABNET_WEIGHT` env var)
- **Threshold**: 0.5 (configurable via `PREDICTION_THRESHOLD` env var)

## 🔧 Configuration

### Environment Variables

```bash
# Model paths
DEADHEART_YOLO_PATH=models/yolov_deadheart.pt
DEADHEART_TABNET_PATH=models/tabnet_deadheart.joblib
TILLER_YOLO_PATH=models/yolov_tiller.pt
TILLER_TABNET_PATH=models/tabnet_tiller.joblib

# Fusion weights
IMAGE_WEIGHT=0.6
TABNET_WEIGHT=0.4
PREDICTION_THRESHOLD=0.5
```

## 📁 Project Structure

```
sugarcane-disease-detection/
├── README.md                 # Project documentation
├── LICENSE                   # MIT license
├── .gitignore               # Git ignore rules
├── .env.example             # Environment variables template
├── docker-compose.yml       # Docker orchestration
├── backend/                 # FastAPI backend
│   ├── app/
│   │   ├── main.py         # FastAPI application
│   │   ├── models.py       # AI model integration
│   │   └── utils.py        # Utility functions
│   ├── models/             # AI model files (not in git)
│   ├── requirements.txt    # Python dependencies
│   └── Dockerfile         # Backend container
├── frontend/               # React frontend
│   ├── src/
│   │   ├── components/     # React components
│   │   ├── App.jsx        # Main application
│   │   └── main.jsx       # Entry point
│   ├── package.json       # Node.js dependencies
│   └── Dockerfile         # Frontend container
├── examples/               # Example data and requests
└── docs/                  # Additional documentation
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Team

- Team Name: Deepcrop
- Team Members: Hariharan S, Naresh R, Arfath, Yusuf
- Built by Team Deepcrop during the Agrithon conducted at VIT.

## 🙏 Acknowledgments

- **YOLOv8**: Ultralytics for the amazing object detection framework
- **TabNet**: Google Research for the tabular deep learning model
- **FastAPI**: For the modern Python web framework
- **React**: For the powerful frontend library

## 📞 Support

If you encounter any issues or have questions:

1. Check the [Issues](https://github.com/yourusername/sugarcane-disease-detection/issues) page
2. Create a new issue with detailed information
3. Refer to the documentation in the `docs/` folder

## 🔮 Future Enhancements

- [ ] Support for additional sugarcane pests
- [ ] Mobile app development
- [ ] Real-time video analysis
- [ ] Advanced analytics dashboard
- [ ] Multi-language support
- [ ] Cloud deployment guides
- [ ] Model performance monitoring

---

**Made with ❤️ for sustainable agriculture**
