# Agri-Credit-Risk-Predictor-Micro-loans-

[![Python](https://img.shields.io/badge/Python-100%25-blue.svg)](https://www.python.org/)
[![Streamlit](https://img.shields.io/badge/Streamlit-App-red.svg)](https://streamlit.io/)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](LICENSE)

CropCredit is a machine-learning application for assessing agricultural commodity risk and supporting micro-loan decisions for smallholder farmers. It combines a 90-day price forecast, spoilage-risk prediction, and dynamic loan-to-value (LTV) calculations in an interactive Streamlit dashboard.

> This tool provides decision support and is not a substitute for professional lending, financial, or agricultural advice.

## Features

- **Financial risk:** XGBoost-based commodity price forecasting
- **Physical risk:** Random Forest classification for spoilage probability
- **Yield optimization:** 90-day price simulation and optimal selling-window estimation
- **Loan assessment:** Dynamic approval/rejection, LTV, collateral value, and sanctioned amount
- **Reverse collateral calculator:** Estimates additional commodity tonnage needed to cover a loan shortfall
- **Interactive dashboard:** Commodity, market, loan, and warehouse sensor inputs

## Supported Commodities

| Commodity | Category | Spoilage sensitivity |
|---|---|---:|
| Wheat | Grain | 0.7x |
| Rice | Grain | 0.9x |
| Potato | Vegetable | 1.3x |
| Onion | Vegetable | 1.0x |
| Tomato | Vegetable | 2.0x |

## How It Works

1. Select a commodity and enter the tonnage, market arrivals, mandi price, loan request, and warehouse conditions.
2. The financial model predicts a 90-day future price.
3. The physical-risk model estimates spoilage probability from temperature, humidity, and moisture content.
4. Spoilage risk determines the LTV: 85% below 30%, 60% from 30% to 70%, and rejection above 70%.
5. The application calculates collateral value and the maximum sanctioned loan.
6. A 90-day price curve identifies a preferred sale window for collateral liquidation.

## Project Structure

```text
.
├── app.py                         # Streamlit dashboard and inference engine
├── train_models.py                # Model training pipeline
├── tune_hyperparameters.py        # Hyperparameter tuning utilities
├── requirements.txt               # Python dependencies
├── label_encoder.pkl              # Root-level label encoder artifact
├── physicalriskmodel.pkl          # Root-level physical-risk artifact
├── Data files/                    # Source and cleaned datasets
└── models/
    ├── label_encoder.pkl
    ├── financialriskmodel_*.pkl
    └── physicalriskmodel_*.pkl
```

## Installation

### Prerequisites

- Python 3.9 or newer
- `pip`

### Setup

```bash
git clone https://github.com/erico19/Agri-Credit-Risk-Predictor-Micro-loans-.git
cd Agri-Credit-Risk-Predictor-Micro-loans-

python -m venv .venv
source .venv/bin/bin/activate
pip install -r requirements.txt
```

On Windows, activate the virtual environment with:

```bash
.venv\Scripts\activate
```

## Run the Application

The repository includes pre-trained model artifacts. Start the dashboard with:

```bash
streamlit run app.py
```

Open the local URL shown by Streamlit, usually `http://localhost:8501`.

## Train the Models

Place the cleaned data files for each supported commodity in `Data files/` using these filenames:

- `cleaned_wheat_data.csv`
- `cleaned_rice_data.csv`
- `cleaned_potato_data.csv`
- `cleaned_onion_data.csv`
- `cleaned_tomato_data.csv`

Each training file must contain:

- `commodity_encoded`
- `tonnage`
- `market_arrivals`
- `current_price`
- `rainfall_deficit`
- `warehouse_temp`
- `humidity`
- `moisture_content`
- `future_price`
- `spoilage_label`

Run:

```bash
python train_models.py
```

The script trains separate XGBoost and Random Forest models for each commodity and saves artifacts under `models/`.

## Model Details

### Financial Risk Model

- Algorithm: `XGBRegressor`
- Target: future commodity price
- Evaluation: MAE, R², and 5-fold cross-validation R²
- Test split: 20%
- Random seed: 42

### Physical Risk Model

- Algorithm: `RandomForestClassifier`
- Target: spoilage risk label
- Techniques: balanced class weights and out-of-bag scoring
- Evaluation: AUC-ROC, OOB accuracy, and classification report
- Test split: 20%
- Random seed: 42

## Input Features

| Feature | Description |
|---|---|
| `commodity_encoded` | Encoded commodity identifier |
| `tonnage` | Stored commodity quantity in metric tonnes |
| `market_arrivals` | Daily market arrivals in quintals |
| `current_price` | Current mandi price per quintal |
| `rainfall_deficit` | Rainfall deficit or supply-shock indicator |
| `warehouse_temp` | Warehouse temperature in degrees Celsius |
| `humidity` | Relative warehouse humidity percentage |
| `moisture_content` | Commodity moisture percentage |

## Data

The `Data files/` directory contains cleaned and raw commodity datasets, weather data, and data-processing scripts. The cleaned CSV files are used by `train_models.py`.

## Responsible Use

The risk thresholds, LTV ratios, and sanctioning rules are configurable policy choices embedded in the application. Before real-world use, validate them against local regulations, lending policies, and representative out-of-sample data. Model outputs should be monitored for bias and distribution drift.


Author
Eric Mafabi
Systems Engineer | Full-Stack Developer | Software Testing | Systems Analyst | UI/UX Designer | Experienced Tech Support Specialist | Open to new opportunities.

## License

This project is licensed under the [GNU General Public License v3.0](LICENSE).
