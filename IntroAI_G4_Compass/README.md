# Project title
**Compass** - Campus Buidling Recognition and Navigation System at NCCU

## Project description 
  
  Compass: NCCU Map is an AI-powered building identification and navigation assistant for National Chengchi University (NCCU), built to help first-year students, exchange students, and visitors who struggle to identify campus buildings labelled only with Chinese names or codes and to navigate the university's large hillside campus. Users can identify a building either by submitting a photo, which a Convolutional Neural Network recognises, or by entering a known building code. 
  
  Recognition is geolocation-aware, combining image features with the user's GPS coordinates to improve accuracy, and the result expands into a full information page with the building's name, facilities, departments, photos, and Google Maps walking directions to the building and nearest bus stop. To keep results reliable, the system cross-checks predicted coordinates against the user's location and applies confidence thresholds that trigger a graceful "Not Found" response when an image or code cannot be matched. The model is trained photographs per building, each labelled with its code and paired with GPS coordinates, and is evaluated using accuracy and a confusion matrix before deployment at a target accuracy of at least 90%.

## Getting started

**1. Prerequisites Software:**

  - Colab - Python
  - Hugging Face Account: A valid Hugging Face Write Token stored in your workspace environments/secrets as HUGGING_FACE.

**2. Installation & Dependencies**

  - Install the required packages by running:
    pip install -q gradio folium

**3. Required Data&Setup**

  - Required Data & Setup
      Dataset Structure: Organize your campus building images on Google Drive under /MyDrive/NCCU_BUILDINGS/. Ensure subfolders match the building name keys exactly (e.g., Administration Building, Main Library).
  - Model Weights: If skipping training, ensure you have the trained weights file nccu_campus_model.h5 inside your root directory, or fetch it directly from your Hugging Face space repository (bonki67/nccu-compass).

**4. Pipeline Usage**

   - Data Cleaning: Automatically detects and removes corrupt images using multithreaded PIL decoding.
   - Preprocessing & Augmentation: Images are resized to 224x224, normalized ($/255.0$), and mapped dynamically using a tf.data pipeline.
   - Model Fine-Tuning: Loads an ImageNet-pre-trained MobileNetV2 backbone, adds Custom Classification heads (GlobalAveragePooling2D $\rightarrow$ Dense(128) $\rightarrow$ Softmax), and trains using the Adam optimizer with Early Stopping.
   - App Deployment: Launches an interactive Gradio GUI that allows users to upload a campus photo to predict the building name, view its key facilities, and render its GPS location interactively on a Folium Map.
   - HF Hub Sync: Automatically pushes the finalized nccu_campus_model.h5 directly to Hugging Face Spaces upon completion.


## File structure

The project is organized as follows:

**nccu-compass**
      
      - NCCU_Compass.ipynb — Main training notebook containing data loading, image augmentation, MobileNetV2 transfer learning model, training loop, and accuracy evaluation
      - app.py — Gradio web application deployed on HuggingFace Spaces with three tabs: Identify by Photo, Search by Code, and Bus Timetable
      - nccu_campus_model.h5 — Trained CNN model file saved after training, loaded by app.py at runtime

**photos** 
      
      — Curated reference photos for each building, displayed in the app after a building is identified or searched. Named by building code (e.g. 16_1.jpg, 16_2.jpg). Hosted on HuggingFace at bonki67/nccu-compass.
**dataset** (stored on Google Drive) 
     
      — 25 classes, 1,420 images total, collected via ground-level photography and drone footage across NCCU campus:
Administration Building — 20 images
         
          Art & Culture Center — 53 images
          Bai Nian Building (College of Liberal Arts) — 51 images
          College of Commerce — 95 images
          College of Communication — 26 images
          College of Law — 65 images
          Computer Center — 61 images
          Da Yong Building — 27 images
          Dao Fan Building (College of Foreign Language) — 37 images
          General Building of Colleges — 76 images
          Information Building — 61 images
          International Building — 62 images
          Ji Tao Building — 54 images
          Ji Ying Building — 11 images
          Lohas Plaza — 51 images
          Main Library — 46 images
          Si Wei Hall — 77 images
          Student Club Center — 50 images
          Syue Sih Building — 19 images
          Yi Xian Building — 40 images
          Zi Chiang Dormitory 5 & 6 — 18 images
          Zi Chiang Dormitory 7 & 8 — 10 images
          Zi Chiang Dormitory 9 — 28 images
          Zi Chiang Dormitory 10 — 37 images
          unknown — 350 images (non-campus / ambiguous scenes, added to reduce false positive predictions)

## Analysis
  **Methodology**

      - Model Architecture: The core architecture leverages Transfer Learning built upon a pre-trained CNN base network (optimized for visual feature extraction from building images).
      - Multi-Input Fusion: The network processes dual inputs:
            + Image data passing through CNN layers.
            + Numerical GPS data (Latitude and Longitude) paired as auxiliary inputs to cross-check realistic predictions and prevent out-of-bounds mismatches.
      - Judgement Logic: A confidence score threshold filters unrealistic predictions. If an image is unrecognizable or a code does not exist, the system gracefully triggers "Not Found" handling
      
**Training Data**
     
      - Images: A comprehensive dataset featuring multiple photos captured per building.
      - Labels & Metadata: Every image is labeled with its corresponding building code. Auxiliary data includes exact GPS coordinates, alongside building descriptive metadata (facilities, departments, bus stops).

## Results

During training across multiple epochs, the model successfully demonstrated optimal convergence:
    
    - Loss Reduction: Both Training Loss and Validation Loss steadily decreased, indicating efficient learning curves without severe overfitting.
    - Accuracy Improvement: Final metrics showed robust Train and Validation Accuracy, verifying that the combination of CNN spatial feature extraction and GPS boundaries yields high-precision classification for campus identification.

## Contributers

This project was developed by a team of four members. All members contributed jointly to data collection, travelling around the NCCU campus to take real-life photographs of the buildings used to train and test the model.

  **Engineering/Development**
        Nguyen Ha Thuy Duong: Engineer / Coder
        Dang Thu Uyen: Engineer / Coder
            Responsible for building and training the Convolutional Neural Network model, implementing the geolocation-aware prediction pipeline, integrating the image recognition and code-based search functions, connecting the system to Google Maps for navigation, and handling data preprocessing, model evaluation, and the confidence-threshold and "Not Found" handling logic.

  **Design**
        Hoang Linh Nhi: Designer
        Lai Boi Nhu: Designer
            Responsible for the user interface and overall user experience, including the building identification and search screens, the building information pages, the navigation views, and the visual presentation and layout of the project materials.

## Acknowlegemendts

We would like to express our gratitude to Professor Pien Chung–pei for providing the advice and campus context that inspired this project.


## References
      - AI model: https://bonki67-nccu-compass-test.hf.space/?view=api 
      - Dataset: https://drive.google.com/drive/folders/1uxquqnzCbrU2pKlzaAhdUQGIDKc-ijzS?usp=drive_link 
      - Poster: https://drive.google.com/file/d/1c8LPzRnSokywIjmuKGpd-iywnG1p6WO9/view?usp=sharing 
