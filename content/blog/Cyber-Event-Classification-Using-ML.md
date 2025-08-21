---
title: "Cyber Event Classification Using ML"

date: 2025-03-10T14:45:28-04:00
url: /Cyber-Event-Classification-Using-ML/
image: /images/2020-thumbs/Cyber-Event-Classification-Using-ML.jpg

draft: false
---

The Following is the code from a recent random forest classification model that I did. Some of the topics covered were TF-IDF, Dimensionality reductions, encoding, ensemble learning, and some model evaluation techniques.
<!--more-->

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/CyberClassifi/Random-Forest-Algorithm.jpg?raw=true)

This took many weeks of reading documentation and learning more about AI. I started trying to use deep learning but rethought my approach to be more based on what I already knew from statistics. This transition was a lot easier as my pretraining was essentially the same.

The primary goal of this project was to build a machine learning model capable of predicting key attributes of cybersecurity incidents based on textual descriptions. The attributes predicted include:

- Event Type
- Actor Type
- Industry
- Motive
- Event Subtype

The pipeline integrates natural language processing (NLP), feature engineering, and machine learning classification to analyze and categorize cybersecurity event data.

## Handling Text Data

The description field (a textual representation of each event) was vectorized using TF-IDF (Term Frequency-Inverse Document Frequency).

Initially, 4,000 features were extracted, later expanded to 6,000 for improved performance. Unigrams and bigrams were used in vectorization to capture both individual words and two-word phrases.

Given the high-dimensional nature of TF-IDF features, Truncated SVD (a variation of PCA for sparse data) was applied. What happens in this step is that the top 4,000 most important words (features) will be kept, based on their frequency and uniqueness across the dataset, and will ignore those that are frequently included like "the".

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/CyberClassifi/IDF-Formula.jpg?raw=true)

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/CyberClassifi/The-TF-Formula.jpg?raw=true)

Key findings:

- 2,500 components preserved 91.95% variance.
- 3,000 components preserved 95.07% variance but took over 15 minutes to run.

I went with 2,500 components as a tradeoff between accuracy and computational efficiency. This was being run on a laptop and waiting the 15 minutes was not something I wanted to do after every change. Initially, multiple train-test splits were performed separately for each target variable. I later learned that doing it this way could introduce data leakage. With the multiple train-test splits they were not consistenlty chosen for training and testing. Which led to weird results that were overfitting. 

A single consistent train-test split was implemented using numpy indexing to ensure that all models were trained and tested on the same subsets of data.

``` py
tfidf = TfidfVectorizer(max_features=6000, ngram_range=(1, 2))
```

The lower and upper boundary of the range of n-values for different word n-grams or char n-grams to be extracted. Which means unigrams and bigrams ( words or tokens of consecutive words) that allow us to see which words commonly co-occur within a given dataset,

### Feature extraction with Dimensionality

Running this at 3000 components runtime > 15 minutes

- Explained variance ratio was 95.07%

Running this at 2500 components runtime > 12 minutes

- Explained variance ratio was 91.95%

``` py
svd = TruncatedSVD(n_components=2500, random_state=42)
X_tfidf_reduced = svd.fit_transform(X_tfidf)
```

This tells you how much of the total variance in the original data is captured by each component after dimensionality reduction.To reduce dimensions, the algorithm tries to preserve as much of the original information as possible.

To explain what all of this data means, an explained variance ratio sum of 0.85, mean that your 1000 components capture 85% of the information that was in the original 6000 features.

## Encoding

One-Hot Encoding for categorical features in the dataset converts categorical variables into a binary matrix where each unique category gets its column.

```py
X_encoded = encoder.fit_transform(df[["event_type","actor_type", "industry","motive","event_subtype"]])
```

## Model Training

At first I was going to solve this using a neural net, however, that 1.3 million parameter model was not as efficient as it could have been. The sklearn library RandomForestClassifier was used here to help me not code an entire random forest calculation. It would have been cool, but I was more interested in getting to the prediction than anything else. I then made index arrays for splitting then convert to CSR format for indexing. Split X data based on these indices using CSR format then I split all y targets using the same indices.

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/CyberClassifi/csr-1561649498.gif?raw=true)

Parameters used:

- 400 trees
- Max depth = 3
- Class-weight = balanced_subsample

Each of the five target variables had a separate Random Forest model trained. The “balanced_subsample” mode is the same as “balanced” except that weights are computed based on the bootstrap sample for every tree grown. <https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier>

## Prediction Function

A function (predict_fields) was built to:

- Process new textual descriptions using the trained TF-IDF vectorizer.
- Generate one-hot encoded features for categorical placeholders.
- Combine all features into the same format used during training.
- Use trained models to predict event type, actor type, industry, motive, and event subtype.

### Challenges and solutions

The model initially expected the full feature set, but predictions were being made using only TF-IDF features. To fix this, placeholder categorical and numeric values were added during the transformation process.

## Model Evaluation

### Model Evaluation Metrics

To assess the model's performance, we analyze the following:

- Accuracy: Measures correct predictions out of total samples.
- Cross-validation results: Ensures robustness.
- Train-test split accuracy: Detects overfitting.

### Accuracy

|Event Type   | 97.8%|
|-------------|------|
|Actor Type   | 94.6%|
|Induestry    | 99.8%|
|Motive       | 97.8%|
|Event Subtype| 99.8%|

### Cross-Validation Results

5-fold cross-validation was used to assess model robustness.
Results were stored in a dictionary for easy retrieval.

These results show the mean accuracy across five folds, with the standard deviation indicating consistency:

Cross-validation results for Actor Type:
Mean Score: 0.9661
Standard Deviation: 0.0118
Individual Scores: [0.98058691 0.95846501 0.97922313 0.95076784 0.96160795]

Cross-validation results for Motive:
Mean Score: 0.9777
Standard Deviation: 0.0047
Individual Scores: [0.98645598 0.97336343 0.97606143 0.97425474 0.97831978]

Cross-validation results for Industry:
Mean Score: 0.9957
Standard Deviation: 0.0024
Individual Scores: [0.99097065 0.99638826 0.99638663 0.99819332 0.99638663]

Cross-validation results for Event Type:
Mean Score: 0.9617
Standard Deviation: 0.0073
Individual Scores: [0.951693   0.95485327 0.96431798 0.97109304 0.96657633]

Cross-validation results for Event Subtype:
Mean Score: 0.9971
Standard Deviation: 0.0008
Individual Scores: [0.99774266 0.99593679 0.99728997 0.99819332 0.99638663]

### Train-test split

Created a function to check for overfitting by comparing train and test accuracy.
Overfitting was not a major issue due to controlled depth (max depth = 3) and other statistical methods.

Here are the train-test accuracy results for each classification task:

- Event Type: Train = 97.28%, Test = 97.83%
- Actor Type: Train = 94.51%, Test = 94.58%
- Industry: Train = 99.84%, Test = 97.72%
- Motive: Train = 97.91%, Test = 97.76%
- Event Subtype: Train = 99.99%, Test = 99.82%

### How accurate is the model?

With all of this information the model has minimal overfitting, with test accuracies generally matching or exceeding training accuracies, which is good. This suggests that the model will generalize well to new data.

### Lessons Learned

Data leakage is a major risk in machine learning pipelines.

- My initial approach led to possible contamination of training data. A single consistent train-test split solved the issue. 

Dimensionality reduction is crucial for performance.

- SVD significantly reduced computational load while preserving >90% of the variance. Without it, the TF-IDF feature matrix would have been too large to handle efficiently.

Class imbalance impacts prediction accuracy.

- Using class_weight='balanced_subsample' in RandomForest helped adjust for imbalances in class distribution and works better than simply balanced given even_subtype.

Combining numeric, categorical, and text-based features required thoughtful encoding and transformation.

- Using scipy sparse matrices made computations feasible.

### Final Thoughts

This project involved text-based classification with dimensionality reduction, data transformation, and machine learning modeling. Despite challenges with data leakage, feature encoding, and model performance, the system effectively categorizes cybersecurity incidents and serves as a foundation for further improvements. However, given that the user could simply hit enter to autocomplete and just enter the other fields, I don't think this tool is a best practice. It has a real potential to be misused and produce bad data quality.

A lot of the work done was informed by prior statistics classes, getting guidance from professors, and reading Geeks for Geeks writeups on functions. I have just started to do real projects like this and it was fun to get it working at the end. I would've liked to create a user interface with a nice text box that shows the current row to be submitted to the file, but I am a graduate student and there are papers to write. After learning a bit more about the libraries that Python has for machine learning, I have a real appreciation for all of the LLM companies and GitHub projects out there. It speaks a lot to their patience and the real pressure to produce something that works better than the last iteration. This was a pretty simple project in comparison, but shed a lot of light on a single process. I didn't share all of my code here as I think more people are interested in the thought process and whether or not the code works. And since it does, I am just showing my results and what I learned. I could be wrong, and if so I will release the code I used later to show the entire process.

So far, for writing to the csv file, it predicts the targeted fields and then opens the csv. From there it gathers user input for the other rows and then writes it to the csv. I am working on an API for this dataset, which would complicate this process more, but I don't think it would be too bad if it's just one insert at a time. It would be slower but than a BULK INSERT query but the actual work behind coding the work could be easier. All of that is yet to be seen as I am in the middle of three papers for school and for some reason they won't write themselves.

![Summary](https://github.com/darkawesome/blog/blob/main/content/img/CyberClassifi/sysmap.jpg?raw=true)
