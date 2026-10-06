# 🔐 Password Strength Classification

A machine learning project that classifies passwords as **Weak**, **Medium**, or **Strong**. Password strength estimation is framed as a multiclass text classification problem, combining hand-crafted structural features with character n-gram patterns.

## Highlights

- Multiclass classification on about 670,000 labeled passwords
- Engineered features: length, character-type counts, unique-character ratio, and a custom complexity score
- Character-level TF-IDF n-grams combined with numeric features into a hybrid feature space
- Logistic Regression with balanced class weights reaching **~99.99% validation accuracy**
- Feature importance analysis showing which signals drive the predictions

## Dataset

The dataset (`data.csv`) has two columns: `password` and `strength` (0 = Weak, 1 = Medium, 2 = Strong). After removing one row with a missing password, it contains 669,639 passwords.

| Strength | Label | Count | Share |
|----------|-------|-------|-------|
| 0 | Weak | 89,701 | 13.4% |
| 1 | Medium | 496,801 | 74.2% |
| 2 | Strong | 83,137 | 12.4% |

The classes are imbalanced, with Medium passwords dominating.

## Approach

1. **Exploratory data analysis**
   - Checked class distribution and missing values.
   - Compared password length across strength classes with summary statistics and boxplots.
2. **Feature engineering**
   - `password_length` and `unique_ratio` (unique characters divided by length).
   - Character counts: `n_digit`, `n_lower`, `n_upper`, `n_symbol`.
   - `complexity_score` (digits x 1 + uppercase x 2 + symbols x 3) and `complexity_density` (score divided by length).
3. **Character n-grams**
   - Applied TF-IDF on character 2-grams and 3-grams (top 1,000 features) to capture patterns such as repeated digits.
4. **Hybrid feature space**
   - Stacked the TF-IDF features with the 7 numeric features, giving 1,007 features in total.
5. **Modeling**
   - Stratified 85/15 train/validation split (569,193 / 100,446 passwords).
   - Trained a `LogisticRegression` with `class_weight="balanced"` to handle the class imbalance.
6. **Evaluation and interpretation**
   - Accuracy, classification report, and confusion matrix.
   - Feature importance from the mean absolute model coefficients.

## Results

| Metric | Value |
|--------|-------|
| Validation accuracy | 0.9999 |
| Precision, recall, F1 (all classes) | 1.00 |

Most influential features by mean absolute coefficient: `complexity_density`, `password_length`, `n_symbol`, `n_upper`, and several repeated-digit n-grams such as `00`, `11`, and `55`.

**Note on the score:** in this dataset, password length almost perfectly separates the classes (Weak: up to 7 characters, Medium: 8 to 13, Strong: 14 or more). This suggests the labels follow rules based on length and character composition, so the near-perfect score reflects how well the model recovers those rules rather than a general measure of real-world password security.

## Tech Stack

- Python
- pandas, NumPy
- scikit-learn (TF-IDF, Logistic Regression, metrics)
- SciPy (sparse feature stacking)
- matplotlib, seaborn

## Getting Started

```bash
git clone <your-repo-url>
cd <your-repo-folder>
pip install pandas numpy scikit-learn scipy matplotlib seaborn
```

Place `data.csv` in the project root, then run:

```bash
jupyter notebook PasswordClassifier.ipynb
```

## Project Structure

```
.
├── PasswordClassifier.ipynb   # EDA, feature engineering, modeling, and interpretation
├── data.csv                   # Dataset (not included)
└── README.md
```

## Future Work

- Test the model on passwords labeled by an independent method (for example, an entropy-based estimator such as zxcvbn) to check how well it generalizes.
- Add features such as common-password and dictionary checks, keyboard patterns, and sequence detection.
- Compare other models (Random Forest, Gradient Boosting, Naive Bayes) and report macro-averaged metrics.
- Wrap the model in a small app (e.g., Streamlit) that scores a password as the user types.

## Author

Furkan
