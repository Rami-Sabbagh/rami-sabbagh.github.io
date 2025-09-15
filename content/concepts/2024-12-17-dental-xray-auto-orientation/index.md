---
title: Dental XRAY
description: A report of creating an AI solution for auto-orientation of Dental XRAY images captured through a computer sensor.
summary: Report of creating an AI solution for auto-orientation of Dental XRAY images captured through a computer sensor.
date: 2024-12-17T12:02:00+03:00
draft: true
---

## Post Concept

A report of creating an AI solution for auto-orientation of Dental XRAY images captured through a computer sensor.


## Series

1. Preparing the dataset.
    - Explain the short-term objective of the project (auto-orientation).
    - Explain how the data is available.
    - How manual labelling was done.
        - The tooling used.
        - The collaborative labelling process.
    - Explain the pre-processing the data gone through.
        - Changing the labels format.
        - Differentiate the "frame" direction from the "teeth" direction.
        - Auto-detection of **frame** direction, unifying the orientation and correcting the label.
    - Demonstrate the imbalance of the dataset.
    - Explain the augmentation opportunity.
        - Efface the 4 corners.
    - Train-Validation-Test Split.
    - Using the HDF5 format.

2. Training a CNN classifier model.
    - Using PyTorch.
    - Using MlFlow for experiment tracking.
    - Notebook vs Classic.

3. Integrating the AI model with simple UI for capture.
    - Overview of the UI app.
    - How to integrate.
    - Packaging for customer distribution.

4. Optimizing the model for better performance.
