# Fraud Detection in Telecommunications: History and Lessons Learned
#### Richard A. BECKER, Chris VOLINSKY, and Allan R. WILKS
AT&T Labs-Research, 2010 - [Original paper](https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/fraud-detection-in-telecom.pdf)


This paper reviews the history of fraud detection at AT&T discussing some of the major fraud schemes and the techniques used to address them. Authors specifically advocate the use of simple, understandable models, heavy use of visualization, and emphasize the importance of data management and the need to keep humans in the loop.


## The Nature of Fraud

The most difficult aspect of fighting fraud is identifying it. AT&T's goal was to create a fraud management system that was powerful enough to handle the many different types of fraud that they encountered and flexible enough to potentially apply to things they had not seen yet. Examples of some common varieties of fraud in the telecommunications world:

- **Subscription fraud.** Someone signs up for a service with no intent to pay.
- **Intrusion fraud.** An existing, otherwise legitimate account is compromised by an intruder, wo subsequently makes or sells calls on this account.
- **Fraud based on loopholes in technology.** As an example, voice mail can be configured in such a way that calls can be made out of the voice mail system. The fraudster compromises a mailbox (e.g. by guessing a weak password) and then uses the system to make outgoing calls.
- **Social engineering** exploits human interaction with the system. The fraudster pretends to be someone he/she is not, such as the account holder or a phone repair person to access a customer's account.
- **Fraud based on new technology.** New technology, such as VoIP enables international telephony at very low cost. Fraudsters realized they could purchase the service at a low proce and then resell it illegally at a higher price to consumers unaware of the new service, unable to get it themselves, or technologically unsophisticated. 
- **Fraud based on new regulation.** In 1996, FCC (Federal Comminications Commission) modified payphone compensation rules, requiring payphone operators to be compensated by the telecommunications providers. This spawned a new type of fraud - payphone owners placing spurious calls from payphones to toll-free numbers simply to bring in compensation income from the carriers.
- **Masquerading as another user.** Credit card numbers can by stolen by various means and used to place calls masquerading as the card-holder.

There are many more fraud techniques, some of which are quite sophisticated and combine more than one known method. New types appear regularly, and these schemes evolve and adapt to attempts to stop them.


## Detecting Fraud

In the authors' experience for massive data streams, there is no way to effectively separate data analysis from the larger issues of data delivery, data management, and data storage. In the next subsections, they describe the fraud detection framework developed at AT&T over the years and how data analysis fits into the broader scheme.

Key components of a fraud detection system:
- a continuing source of call detail data,
- a database to store the data,
- a set of detection algorithms,
- people to verify fraud and implement corrective measures,
- visualization tools to help people make diagnoses.


### Data Sources

Analyzed data comprises records of phone calls. Common data formats:

- *Automated message accounting (AMA) format:* comprehensive, used to create phone bills. Nowadays transmitted electronically and often can be sent in real time. Drawback: details for a call are only available after the call is complete.
- *Data from celullar calls:* no standardized form. Equipment vendors for cellular switches have their own complex formats.
- *SS7 signalling data:* used to set up calls, independent of the voice network, similar in volume to AMA, can be collected real-time. Additional advantage: can be sampled at call setup, avoiding the delay until call completion.
- *Line Information Database (LIDB):* gives the status of other calls, such as bill-to-third party, and calling card calls.

Along with all these data, a continuous stream of metadata must also be dealt with. This large data stream brings greatly increased complexity. The tight integration of data analysis and data management is a direct result of the volume and complexity of the data sources.

### Storage

AT&T required a database that
- could be updated in real time,
- could hold vast amounts of historical data in minimal disk space,
- had the ability to retrieve thousands of call records quickly.

Chosen DBMS: Daytona. Characteristics and strategy:
- Use both field-level and record-level compression. Besides sparing disk space this also speeds up retrieval time for queries: fewer bytes accessed from disk. Typical query for 2000 international calls < a minute.
- Store all of the call detail for several years to provide a lengthy historical reference.
- As the call detail arrives, each record is augmented with supporting information (e.g. estimated retail cost, type of service, business/residence). Consistency checks.

Having a database allows for retrospective analyses and provides the means of testing new algorithms on historical data. Also provides historical view of individual calling patterns, so the deviations from normal can be seen.


### Detection

The system must automatically filter the data looking for unusual usage patterns, and then algorithms or people can look at the unusual parts to investigate the potential fraud.

#### 1. Early Threshold-Based Alerting

Early fraud detection algorithms often used thresholds, delivering alerts when the number of messages and the number of minutes of calling within a specific time period exceeded a preset threshold. Drawback: fraud continues until the threshold without an alert. This method also treats customers identically - a large business is likely to trip the threshold routinely. After a period of frustrating fine-graining of independent sets of threshold variables, AT&T devised a customer-specific signature based alerting system, of which a brief description follows.

#### 2. Signature-Based Alerting

Basic idea: as each call comes in, it is compared to the current signature for that customer and also to a generic fraud signature. Two possible scenarios based on which pattern the call resembles more:
- *customer usage pattern:* characteristics of the call are used to update the signature according to an adaptive exponentially weighted moving average (EWMA),
- *fraudulent usage pattern:* fraud score is increased for the phone number. Once it passes a threshold, an alert is generated.

Some measures signatures are based on:
- calling rate,
- distribution of calls by day of week, hour of day,
- distribution of call duration,
- regions of the world called,
- most frequent countries, phone numbers called, etc.

All of this information is stored in about 500 bytes per customer and contains simple statistical summaries of that customer. Each parameter in the signature **X** is updated by a version of an EWMA,

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/ewma.png" /></p>

where <img src="/fraud-telecom-overview/pretty-math/X_p.png" /> and <img src="/fraud-telecom-overview/pretty-math/X_n.png" /> are previous and new values of the parameter and <img src="/fraud-telecom-overview/pretty-math/D_c.png" /> is the information that comes from the current call. Two important parameters must be set: <img src="/fraud-telecom-overview/pretty-math/theta.png" /> and the updating interval. These together determine how quickly new information washes out old information. Possible strategies of setting the parameters:
- Multiply <img src="/fraud-telecom-overview/pretty-math/theta.png" /> by <img src="/fraud-telecom-overview/pretty-math/log.png" />, where r is the calling rate to ensure old information is not washed out too quickly.
- Optimize <img src="/fraud-telecom-overview/pretty-math/theta.png" /> using machine learning methods. Look at the signature as a predictive function - the best value of the parameter is the ones that best predicts future behavior. 

**Signature initialization:** When there is no prior signature, data values from the first two calls are used to select an initial signature from a set of empirically built signatures. Old signatures that are not reinforced by new calls within a month or so are dropped completely.

As mentioned earlier, the overall fraud score for a phone number may increase call by call. To complement this growth, fraud scores are **decreased every day**. In this way, fraud score is an indicator of *recent* fraud activity.

AT&T uses **many** detection algorithms, each of which tends to be **simple**, rather than a single complex algorithm. Various detection algorithms may generate an alert on the same number, and these alerts are combined into a single case to be investigated. The case manager is a component of the system and can decide which cases should be investigated first.


#### 3. Moving to Graph-Based Signatures

**Social networks** of fraudsters are another source of information that can help identify fraud cases. The *callgraph network* (denoted G_t at time t) is a conceptualization of the call detail data as a graph, where nodes are phone numbers and directed edges represent communication between those numbers. In order to be able to extract the social network easily for any one of the hundreds of millions of members, AT&T developed a framework known as the *community of interest (COI) signature* for each telephone number. This includes the top numbers (top-*k*) called by the target number and the top numbers that call that number. Thus each phone number has its small graph and the union of these graphs make up G_t.

The signature needs to deal with the fact that phone numbers are transient - a large turnover of the phone number space is possible over the course of several months. Graph signatures are updated by a variant of the exponential smoothing:

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/ewma_graph.png" /></p>

where the circle operator is a graph sum operation, G_{t-1}^ denotes the top-*k* approximation of G_{t-1} at time t-1, and g_t denotes the graph derived from the new transactions at step t. The following figure might help to understand the concept easily:

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/topk.png" /></p>

Based on these, a **"guilt by association"** module was established to lead the company to new fraud cases. For example, a new suspicious number appears (labeled suspect on the figure below). After it has established its COI signature through calling behavior, an extended social network is built and visualized in a graph. By coloring the nodes of known fraudulent numbers, a probability of fraud gets calculated for all new numbers, and a ranked list is sent to the fraud investigators.

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/guilt_by_assoc.png" /></p>


#### 4. Catching Fraud with Graph Matching

COI signatures has also been useful in tracking down miscreants who try to cover their tracks by changing their phone number, name or address. The *repetitive debtors database (RDD)* is designed to keep a running database of COI signatures of delinquent customers for this purpose. Intuition: build COI signatures on all new accounts and match them to the signatures in the RDD to find these fraudsters. 

The distance function is based on the overlap between the two graphs and the proportion of the overall communication accounted for by those overlapping nodes. Two metrics used:
- Dice criterion for two sets *A* and *B*:

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/dice.png" /></p>

A weighted version of this criterion is used to account for the weights on the edges.

- Hellinger distance applied to graph matching:

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/hellinger.png" /></p>

where *w* represents the weights on the edges in the overlapping part of the graphs.

Both of these metrics can be efficiently calculated for large numbers of candidate pairs. In AT&T's application, they tested tens of thousands of new accounts daily. The generated daily case list of suspected "repetitive debtors" would get handed off to the fraud team for further investigation.

### The Role of Humans

Over the years, authors have witnessed several attempts to build a system with intelligent "agents" that can learn the actions the human would take in a given situation and apply them automatically thereafter. However, correctly diagnosing fraud is difficult without a person involved - the same calling pattern could be fraud for one customer and normal usage for another. Humans are sensitive and creative in assessing calling behavior. In the authors' experience, this intuition is extremely difficult to codify. 

Another reason to have people in the loop is that it means that the fraud detection system does not have to be perfect (and thus difficult and complex to produce). Instead, algorithms point to potential crime scenes, and the person is an investigator looking for additional clues.


### Visualization

A visualization tool enables a fraud analyst to view and understand perhaps thousands of calls at once. The goal is to display all the recent call detail, to allow the analyst to see the potentially fraudulent calls in the context of the normal calling pattern. For this, AT&T provides a tool based on the Yoix language, which itself is built atop Java. The tool provides a plot with a time axis and interactive histograms of various call characteristics that allow the analyst to display interesting subsets of the data.

<p align="center"><img src="https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/at_t_visualization.png" /></p>

The above figure shows a fraud event, in which the normal calling pattern is overlaid by many calls to a foreign country historically associated with fraud. Detecting these calls is easy, because many of them is of the same duration.

This screenshot is static and monochromatic, but real displays are fully interactive and use color to encode categorical variables. The analyst may, for example, pop up a histogram that shows the number of calls going to each country, etc.


## Implementation

TBA

## Conclusion

Some common themes found in cases dealing with extremely large datasets over the years at AT&T:

- The need to join data analysis and data management.
- Inadequacy of large models.
- Necessity of humans.
- Need for fast feedback loops.
- Importance of flexibility.
