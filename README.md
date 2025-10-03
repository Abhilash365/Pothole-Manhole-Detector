

# Pothole-Manhole-Verification-Ensemble

## Project Overview

This project implements a high-accuracy, three-class image classification system designed to support a public safety application (e.g., a geotagging platform). The primary goal is to **automatically verify and classify** user-submitted road imagery into one of three critical categories: **Pothole**, **Manhole**, or **Normal Road**.

The system achieves maximum robustness and accuracy by utilizing a **Deep Learning Ensemble** approach, ensuring reliable data for warnings against road damage and uncovered manholes, which pose severe risks, especially during heavy rain or flood conditions.

<div align="center"> <img width="512" height="430" alt="OverView_IMG" src="https://github.com/user-attachments/assets/00197351-4466-4d2a-aa98-ee0b7f88cf2b" /></div>



***

## Technical Solution and Model Performance

The core of the system is built on **Transfer Learning**, fine-tuning two distinct state-of-the-art models pre-trained on ImageNet.

### Final Performance

The **Soft Voting Ensemble** successfully increased accuracy by leveraging the models' different error patterns, validating the ensemble strategy.

| Metric | InceptionV3-only | InceptionResNetV2-only | **Soft Voting Ensemble** |
| :--- | :--- | :--- | :--- |
| **Validation Accuracy** | 94.75% | 95.00% | **96.00%** |
| **Final Loss** | 0.1549 | 0.1502 | N/A (Averaged Probabilities) |

### Model Architecture and Training

| Model Name | Base Architecture | Final Layer (Classes) | Total Epochs | Stable Learning Rate |
| :--- | :--- | :--- | :--- | :--- |
| **Model 1** | InceptionV3 | `Dense(3, softmax)` | 20 | $10^{-5}$ |
| **Model 2** | InceptionResNetV2 | `Dense(3, softmax)` | 20 | $10^{-5}$ |

**Key Technical Details:**

* **Ensemble Method:** **Soft Voting** (Averaging Probabilities).
* **Stability Fix:** Training required reducing the **Learning Rate (LR) to $10^{-5}$** to overcome the initial `NaN` loss instability common in high-depth CNNs.
* **Model Checkpointing:** Future training is configured to use a `ModelCheckpoint` callback, saving only the version with the lowest `val_loss` to prevent overfitting.

***



## Dataset Preparation

The training required building a clean, balanced dataset from mixed-source inputs.

* **Total Final Images:** **4020 images** (Images that are either Pothole, Manhole, or Normal road).
* **Class Distribution:** The final distribution is highly balanced, with each class representing approximately $32.2\%$ to $35.0\%$ of the total data, eliminating class bias.
* **Data Integrity:** A dedicated script was used to remove corrupted and irrelevant stylized/cartoon files from the dataset, ensuring the model only trained on real-world road features.
* **Augmentation Strategy: Controlled Diversity**

    The dataset was expanded using **controlled augmentation** to boost diversity without causing compounding redundancy. The strategy created three unique variations for every original image:
    1.  **Rotation:** $90^{\circ}$ rotation to introduce different angular perspectives.
    2.  **Blur:** Application of a mild Gaussian Blur (or equivalent `ImageFilter.BLUR`) to simulate varying camera focus, weather conditions, or motion blur.
    3.  **Brightness Adjustment:** Increase in brightness (using a factor of $1.5$) to simulate different lighting conditions (e.g., bright sun or poor camera exposure).

    This process ensured a controlled **$4\times$ dataset increase** (1 Original + 3 Augmented) where each augmentation was applied only to the base image, preventing the creation of redundant, over-augmented files.

***


## Future Goals and Deployment

1.  **Model Optimization:** Convert the final ensemble models to **TensorFlow Lite** format to reduce size and latency for potential integration into a mobile application.
2.  **Web API:** Deploy the ensemble prediction function into a lightweight, scalable web service (e.g., using Python Flask) for public geotagging and verification.
3.  **Data Expansion:** Introduce more diverse hazard categories (e.g., severe cracks, eroded shoulders) to create a comprehensive road quality assessment system.

***


