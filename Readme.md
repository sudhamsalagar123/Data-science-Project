# House Price Prediction Project

## Project Structure

```
housing-price-prediction/
├── data/
│   ├── housing_data.csv              # Original dataset
│   └── exploratory_analysis.png      # Data visualization
├── models/
│   ├── scaler.pkl                    # Saved feature scaler
│   └── house_price_model.pkl         # Trained model
├── src/
│   ├── data_creation.py              # Script to create sample data
│   ├── data_preprocessing.py         # Data cleaning and prep script
│   ├── model_training.py             # Model building script
│   └── feature_importance.png        # Feature importance visualization
├── api/
│   ├── app.py                        # FastAPI application
│   └── requirements.txt              # API dependencies
├── requirements.txt                  # Project dependencies
├── Dockerfile                        # For containerizing the application
└── README.md                         # Project documentation
```

## Setup Instructions

### 1. Clone the repository and navigate to the project directory
```bash
git clone https://github.com/yourusername/housing-price-prediction.git
cd housing-price-prediction
```

### 2. Create and activate a virtual environment
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Run the data creation script (if you need to generate a new dataset)
```bash
python src/data_creation.py
```

### 5. Preprocess the data and train the model
```bash
python src/data_preprocessing.py
python src/model_training.py
```

## Deployment Options

### Option 1: Local Deployment with FastAPI

1. Navigate to the API directory
```bash
cd api
```

2. Run the FastAPI application
```bash
uvicorn app:app --reload
```

3. Access the API documentation at `http://localhost:8000/docs`

### Option 2: Docker Deployment

1. Build the Docker image
```bash
docker build -t housing-price-api .
```

2. Run the Docker container
```bash
docker run -d -p 8000:8000 housing-price-api
```

3. Access the API documentation at `http://localhost:8000/docs`

### Option 3: Cloud Deployment (AWS Example)

1. Build the Docker image locally
```bash
docker build -t housing-price-api .
```

2. Tag the image for Amazon ECR
```bash
docker tag housing-price-api:latest [AWS_ACCOUNT_ID].dkr.ecr.[REGION].amazonaws.com/housing-price-api:latest
```

3. Push the image to ECR
```bash
aws ecr get-login-password --region [REGION] | docker login --username AWS --password-stdin [AWS_ACCOUNT_ID].dkr.ecr.[REGION].amazonaws.com
docker push [AWS_ACCOUNT_ID].dkr.ecr.[REGION].amazonaws.com/housing-price-api:latest
```

4. Deploy the container using AWS ECS/Fargate or Elastic Beanstalk

## API Usage

Once deployed, you can use the API to predict house prices:

```python
import requests
import json

# API endpoint
url = "http://localhost:8000/predict"

# Input data
data = {
    "area": 1800,
    "bedrooms": 3,
    "bathrooms": 2,
    "age": 15,
    "distance_to_city": 8.5
}

# Make the prediction request
response = requests.post(url, json=data)
result = response.json()

print(f"Predicted house price: ${result['predicted_price']:.2f}")
if result['confidence_interval']:
    print(f"Price range: ${result['confidence_interval']['lower_bound']:.2f} - ${result['confidence_interval']['upper_bound']:.2f}")
```

## Requirements Files

### Project requirements.txt
```
pandas==2.1.1
numpy==1.24.3
scikit-learn==1.3.0
matplotlib==3.7.3
seaborn==0.12.2
joblib==1.3.2
```

### API requirements.txt
```
fastapi==0.103.1
uvicorn==0.23.2
pydantic==2.3.0
joblib==1.3.2
pandas==2.1.1
numpy==1.24.3
scikit-learn==1.3.0
python-multipart==0.0.6
```

## Dockerfile
```dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY api/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY models/ /app/models/
COPY api/ /app/

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
```
