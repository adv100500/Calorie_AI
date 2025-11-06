# 🍽️ Calorie Estimation from Food Images  
_EPFL Capstone Project_

Predict **calories and macronutrients per 100g** directly from a **food image**, using a **multi-task deep learning model** inspired by Google Research's **Nutrition5k** paper.

<p align="center">
  <img src="https://github.com/user-attachments/assets/1244fa8d-1e4f-46a7-9011-bfc35790b484" width="420"/>
</p>

---

## 🎯 Problem Motivation
Manual calorie tracking is **slow**, **inaccurate**, and **annoying**.  
Goal: build a model that can **estimate nutritional values automatically** from images.

Dataset reference:  
- Nutrition5k Dataset → https://github.com/google-research-datasets/Nutrition5k  
- Paper → _Nutrition5k: Towards Automatic Nutritional Understanding of Generic Food_ ([PDF](https://arxiv.org/pdf/2103.03375))

---

## 🧠 Model Architecture

I use a **multi-task CNN**:

- **Backbone:** MobileNetV2 pretrained on ImageNet (frozen initially, then fine-tuned)
- **GlobalAveragePooling** to reduce parameters and avoid encoding portion size
- **Shared fully-connected layers** forming a compact latent representation
- **Two task-specific heads:**
  1. **Calories (kcal/100g) regression**
  2. **Macronutrient regression** (fat, carbs, protein)

Both prediction heads share the same convolutional backbone.  
Gradients from both tasks are combined (joint loss), so the backbone learns a shared representation capturing **nutrition-relevant visual cues** (texture, density, surface reflectance).  

---

## ⚙️ Training Setup

**Loss (MAE):**

$$
\mathcal{L} = \text{MAE}_{\text{kcal}} +
\frac{1}{3}\left(\text{MAE}_{\text{fat}} + \text{MAE}_{\text{carb}} + \text{MAE}_{\text{protein}}\right)
$$

- **Hardware:** A100 GPU  
- **Precision:** Mixed precision training  
- **Batch size:** 42  
- **Shuffle buffer:** 1000 samples
- **Augmentation:** random horizontal/vertical flips and small-angle rotations 
- **Optimizer:** RMSProp (as in Nutrition5k) + ReduceLROnPlateau  
- **Early Stopping:** patience = 22 epochs  

---

## 📊 Results
| Model | MAE ↓ | Notes |
|------|------|------|
| Ridge Regression | high | no spatial understanding |
| Random Forest | high | no spatial understanding |
| **Multi-task CNN (Final)** | **significantly lower error** | shared representation learns texture/composition |

*The multi-task model clearly outperformed all baselines.*

---

## 📂 Project Artifacts
| Item | Link |
|------|------|
| 📒 Full Training Notebook | [Github](https://github.com/adv100500/Calorie_AI/blob/7f14999de1a4178f6e5b0902ec04ee373e5da3b3/CAPTSTONE_EPFL_FULL.ipynb) |
| 🖥️ Final Presentation Slides | [PPT, github](https://github.com/adv100500/Calorie_AI/blob/7f14999de1a4178f6e5b0902ec04ee373e5da3b3/Presentation.pptx) |
| 📱 Android App (APK Download) | [Google drive](https://drive.google.com/file/d/170_sOFG1kISz2EWSmi7CLKYVLUlap4oN/view) |

---

## 📱 Mobile App Demo
<p align="center">
  <img src="https://github.com/user-attachments/assets/57f908f4-2abc-43ab-a0fb-0dcb5d9f86d1" width="300"/>
</p>

---
