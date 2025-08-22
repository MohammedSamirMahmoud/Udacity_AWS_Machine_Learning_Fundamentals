# Scones Unlimited: Vehicle Image Classification

## Project Introduction
This project demonstrates building an image classification model for **Scones Unlimited**, a scone-delivery-focused logistics company. The goal is to detect delivery vehicles (bicycles vs. motorcycles) to optimize order routing. The project leverages **AWS SageMaker, Lambda, and Step Functions** to build a scalable, event-driven ML pipeline.

### Use Cases
- Detect bicycles vs. motorcycles in delivery scenarios.
- Assign delivery professionals to nearby orders more efficiently.
- Optimize operations and routing for Scones Unlimited.
- Serve as a portfolio-ready demo of ML-enabled AWS applications.

---

## Project Steps Overview
1. **Data Staging** – Extract, transform, and load CIFAR-100 dataset images to S3.
2. **Model Training and Deployment** – Train an image classification model on SageMaker and deploy it.
3. **Lambdas and Step Function Workflow** – Create Lambda functions for serialization, classification, and threshold filtering, then orchestrate them using Step Functions.
4. **Testing and Evaluation** – Run test cases and visualize inference results.
5. **Optional Challenge** – Improve model performance or extend functionality.
6. **Cleanup Cloud Resources** – Remove resources to avoid unnecessary costs.

---

## Data Staging
- Extract CIFAR-100 dataset from [University of Toronto](https://www.cs.toronto.edu/~kriz/cifar-100-python.tar.gz).
- Transform images into a usable shape (32x32x3) and save as PNGs.
- Filter for relevant classes (Bicycles: label 8, Motorcycles: label 48).
- Upload processed images to S3:
  - `s3://<bucket>/train/`
  - `s3://<bucket>/test/`

---

## Model Training and Deployment
- Convert data to SageMaker-friendly metadata files.
- Use SageMaker’s built-in image classification algorithm.
- Train on `ml.p3.2xlarge` instance and deploy to `ml.m5.xlarge`.
- Configure **Model Monitor** to track endpoint performance.
- Deploy model and create a **Predictor** for inference.

---

## Lambda Functions
Three main Lambda functions are used:

1. **Serialize Image Data**  
   - Reads images from S3, base64 encodes them, returns to Step Function.

2. **Image Classification**  
   - Decodes base64 image, sends to SageMaker endpoint, returns predictions.

3. **Threshold Filtering**  
   - Checks model confidence against threshold (e.g., 0.93) and raises an error if not met.

---

## Step Functions Workflow
- Chaining the three Lambda functions in a **Standard Step Function**.
- Input and output fields are properly filtered (`$.body`).
- Supports automatic failure for low-confidence predictions.
- Example event object:
```json
{
  "image_data": "",
  "s3_bucket": "<MY_BUCKET_NAME>",
  "s3_key": "test/bicycle_s_000513.png"
}
