# 🧠 Stress Level and Behavioral State Classification Using EEG and Vision-Based Analysis

---

## 📘 Overview  
This project presents a **real-time multimodal mental state monitoring system** combining **EEG signals** and **facial behavioral cues** to assist psychiatrists in early-stage stress diagnosis and cognitive evaluation.  
It integrates **EEG, Behavioral Vision Model, and Questionnaire data (GAD-7, PHQ-9, PSS, ADHD)** into a unified dashboard for real-time analysis.

> Detects: **Relaxed | Focused | Stressed | Distressed | Drowsy**

---

## 🚀 Key Features  
- 🧩 **EEG-Based Mental State Detection** (Single-channel EEG)  
- 👁️ **Behavioral Analysis via Webcam (Blink Rate, Emotion, Fatigue)**  
- ⚙️ **Ensemble Machine Learning** (XGBoost + LightGBM + Random Forest)  
- 🤖 **CNN + Random Forest for Facial Emotion Classification**  
- 📊 **SHAP Explainability** for clinical interpretability  
- 🌐 **Flask Web App** for real-time results and downloadable reports  
- 💻 **Full-stack deployment** (Localhost or Render Cloud)  
- 🧾 **PDF & CSV Reports** for psychiatrists  

---

## 🧩 System Architecture  

EEG Sensor (Neuroscience Kit)
↓
Signal Filtering & Feature Extraction
↓
Soft Voting Ensemble (XGBoost + LGBM + RF)
↓
EEG Classification (5 States)
↓
Webcam Feed (OpenCV + MediaPipe)
↓
Facial Emotion CNN + Blink/Fatigue Metrics
↓
Random Forest → Behavioral Classification
↓
Fusion Dashboard (EEG + Behavioral + Questionnaire)
↓
SHAP Explainability + PDF/CSV Reports




---

## ⚙️ EEG Analysis Pipeline  

| **Component** | **Details** |
|----------------|-------------|
| **Device** | Upside Down Labs Neuroscience Kit (Fp1/Fp2 electrodes) |
| **Preprocessing** | 0.5–45 Hz Butterworth Bandpass Filter |
| **Features** | Band Power Ratios (β/α, θ/β), Spectral Entropy, Relative Power |
| **Model** | Soft Voting Ensemble (XGBoost + LightGBM + Random Forest) |
| **Performance** | Accuracy = 85.2%, Macro F1 = 0.83 |
| **Interface** | Flask-based real-time classifier & SHAP viewer |

---

## 👁️ Behavioral Analysis Model  

### 🎯 Objective  
To perform real-time monitoring of **facial emotion, blink patterns, fatigue, and micro-expressions** to estimate stress and engagement levels.  

---

### 🔍 Features Extracted  

| **Feature** | **Description** |
|--------------|-----------------|
| **EAR (Eye Aspect Ratio)** | Detects drowsiness and blink frequency |
| **Blink Rate (BPM)** | Indicator of fatigue |
| **Smile Ratio** | Facial expression engagement |
| **Brow Furrow Score** | Stress/anger intensity |
| **Movement & Tilt Code** | Head movement tracking |
| **Emotion Label** | CNN-based facial emotion classification |
| **Fatigue Score** | Computed from EAR, blinks, and facial tension |

---

### 🧠 Model Architecture  

| **Model** | **Purpose** | 
|------------|--------------|
| **CNN (Keras)** | Feature extraction from behavioral inputs |
| **Random Forest (Scikit-learn)** | Final behavioral state classification |
| **Scaler + Label Encoder** | Feature normalization & label mapping |
| **MediaPipe FaceMesh** | Landmark tracking for EAR & movement |

---

### ⚡ Real-Time Process  

1. Webcam captures facial frames.  
2. MediaPipe tracks face landmarks (eyes, brows, lips).  
3. Emotion detected using pretrained CNN (`emotion_model.h5`).  
4. Blink rate & fatigue calculated every 10 seconds.  
5. Features scaled and CNN embeddings extracted.  
6. Random Forest predicts **behavioral label** (Focused, Drowsy, Stressed, etc.).  
7. Results saved as a CSV file in `reports/`.  

> Example Output File:  
> `reports/Abhiram_Behavioral_20251105_103055.csv`
> https://drive.google.com/file/d/1iI1P38PRPfa66XQlBXyW9Kz_uOHWbj5w/view
---

### 🧾 Example Behavioral Output Summary  

| **Metric** | **Value** |
|-------------|-----------|
| Average EAR | 0.26 |
| Blink Rate | 17 BPM |
| Emotion | Neutral |
| Predicted Label | Focused |
| Fatigue Score | 0.38 |

---

## 🧪 Dataset and Training  

| **Data Type** | **Source** | **Features** |
|----------------|-------------|---------------|
| **EEG** | Custom dataset (26 participants × 4 sessions) | Band Power, Entropy, Ratios |
| **Behavioral** | Webcam recordings, FER2013 | EAR, Blink, Emotion, Fatigue |
| **Questionnaire** | GAD-7, PHQ-9, PSS, ADHD | Psychological indicators |

---

## 🧰 Technologies Used  

- **Languages:** Python, HTML, CSS, JavaScript  
- **Frameworks:** Flask, TensorFlow, Scikit-learn, MediaPipe  
- **Libraries:** NumPy, Pandas, Matplotlib, OpenCV, SHAP  
- **Hardware:** Upside Down Labs Neuroscience Kit + Webcam  
- **Deployment:** Render / Localhost  

---

## 📝 Questionnaire Integration (GAD-7, PHQ-9, PSS, ADHD)

### 📌 Overview
The web app includes an interactive questionnaire flow to collect standard psychometric measures alongside EEG & behavioral data.  
Questionnaires implemented:

- **GAD-7** — Generalized Anxiety (7 items)
- **PHQ-9** — Depression (9 items)
- **PSS** — Perceived Stress Scale (10 items; some reversed)
- **ADHD** — Attention-related (6 items)

All questionnaires can be run individually or as a single combined sequence (`/questionnaire/all`), which is used in the **Run All** flow (captures EEG and behavioral data while the user answers).

---

### 🔗 Routes & Flow

- `GET /` → `personal_info()` — Personal details form (name, age, gender, email). On submit redirects to `/dashboard`.
- `GET /dashboard` → Dashboard landing (start EEG, Behavioral, Questionnaire or Run All).
- `GET /questionnaire_menu` → Menu with options to run each questionnaire or all.
- `GET /questionnaire/<category>?start=true` → Initialize and start the chosen questionnaire. `category ∈ {gad, phq, pss, adhd, all}`.
- `POST /questionnaire/<category>` → Submit an answer for the current question (handles next/back/submit).
- `GET/POST /submit/<category>` → Score and finalize the category (saves CSV + PDF), rendering `result.html`.
- `POST /run_all_sequence` → Starts EEG & Behavioral in background and redirects user to the combined questionnaire flow.

> The app stores per-category progress and timing in `session` (e.g., `answers_gad`, `times_gad`, `start_time_gad`). That allows navigation back/forward while preserving answers and measuring time spent per question.

---

### 🧮 Scoring Rules (what the README should document)

**GAD-7 (7 items)**  
- Answers use the 0–3 scale: `["Not at all", "Several days", "More than half the days", "Nearly every day"]`  
- **Score** = sum(answers) → Interpretation:  
  - `0–4 = Minimal`  
  - `5–9 = Mild`  
  - `10–14 = Moderate`  
  - `15–21 = Severe`

**PHQ-9 (9 items)**  
- Same 0–3 label scale as GAD-7  
- **Score** = sum(answers) → Interpretation:  
  - `0–4 = Minimal`  
  - `5–9 = Mild`  
  - `10–14 = Moderate`  
  - `15–19 = Moderately Severe`  
  - `20–27 = Severe`

**PSS (10 items, some reversed)**  
- Labels: `["Never","Almost Never","Sometimes","Fairly Often","Very Often"]` → 0–4  
- Some items are *reverse scored* (your app stores those flags). Reverse logic: `val = 4 - original_val` for reversed items.  
- **Score** = sum(all adjusted items) → Interpretation:  
  - `0–13 = Low`  
  - `14–26 = Moderate`  
  - `27–40 = High`

**ADHD (6 items)**  
- Labels: `["Never","Rarely","Sometimes","Often","Very Often"]` → map to 0–4  
- Heuristic: if count of answers ≥ 4 equals `>= 4`, return `Suggestive of ADHD`, else `Not Suggestive`

---

### 💾 Saved Outputs
- Each category result is written to: `reports/<Name>_<category>.csv` and `reports/<Name>_<category>.pdf`  
- Combined (`all`) results are saved to `reports/<Name>_all.csv` and `.pdf` (includes questionnaire + EEG ML & rule-based summaries + behavioral summary).

---


🧾 How to Run the Project

🧱 Clone Repository
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

📦 Install Dependencies
pip install -r requirements.txt

▶️ Run Flask Application
python app.py


Then open in browser:
👉 http://127.0.0.1:5000/



## 🖼️ Sample Outputs  
<img width="1366" height="768" alt="bci dashboard" src="https://github.com/user-attachments/assets/9b9fbbee-dd5c-4062-9f86-09c2a17afb98" />
<img width="879" height="585" alt="model" src="https://github.com/user-attachments/assets/a2150b18-72f8-4bae-94b4-03d90f7a2b2d" />
<img width="1366" height="768" alt="eeg signal recording" src="https://github.com/user-attachments/assets/d05628ef-3af2-4476-8e76-b695a7568ec5" />
<img width="1362" height="618" alt="questionary block" src="https://github.com/user-attachments/assets/b575f265-4e8e-48b9-8bb0-83c726fdf65c" />
<img width="345" height="292" alt="Screenshot from 2025-11-05 10-48-01" src="https://github.com/user-attachments/assets/6b8a116f-0df7-46f2-98cd-8b4c3da47db9" />
<img width="253" height="217" alt="Screenshot from 2025-11-05 10-48-29" src="https://github.com/user-attachments/assets/4f89b6f4-0bee-4d34-bbe1-ea8f46587927" />
<img width="292" height="167" alt="Screenshot from 2025-11-05 10-48-55" src="https://github.com/user-attachments/assets/10bed38b-e459-46c2-9c0f-919b4db40555" />
<img width="665" height="305" alt="Screenshot from 2025-11-05 10-49-21" src="https://github.com/user-attachments/assets/6b03d88f-61cf-437d-91f2-e0fbb39aa8ab" />



| **Image**                 | **Description**                         |
| ------------------------- | --------------------------------------- |
| `electrode_placement.png` | EEG electrode setup                     |
| `confusion_matrix.png`    | Ensemble model results                  |
| `shap_summary.png`        | SHAP interpretability plot              |
| `dashboard.png`           | Flask dashboard interface               |
| `behavioral_features.png` | Live webcam analysis with emotion & EAR |
| `report_output.png`       | Generated diagnostic report sample      |


