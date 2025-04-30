# Simple Image Classification API (MobileNetV2)

## Project Overview

This project implements a basic image classification API using a pre-trained MobileNetV2 model from TensorFlow/Keras. It accepts an image file upload via a POST request and returns the top predicted object labels (based on ImageNet categories) along with their confidence scores.

This project demonstrates leveraging pre-trained deep learning models for Computer Vision tasks, handling image data within a web API, and serving predictions using Flask.

## Features

*   Loads the MobileNetV2 model pre-trained on the ImageNet dataset using `tf.keras.applications`.
*   Preprocesses uploaded images (resizing, pixel scaling using model-specific functions) to match the model's expected input format (224x224).
*   Provides a Flask API with two endpoints:
    *   `/` (GET): Returns a basic status message.
    *   `/predict` (POST): Accepts an image file upload (key: `file`) via `multipart/form-data`, performs classification, and returns a JSON response with the top 5 predictions: `{"predictions": [{"label": ..., "probability": ...}, ...]}`.
*   Includes steps for temporary deployment using Ngrok (useful for testing/Colab).

## Technology Stack

*   **Python 3.x**
*   **TensorFlow / Keras:** For loading the pre-trained model, image preprocessing (`tf.keras.preprocessing.image`, `tf.keras.applications.mobilenet_v2`), prediction, and decoding.
*   **Pillow (PIL):** For loading and manipulating image data from uploads in memory.
*   **NumPy:** Numerical operations (used heavily by TensorFlow).
*   **Flask:** Micro web framework for creating the API.
*   **Pyngrok (Optional but used in demo):** For creating secure tunnels to localhost (essential for testing from Colab).
*   **Requests (Optional):** General HTTP library.

## Setup and Installation (Colab Example)

1.  **Ngrok Setup (Required for Colab Access):**
    *   Sign up for a free account at [https://ngrok.com/](https://ngrok.com/).
    *   Get your Authtoken from [https://dashboard.ngrok.com/get-started/your-authtoken](https://dashboard.ngrok.com/get-started/your-authtoken).
    *   In a cell **before** the main application code cell, execute:
        ```python
        # Install necessary libraries
        !pip install pyngrok Flask tensorflow Pillow numpy requests

        # Configure ngrok - REPLACE YOUR_AUTHTOKEN
        # IMPORTANT: Do not commit your real token to public repositories!
        !ngrok config add-authtoken YOUR_AUTHTOKEN
        ```
2.  **Run the API Code:**
    *   Place all the Python code (imports, model loading, helper functions, Flask app definition, `if __name__ == '__main__':` block with pyngrok integration) into a **single Colab cell**.
    *   Run this main cell. On the first run, TensorFlow will download the MobileNetV2 weights (~14MB).
    *   The script will load the model, start Flask, start an Ngrok tunnel, and print the public URL (e.g., `https://<random-string>.ngrok-free.app`). **Note this URL.**

## How to Use the API

Once the API is running and you have the Ngrok URL:

**1. Check Status:**

*   Send a `GET` request to the base URL (e.g., `http://127.0.0.1:5001/` or your Ngrok URL).
*   **Expected Response:** `{"message": "Image Classification API is running!"}`

**2. Predict Image Class:**

*   Use a tool like Postman or `curl`.
*   **Method:** `POST`
*   **URL:** Use your Ngrok URL + `/predict` (e.g., `https://<your-ngrok-url>/predict`).
*   **Body Type:** `form-data`
*   **Key:** `file`
*   **Value:** Select/attach your image file (e.g., `dog.jpg`, `car.png`).
*   **Example `curl`:**
    ```bash
    # Make sure dog.jpg is in your current terminal directory
    curl -X POST -F "file=@dog.jpg" https://<your-ngrok-url>/predict
    ```
*   **Expected Response (example for a dog image):**
    ```json
    {
        "predictions": [
            {
                "label": "Labrador_retriever",
                "probability": 0.75...
            },
            {
                "label": "golden_retriever",
                "probability": 0.12...
            },
            // ... (up to 5 predictions)
        ]
    }
    ```

## Project Structure (Conceptual for Colab)
├── Image Classification Model.ipynb # Colab notebook containing all code
Or if split into files locally:
├── image_classifier_app.py # Main Flask app and functions
├── requirements.txt # List of dependencies
├── test_images/ # Folder for sample images
│ └── dog.jpg
└── README.md # This file


## Notes & Limitations

*   This is a functional prototype demonstrating model integration and API creation.
*   Relies on the pre-trained MobileNetV2 model; performance is limited by its training data (ImageNet) and architecture. It won't recognize highly specialized objects.
*   Deployment via Ngrok is temporary.
*   Minimal error handling for uploaded files (e.g., checking file size, advanced format validation).
*   Uses Flask development server; a production deployment would use Gunicorn/uWSGI.
