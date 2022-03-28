This repository contains implementation of the code to identify anomalous session activities based on the click-stream data collected for sessions.

# Project Details
| | |
| --- | --- |
| Title:| Detecting Anomalous Session Activity From Click-stream Data. |
| Type: | Proof of Concept|
| Date: | 1st November 2017
| Span: | One week |
| Language: | R |
| | |

# Quicklinks
- 1. [Introduction](#1-introduction)
- 2. [Data Preparation](#2-data-preparation)
- 3. [Defining "Anomaly"](#3-defining-"anomaly")
- 4. [Exploratory Data Analysis](#4-exploratory-data-analysis)
- 5. [Markov Model](#5-markov-model)
- 6. [Detecting Anomaly](#6-detecting-anomaly)
- 7. [Improvements](#7-improvements)
- 8. [References](#8-references)


# 1. Introduction
Purpose of this study is to identify anomalous session activities based on the click-stream collected for sessions.
The dataset set used for conducting this study consist of 23937 records containing activities of 11 different sessions with primarily six attributes, namely **height, width, time, type, scrollX** and **scrollY**. Where, **height** and **width** are height and width of window respectively, **time** is the time lapsed since start of session, **type** represents type of DOM events (such as mouse events, keyboard events etc) containing 15 categorical factors (**click, dblclick, focusin, focusout, mousedown, mousemove, mouseout, mouseover, mouseup, scroll, unload, keydown, blur, focus, resize**). There are certain assumptions which are implied for simplifying the task, that are a) All the session activities are recorded for each individual user on a single web page. b) There's only single IP address for each session. All simulation discussed in this document are programmed using R Language.

# 2. Data Preparation
In addition to six attributes, two new features **sessionid** and **timegap** are added to the dataset that shows unique number to identify session and time gap between to consecutive event respectively. For two consecutive events $e_i(t_i)$ and $e_j(t_{i+1})$ occurring at time stamps $t_i$ and $t_j$ respectively, the time gap $g_{ij} = \mid{t_i - t_j}\mid$ shows time lapsed between events $e_i$ and $e_j$. For the first record of each session, $t_{ij}$ is `NULL`. Certain **time** values were not sorted in dataset. Thus the entire data set is ordered by **time** attribute. 

In order to create Markov chain model, dataset containing state sequence information is created from attribute **type**. State sequence is a list of consecutive events (e.g. "mouseover-mouseout-mousemove-scroll"). For brevity, only first 50 event **types** from each session is considered Fig. (<a href="#seqdata">1</a>). The dataset contains 11 records and 50 attributes. Where, each record represents a session $s_j$ and each attribute represents event $e_i(t_i)$ at time $t_i$ for $i = 1,\ldots,50$ and $j = 1,\ldots,11$.

<div align="center">
<a name="seqdata"></a>

| ![seqdata](plots/seqdata.png) | 
|:--:| 
| **Figure 1: State sequence data for *type* attribute** |

</div>

# 3. Defining "Anomaly"
First process while beginning anomaly detection is defining what is normal/typical and what is anomalous/atypical. 
In context of session click-stream data, measures like session length, average time spent by user on a page, number of clicks per session, time gap between consecutive events etc can be used to find outlier sessions. Possible anomalous session are listed below:

1. Sessions that has gaps longer than 30 minutes.
2. Sessions that are longer than a day and lasts for days (suggests the user is bot).
3. Large number of click events in very short time (suggests DOS attack, click fraud etc.).

If there's information about the **pages** visited during a session, **IP addresses** accessing the page and **Date-time**, confidence in the session classification can be improved by considering following anomalous characteristics:

1. Large number of pages visited in very short period of time (suggests web scrapping).
2. Multiple IP addresses for a single session.
3. Multiple short sessions from a single IP Address.
4. Large number of sessions opened per second (suggests attack).
5. Sudden increase in volume of activity in a session (suggests the user is bot).

# 4. Exploratory Data Analysis
Fig. (<a href="#bp_time_session">2</a>) shows box-plot of feature **time** for each **sessionid**. It helps in visualizing how the time stamps are distributed or spaced. We can observe certain points that are classified as outliers in session $s_6$, in current context these are not outliers but represents the notion of unequal gap between time stamps in session $s_6$. It means that, there are certain **timegap** values in session $s_6$ that are larger than other **timegap** values in $s_6$.

<div align="center">
<a name="bp_time_session"></a>

| ![bp_time_session](plots/bp_time_session.png) | 
|:--:| 
| **Figure 2: Box plot for time** |

</div>

Cumulative distribution function (CDF) plot in Fig. (<a href="#cdfs">3a</a>) shows how many sessions in the data takes up a certain session length value. Session length is a value depicting how long a session lasted. CDF helps in answering questions like what percent of sessions last longer than 1 minute? or How many sessions have session length between 50 seconds and 1 minute? We see that almost 50% of data has session length more than a minute As there are only 11 sessions, the CDF is not smooth. CDF can be useful in detecting sessions that are too long, which can further be checked for being anomalous.
CDF of **timegap** in Fig. (<a href="#cdfs">3b</a>) shows that almost all the time gaps in the dataset for each session are below or around 1 minute. This CDF can be useful in detecting sessions that have time gaps of more than 30 minutes.

<div align="center">
<a name="cdfs"></a>

Figure 3a: Session length per user | Figure 3b: Time gap between events
:-------------------------:|:-------------------------:
![Variation in Price](plots/CdfSessionLength.png)  |  ![Variation in Extra_people Price](plots/CdfTimeGap.png)
<p align="center"><b>Figure 3: CDF</b></p>
</div>

State sequence data for **type** attribute (Fig. (<a href="#seqdata">1</a>)) can be visualized using Sequence Index Plot that shows states over successive timestamps (Fig. (<a href="#sequenceplots">4a</a>)) which helps in easily visualizing patterns in the sequence of events. For brevity, only first 50 events from each of 11 sessions are stacked. We can observe some common behaviour such as: the most occurring event is "mouseover", "mouseout" is followed by "mousemove", like "mouseover"; "scroll" is also continuous for longer sequence etc.

We can easily visualize similarity between sequences from Index plot. Fig. (<a href="#sequenceplots">4b</a>) shows the most frequently occurring patterns in a sequence e.g. patterns containing majority "scroll" events are most occurring while patterns similar to seq. 1 occurs less frequently. 

Similarly Fig. (<a href="#sequenceplots">4c</a>) shows the distribution of events in a sequence. "mouseover" event holds major part of the distribution in all the sequences.
In state sequence analysis, only sequence of events is of importance, irrespective of the time at which the event has occurred. E.g. in Fig. (<a href="#sequenceplots">4a</a>)  seq. 1 and seq. 2 both have "mousemove" event at some time stamp $t_1$. It is not necessary that this time stamp is same for both the sequences, but what matters is the sequence of events in both seq. 1 and seq. 2 is similar till time stamp $t_6$ that shows there's some common behaviour between these two sequences.  

<div align="center">
<a name="sequenceplots"></a>

|||
:-----------------:|:-----------------:
**Figure 4a: Sequence Index Plot** | **Figure 4b: State Frequency Plot**
[<img src="plots/SeqIndexPlot.png" alt= "Sequence Index Plot" width="400"/>](plots/SeqIndexPlot.png)  | [<img src="plots/SeqFreqPlot.png" alt= "State Frequency Plot" width="400"/>](plots/SeqFreqPlot.png)
**Figure 4c: State Distribution Plot** | 
[<img src="plots/SeqDistPlot.png" alt= "State Distribution Plot" width="400"/>](plots/SeqDistPlot.png)  | [<img src="plots/SeqLegend.png" alt= "Sequence Color Code" width="400"/>](/plots/SeqLegend.png)

</div>

# 5. Markov Model
The click-stream can be considered as categorical temporal data, where the state of a web page keeps changing over a time due to transition events. Markov model is a stochastic process that shows how a random variable changes over a time at discrete time stamps.
User behaviour can be modeled as a sequence of events (similar to random variable). In order to detect anomaly, the usual behaviour of the session activities is modeled using Markov chain which then acts as a base for comparison. It is very useful in finding position outliers in a sequence. To model the state sequence data of Fig. (<a href="#seqdata">1</a>), first order Markov model is used because of certain assumptions as below:

1. It is assumed for now that occurrence of an event at time $t_i$ is only dependent on event that has occurred at time $t_{i-1}$ and not on the previous sequence of events thus we need only order one Markov model. 
2. As all the possible event types are already known, there is no need of using Hidden Markov Model.
3. As for now, only occurrence of event triggered by previous event is considered and not the time gap between the two.

<div align="center">
<a name="markovchain"></a>

| ![markovchain](plots/markovchain.png) | 
|:--:| 
| **Figure 5: Markov Chain Model** |

</div>

Markov chain for a sequence in data Fig. (<a href="#seqdata">1</a>) is shown in Fig. (<a href="#markovchain">5</a>) In order to build model we need to compute transition probabilities. Transition probability from event $e_i$ to event $e_j$ is given by

<a name="transprob"></a>
$$
\begin{align}
	\tag{1} P(e_j|e_i) & = \frac{N_{ij}}{N_{ik}} 
\end{align}
$$
Where $N_{ij}$ = Number of times $e_i$ is followed by $e_j$ and $N_{ik}$ = Number of times $e_i$ is followed by all events including self transition. All these probabilities can be easily compared from given data.

# 6. Detecting Anomaly
Once the normal user behaviour is captured, the test observation sequence is compared against the model. It is to be tested if the new test sequence can be modeled by trained Markov model. This is decided on the basis of "Anomaly Score", higher the score of a test sequence rarer the sequence is i.e. the sequence may be an anomaly. There are several techniques for choosing anomaly score:

1. **Score 1**\
***Sadagopan and Li*** [[1]](#1) present the use of log likelihood as an anomaly score, they call it Markovian LoglikeHood ($MLH_{avg}$). For a test sequence they compute its log likelihood $\ln(\phi)$ from start to end state, then they take the log of this value and divide it by number of transitions. For example: in our case take test sequence "mouseout-mouseover-mousemove-scroll" denoted as "MT-MR-ME-SL" and compute its anomaly score $MLH_{avg}$. Assume that for our case start state is "mouseover (MR)" so S = MR. Transition probability is computed as per Eq. [[1]](#transprob).

$$
\begin{align*}
ln(\phi) &= \ln\Big(P(MT|S)\times P(MR|MT)\times \\
& P(ME|MR)\times P(SL|ME)\Big)\\
ln(\phi) & = \ln(0.5 \times 0.14 \times 0.11 \times 0.04)\\
ln(\phi) & = \ln(0.000308)\\
& \text{$MLH_{avg}$ is computed using $ln(\phi)$ as}\\
MLH_{avg} & = \frac{ln(\phi)}{4}\\
& = \frac{ln(0.000308)}{4} = -2.02
\end{align*}
$$

Deciding what $MLH_{avg}$ value defines normal behaviour is completely based on the dataset. Threshold is decided by computing $MLH_{avg}$ for every normal session and defining a non-anomalous session range. ***Sadagopan and Li*** [[1]](#1) also discuss shortcomings of this measure in [[1]](#1) and propose other measure in association with $MLH_{avg}$, that considers taking $`mean`$ of each event.

2. **Score 2**\
***Ye*** [[2]](#2) also use likelihood as anomaly score but there is a slight change. There is no start state in this computation instead there is probability of observing first event of test sequence in training data (in our case its $P(MT)$). Anomaly score is inverse of likelihood $P(e_1,\ldots,e_T)$, means higher the probability, lower is the possibility of sequence being anomaly.

$$
\begin{align*}
P(e_1,\ldots,e_T) & =  q_{e_1} \prod_{t=2}^{T}P(e_t|e_{t-1})\\
P(MT,\ldots,SL) & = q_{MT} \times P(MT|S)\\
& \times P(MR|MT)\times \\
& P(ME|MR)\times P(SL|ME)
\end{align*}
$$

Where, 
$$
q_{MT} = \frac{\text{Number of time MT precedes an event}}{\text{Total number of obs. in training data}}
$$
and $T$ = number of events in test sequence

# 7. Improvements
- Model can be improved by considering information about gaps between events. Markov model we discussed considers events as function of time. ***Wang et al.*** [[3]](#3) present a new approach for representing events as function of gaps, They classify gaps in five discrete time buckets, and each event is assigned a time bucket based on the lag between the event and its predecessor. Later they combine these sequences based on similarity graph into clusters.

- In case of Markov model when the sequence is too long the complexity increases potentially. Probability Suffix Trees [[4]](#4) can be used to improve efficiency of computing conditional probabilities (transition probabilities) when there are long sequences.

- In this study it is assumed that occurrence of an event depends only on its predecessor event But, in case of web user behaviour this is not true always, e.g. in case of web search log the event of user clicking a page may depend on sequence of previous searches. Thus, information related to pages should be considered for developing $`k`$-order Markov model.

# 8. References
<a name="1">[1]</a> N. Sadagopan and J. Li, "Characterizing typical and atypical user sessions in clickstreams," in Proceedings of the 17th inter-national conference on World Wide Web, pp. 885–894, ACM, 2008.

<a name="2">[2]</a> N. Ye, "A markov chain model of temporal behavior for anomaly detection," in Proceedings of the 2000 IEEE Systems, Man, and Cybernetics Information Assurance and Security Workshop, vol. 166, p. 169, West Point, NY, 2000.

<a name="3">[3]</a> G. Wang, X. Zhang, S. Tang, H. Zheng, and B. Y. Zhao, "Unsupervised clickstream clustering for user behavior analysis," in Proceedings of the 2016 CHI Conference on Human Factors in Computing Systems, pp. 225–236, ACM, 2016.

<a name="4">[4]</a> C. C. Aggarwal, "Outlier detection in discrete sequences," in Outlier Analysis, ch. 9, pp. 267–311, Springer Science & Business
Media, 2013.