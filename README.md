

# Road-Hazard-Classification-Ensemble

## Project Overview

This project implements a high-accuracy, three-class image classification system designed to support a public safety application (e.g., a geotagging platform). The primary goal is to **automatically verify and classify** user-submitted road imagery into one of three critical categories: **Pothole**, **Manhole**, or **Normal Road**.

The system achieves maximum robustness and accuracy by utilizing a **Deep Learning Ensemble** approach, ensuring reliable data for warnings against road damage and uncovered manholes, which pose severe risks, especially during heavy rain or flood conditions.

***

## Technical Solution and Model Performance

The core of the system is built on **Transfer Learning**, fine-tuning two distinct state-of-the-art models pre-trained on ImageNet.

### Final Performance

The **Soft Voting Ensemble** successfully increased accuracy by leveraging the models' different error patterns, validating the ensemble strategy.

| Metric | InceptionV3-only | InceptionResNetV2-only | **Soft Voting Ensemble** |
| :--- | :--- | :--- | :--- |
| **Validation Accuracy** | 94.75% | 95.00% | **96.00%** |
| **Final Loss** | 0.0857 | 0.1502 | N/A (Averaged Probabilities) |

### Model Architecture and Training

| Model Name | Base Architecture | Final Layer (Classes) | Total Epochs | Stable Learning Rate |
| :--- | :--- | :--- | :--- | :--- |
| **Model 1** | InceptionV3 | `Dense(3, softmax)` | 40 | $10^{-5}$ |
| **Model 2** | InceptionResNetV2 | `Dense(3, softmax)` | 20 | $10^{-5}$ |

**Key Technical Details:**

* **Ensemble Method:** **Soft Voting** (Averaging Probabilities).
* **Stability Fix:** Training required reducing the **Learning Rate (LR) to $10^{-5}$** to overcome the initial `NaN` loss instability common in high-depth CNNs.
* **Model Checkpointing:** Future training is configured to use a `ModelCheckpoint` callback, saving only the version with the lowest `val_loss` to prevent overfitting.

***

## Dataset Preparation

The training required building a clean, balanced dataset from mixed-source inputs.

* **Total Final Images:** **4020** (Images that are either Pothole, Manhole, or Normal road).
* **Class Distribution:** The final distribution is highly balanced, with each class representing approximately $32.2\%$ to $35.0\%$ of the total data, eliminating class bias.
* **Data Integrity:** A dedicated script was used to remove corrupted and irrelevant stylized/cartoon files from the dataset, ensuring the model only trained on real-world road features.
* **Augmentation Strategy:** Controlled augmentation (Rotation, Blur, Brightness) was applied to the initial set of images to boost diversity without causing compounding redundancy.

***

## Future Goals and Deployment

1.  **Model Optimization:** Convert the final ensemble models to **TensorFlow Lite** format to reduce size and latency for potential integration into a mobile application.
2.  **Web API:** Deploy the ensemble prediction function into a lightweight, scalable web service (e.g., using Python Flask) for public geotagging and verification.
3.  **Data Expansion:** Introduce more diverse hazard categories (e.g., severe cracks, eroded shoulders) to create a comprehensive road quality assessment system.

***

## Suggested Image Placeholders

To make your `README.md` visually appealing and informative, include these images:

| Section | Image Suggestion | Placeholder |
| :--- | :--- | :--- |
| **Project Overview** | A simple, clear diagram showing a user uploading a geo-tagged image and the model classifying it. |  |
| **Model Performance** | The final **Training & Validation Accuracy/Loss Plot** for the 30-epoch training run. (This is the most crucial visualization.) |  |
| **Technical Details** | An image showing the **three final output classes** for the ensemble (e.g., three separate cropped examples of a Pothole, a Manhole, and a Normal road). |  |
