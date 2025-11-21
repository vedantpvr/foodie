#  Firebase Recipe Analytics Pipeline

---

## Architecture Overview

We mimic a real-world data flow:
1.  **Generate:** Create synthetic recipes and user interactions.
2.  **Ingest:** Upload JSON documents to Google Firestore (NoSQL).
3.  **ETL (Extract-Transform-Load):** Download data, flatten nested arrays, and save as relational CSVs.
4.  **Analyze:** Generate insights from the clean data.

---
## Project Structure 

```text
FOODIE/
├── docs/
│   ├── data_dictionary.md
│   └── ERD.png
├── node_modules/
├── output/
│   ├── charts/
│   │   ├── users/
│   │   ├── prep_time_histogram.png
│   │   ├── prep_vs_likes_scatter.png
│   │   ├── README_charts.txt
│   │   ├── top_ingredients_preview.csv
│   │   └── top_ingredients.png
│   ├── analytics_summary.txt
│   ├── ingredients.csv
│   ├── interactions.csv
│   ├── recipe.csv
│   ├── steps.csv
│   ├── users.csv
│   ├── validation_invalid.csv
│   ├── validation_report.csv
│   ├── validation_report.json
│   ├── validation_valid_summary.csv
│   └── Vedant_Raut_Data.pdf
├── .gitignore
├── analysis.js
├── export_etl.js
├── generate_charts.py
├── image-1.png
├── image.png
├── implementation.md
├── README.md
└── seeding_data.js
```

##  Part 1: Prerequisites & Firebase Setup (Start Here!)

Since this project uses a real Cloud Database, you need to set up a free Google Firebase project. Follow these exact steps:

### Step 1: Create the Project
1.  Go to the [Firebase Console](https://console.firebase.google.com/) and log in with your Google account.
2.  Click **"Create a project"**.
3.  Name it `foodie-lab` (or anything you want).
4.  Disable Google Analytics (not needed for this) and click **Create Project**.

### Step 2: Create the Database
1.  In the left sidebar, click **Build** → **Firestore Database**.
2.  Click **Create Database**.
3.  **Important:** Select **Start in Test Mode** (This allows read/write access for 30 days without complex security rules).
4.  Select a location (e.g., `nam5` or `asia-south1`) and click **Enable**.

### Step 3: Get Your Admin Credentials
This allows the Node.js scripts to talk to the cloud.
1.  Click the **Gear Icon ⚙️** (next to Project Overview) → **Project Settings**.
2.  Go to the **Service accounts** tab.
3.  Click **Generate new private key**.
4.  A file will download. **Rename this file** to: `serviceAccountKey.json`
5.  **Move this file** into the root folder of this project (where `package.json` is).

### Step 4: Set Up Local System 
1. Run this in CMD:    **npm install -g firebase-tools**
2. Verify installation:   **firebase --version**
3. Log in to Firebase: **firebase login**
4. Initialize Firebase in your project : **cd your-project-folder**
5. Run initialization : **firebase init**

---

##  Part 2: Installation

1.  **Install Node.js:** Ensure you have [Node.js](https://nodejs.org/) installed.
2.  **Install Dependencies:** Open your terminal in the project folder and run:

```bash```
npm install

##  Part 3: Running the Pipeline

1. **Seed the Database** Generates recipes (including the "Gold Standard" Puran Poli) and simulates user traffic.
            node seeding_data.js

   **Expected Console Output** 
            Connecting to Firebase...
            ✔ Firebase connected successfully.
            🎉 Seeding completed successfully!

2. **Run ETL (Extract & Transform)** Connects to Firestore, fetches the data, flattens nested ingredients/steps, and saves them as CSVs.
            node export_etl.js

    **Expected Console Output**
            ✔ CSV files written to /output/
            🎉 ETL DONE — All tables exported!

3. **Validate Data Quality** Runs automated checks to ensure no IDs are missing and the schema is correct.
            node validate_rule.js

    **Expected Console Output**
             Writing results...
             validation_invalid.csv
             validation_valid_summary.csv
            validation_report.json
             Validation completed successfully!

4. **Generate Analysis** Reads the CSV files and calculates business metrics (e.g., Popularity vs. Prep Time).
            node analysis.js

    **Expected Console Output**
            📁 Writing analytics_summary.txt...
            ✔ analytics_summary.txt saved in /output/
