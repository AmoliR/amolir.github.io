# Portfolio

<!-- ## Natural Language Processing -->
<!-- --- -->

* TOC
{:toc}

---
## Data Science Projects

### Payment Default Prediction

[![Open Web App](https://img.shields.io/badge/AWS-Open_Web_App-blue?logo=amazonaws)](http://ec2-3-15-39-200.us-east-2.compute.amazonaws.com:8080/)
[![Open Notebook](https://img.shields.io/badge/Jupyter-Open_Notebook-blue?logo=Jupyter)](projects/default_prediction/default_prediction.html)
[![View on GitLab](https://img.shields.io/badge/GitLab-View_on_GitLab-blue?logo=gitlab)](https://gitlab.com/amoli13/payment-default-prediction)

<div style="text-align: justify"> Using the dataset containing a series of account statements having some pre-processed features representing a time frame, I developed a logistic regression modle to predict the probability of each customer (account holder) defaulting their next payment. The final working model is built into an AWS web application that accepts the account data and display statement Id and prediction of default for it.</div>
<br>
<center><img src="images/payment-default-app-architecture.png"/></center>
<br>

---
### Click-Stream Data Anomaly Detection

[![Read Blog](https://img.shields.io/badge/Markdown-Read_Blog-blue?logo=markdown)](https://gitlab.com/amoli13/anomaly-detection/-/blob/main/README.md)
[![View on GitLab](https://img.shields.io/badge/GitLab-View_on_GitLab-blue?logo=gitlab)](https://gitlab.com/amoli13/anomaly-detection)

<div style="text-align: justify"> In this project I identify anomalous session activities based on the click-stream collected for sessions. All the session activities are recorded for each individual user (IP Address) on a single web page. Making use of a stochastic process called Markov model, implemented using R, I build a intuition about how a categorical temporal data such as click-stream changes over a time at discrete time stamps.
</div>
<br>
<center><img src="images/clickstream-sequence.png"/></center>
<br>

---
### Predictive Modeling For Real Estate Properties.

[![Read Blog](https://img.shields.io/badge/Markdown-Read_Blog-blue?logo=markdown)](https://github.com/AmoliR/amolir.github.io/blob/main/projects/rental-price-prediction/rental-price-prediction.md#user-content-fnref-1-451e5940de8985c1098b5a20946b9acd)
[![View on GitLab](https://img.shields.io/badge/GitLab-View_on_GitLab-blue?logo=gitlab)](https://gitlab.com/amoli13/predictive-analytics)

<div style="text-align: justify"> Implementation and comparative analysis of various predictive models (such as Multiple Linear Regression,
Support Vector Machine, Regression Trees and Random Forest) to predict features like property rent price and cost of hosting extra people using real estate data.
</div>
<br>
<center><img src="images/rental_price_variation.png"/></center>
<br>

---
### Risk Factor Prediction For Cardiac Disease
<div style="text-align: justify"> Using Fuzzy C-Means clustering algorithm on lipid profile data of patients to predict their likelihood of getting cardiac disease. Statistical analysis was carried out using R.
</div>
<br>
<center><img src="images/cardiac-disease-risk-factor-prediction.png"/></center>
<br>

---
## Publication
[![Read Paper](https://img.shields.io/badge/arXiv-Read_Paper-blue?logo=arxiv)](https://arxiv.org/abs/1707.08369)
[![View on GitHub](https://img.shields.io/badge/GitHub-View_on_GitHub-blue?logo=github)](https://github.com/AmoliR/rank1-svd-update)

<div style="text-align: justify"> Implemented algorithm for updating Singular Value Decomposition (SVD) for rank-1 perturbed matrix using Fast Multipole Method (FMM) in <img src="https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cbg_white%20O%28n%5E2%20%5C%20%5Ctext%7Blog%7D%28%5Cfrac%7B1%7D%7B%5Cepsilon%7D%29%29" alt="Equation" /> time, where <img src="https://latex.codecogs.com/gif.latex?%5Cinline%20%5Cbg_white%20%5Cepsilon" alt="Equation" /> is the precision of computation.</div>
<br>
<center><img src="images/fmm-svdu-reduction-in-error.png"/></center>
<br>

<center>© 2022 Amoli Rajgor. Powered by Jekyll and the Minimal Theme.</center>

