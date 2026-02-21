# my-AI-idea

Final project for the Building AI course, created by Reaktor Innovations and University of Helsinki.

## Summary

A preventive tool based on logistic regression to estimate the probability of high suicide rates based on socio-demographic, health, and territorial variables.

## Background

Suicide is a persistent public health issue, and this project addresses the need to better understand how suicide rates vary across different socio-demographic and contextual factors. While large amounts of statistical data exist, they are often used mainly for descriptive analysis and do not easily reveal patterns or interactions between variables. By applying logistic regression to integrated population data, the project aims to identify statistical relationships between characteristics such as age, sex, education, health conditions, and territory, helping transform raw data into interpretable predictive insights. 

Beyond epidemiological analysis, the proposed approach could also support the development of preventive decision-support tools in clinical settings. For example, similar predictive models could be used to monitor patients in psychiatric care by classifying them into higher or lower risk categories based on relevant clinical and socio-demographic factors. This type of risk stratification could help clinicians implement targeted monitoring and early preventive interventions. Although such applications would require clinical validation and strict ethical safeguards, they illustrate the potential practical value of predictive modeling for prevention.

My personal motivation is to apply machine learning methods to a real-world and socially relevant dataset, exploring how interpretable models can help analyze complex population health patterns. This topic is important because it connects data science with public health research, allowing quantitative methods to support a deeper understanding of how social and demographic factors relate to health outcomes and how predictive analysis may contribute to both research and prevention.

## How is it used?

The solution is mainly intended for research and analytical environments where large population datasets are studied over time, such as epidemiological monitoring, public health analysis, or statistical reporting. Futhermore, it could support clinical monitoring contexts, for example by helping professionals stratify risk levels and guide preventive attention.

Primary users may include researchers, data analysts, public health professionals, and potentially clinicians using decision-support tools. Their needs include model interpretability, transparency of assumptions, reliable data handling, and clear communication of uncertainty. Ethical use and responsible interpretation of results must also be considered essential requirements, given the delicate topic.

## Data sources and AI methods

In Italy, detailed mortality statistics are systematically collected and published by ISTAT (Italian National Institute of Statistics), reflecting the ongoing relevance of the phenomenon. The official dataset is available here:  
[ISTAT – Suicide statistics dataset](https://esploradati.istat.it/databrowser/#/it/dw/categories/IT1,Z0810HEA,1.0/HEA_DEATH/DCIS_SUICIDI)

## Challenges

The project does not predict individual suicidal behavior and cannot be used for diagnosis or clinical decision-making without extensive medical validation. It identifies statistical associations at a population level and does not establish causal relationships between variables and outcomes.

Key limitations include data quality constraints, missing or unobserved variables, and the simplifying assumptions of the modeling approach. Model outputs depend heavily on how risk categories are defined and on the representativeness of the data.

Important ethical considerations include privacy protection, prevention of stigmatization of demographic or geographic groups, responsible communication of risk estimates, and avoidance of misuse in sensitive decision-making contexts. Any clinical or operational deployment would require strict ethical oversight, transparency, and validation by domain experts.

## What next?

The project could evolve into a more comprehensive predictive and analytical platform by incorporating additional data sources, more advanced machine learning techniques, and interactive visualization tools for a more user-friendly use. With appropriate validation, it could also support the development of structured decision-support systems for prevention and monitoring.

Further development would require interdisciplinary collaboration, including expertise in clinical psychiatry, epidemiology, regulatory, and ethics, as well as advanced data science and software engineering skills.

# Code example

import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import ColumnTransformer
from sklearn.pipeline import Pipeline

def main(): 

  # 1. DATASET
  
  data = {
      "sex": [
          "M","M","M","M","F","F","F","F",
          "M","M","M","M","F","F","F","F",
          "M","M","M","M","F","F","F","F"
      ],
      "age_group": [
          "15-34","15-34","15-34","15-34",
          "15-34","15-34","15-34","15-34",
          "35-64","35-64","35-64","35-64",
          "35-64","35-64","35-64","35-64",
          "65+","65+","65+","65+",
          "65+","65+","65+","65+"
      ],
      "physical_disease": [
          0,1,1,0,0,1,1,0,
          0,1,1,0,0,1,1,0,
          0,1,1,0,0,1,1,0
      ],
      "mental_disease": [
          1,0,1,0,1,0,1,0,
          1,0,1,0,1,0,1,0,
          1,0,1,0,1,0,1,0
      ],
      "suicides": [
          50, 8, 3, 381, 14, 2, 1, 93,
          243, 30, 28, 1201, 115, 11, 21, 278,
          129, 129, 70, 814, 81, 13, 22, 197
      ]
  }
  df = pd.DataFrame(data)
  
  # 2. TARGET CREATION
  
  threshold = df["suicides"].median()
  df["high_rate"] = (df["suicides"] > threshold).astype(int)
  
  X = df.drop(columns=["sex","age_group","physical_disease","mental_disease"])
  y = df["high_rate"]
  
  # 3. PREPROCESSING
  categorical_features = ["sex","age_group"]
  numeric_features = ["physical_disease","mental_disease"]
  
  preprocessor = ColumnTransformer(
      transformers=[
          ("cat", OneHotEncoder(), categorical_features),
          ("num", "passthrough", numeric_features)
      ]
  )
  
  # 4. MODEL
  
  model = Pipeline([
      ("prep", preprocessor),
      ("clf", LogisticRegression())
  ])
  
  # 5. TRAINING
  X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
  model.fit(X_train, y_train)
  
  # 6. NEW CASE PREDICTION
  new_case = pd.DataFrame({
      "sex": ["M"],
      "age_group": ["65+"],
      "physical_disease": [1],
      "mental_disease": [1]
  })
  
  prob = model.predict_proba(new_case)[0][1]
  prediction = model.predict(new_case)[0]
  
  print("Probability high risk:", prob)
  print("Predicted class:", prediction)

main()

## Acknowledgments
