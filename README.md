# Electrical Fault Classification in Three-Phase Transmission Lines

This project uses machine learning to identify the type of fault on a three-phase
transmission line from current and voltage measurements. I am an electrical
engineering student, and I wanted to see how well standard classifiers handle a
problem that is normally solved with protection relays and fixed thresholds.

## Dataset

Electrical Fault Detection and Classification dataset (Kaggle), `classData.csv`.

- 7,861 samples
- Inputs: phase currents (Ia, Ib, Ic) and phase voltages (Va, Vb, Vc)
- Output: fault condition, encoded in four binary columns (G, C, B, A) that mark
  which phases and whether ground are involved

The four columns were combined into six classes:

| Code (GCBA) | Fault type | Samples |
|-------------|------------|---------|
| 0000 | No fault | 2365 |
| 1001 | Line to ground (A-G) | 1129 |
| 0110 | Line to line (B-C) | 1004 |
| 1011 | Double line to ground (A-B-G) | 1134 |
| 0111 | Three phase (LLL) | 1096 |
| 1111 | Three phase to ground (LLLG) | 1133 |

There were no missing values.

## Method

1. Combined the G, C, B, A columns into a single fault label
2. Split the data 80/20 into training and test sets, stratified by fault type
3. Trained and compared three models with scikit-learn:
   Logistic Regression (with feature scaling), Decision Tree and Random Forest
4. Evaluated with accuracy, a per-class classification report and feature importance

## Results

| Model | Test accuracy |
|-------|---------------|
| Decision Tree | 88.05% |
| Random Forest | 86.78% |
| Logistic Regression | 34.58% |

Observations:

- No fault, LG, LL and LLG faults were classified perfectly (precision and recall of 1.00).
- Almost all errors come from confusing LLL with LLLG. A three-phase fault is
  balanced, so the phase currents sum to nearly zero and very little current flows
  to ground even when ground is involved. The two faults therefore look almost the
  same in phase measurements.
- Logistic Regression performs poorly because the data is not linearly separable.
  Each row is an instantaneous sample of an AC waveform, so a large positive and a
  large negative current can both indicate the same fault.
- Phase currents were more important to the Random Forest than phase voltages,
  which matches the fact that currents change much more sharply during a fault.

## Possible improvements

- Use a short window of consecutive samples instead of a single instant, so the
  model sees the waveform rather than one point on it
- Add derived features such as current magnitudes or sequence components
- Include a neutral or ground current measurement, which would help separate
  LLL from LLLG
- Try gradient boosting models and tune hyperparameters

## Running the notebook

1. Download `classData.csv` from the Kaggle dataset page
2. Open the notebook in Google Colab or Jupyter and place the CSV in the same folder
3. Run all cells

Requirements: Python 3, pandas, scikit-learn

## Tools

Python, pandas, scikit-learn, Google Colab
