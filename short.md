# Fraud Detection in Telecommunications: History and Lessons Learned
Becker, Volinsky, Wills - AT&T Labs-Research - [Original paper](https://github.com/slaki/labor2017/blob/master/wiki/fraud-telecom-overview/fraud-detection-in-telecom.pdf) (2010)


## Summary

Key components of a fraud detection system:

- **A continuing source of call detail data.** AT&T receives hundreds of thousands of files of AMA data daily from telecommunication switches around the country. Besides AMA (Automated Message Accounting), which is a comprehensive data format used for billing, other formats such as SS7 signalling data, cellular data or LIDB (Line Information Database) can be used.
- **Database to store the data.** AT&T developed their own database system [Daytona](http://www.research.att.com/projects/Daytona/index.html?fbid=R8dDIUGTEIZ) to be able to tie data management and analysis together. Analysts can access full raw data records and take high-level summeries at the same time.
- **A set of detection algorithms.** See below.
- **People to verify fraud and implement corrective measures.** In the authors' experience, fraud investigation
requires a sophisticated sequence of deduction, analysis,
integration between organizations, social interaction, and
decision making that can be done only by people. In their view, the purpose of a fraud detection
system is to point the experts toward cases that might
be fraudulent.
- **Visualization tools to help people make diagnoses.**


## Detection algorithms

#### Signature-Based Alerting

- **Goal:** Alert for numbers that exhibit suspicious behavior.

- **Method:** Maintain a signature for each customer based on comprehensive
call records. Compare new calls to the signature and to a generic fraud
pattern. Increase fraud score or update signature. Signal if fraud score
passes threshold.

<details>
  <summary>Details</summary>
  <br>
A signature vector can be kept on record for each customer. The signature
is a statistical summary of the customer's previous behavior containing data
such as calling rate, distribution of calls by day of week, hour of day,
distribution of call duration, regions of the world called, most frequent
countries and phone numbers called, etc. Such information can be stored in
about 500 bytes per customer.

A generic fraud signature is defined. An incoming call is compared both to 
the current signature of the customer
and the generic fraud signature. If it resembles normal usage pattern more,
then its characteristics are used to update the signature. If it
resembles fraudulent usage pattern more, the customer's fraud score is
increased.

Signature-based alerting tends to produce good results at finding changes in
calling patterns that indicate fraud. AT&T uses many simple detection
algorithms rather than one single complex model. Various algorithms may
generate an alert on the same number, and these alerts are combined into a
single case to be investigated.
</details>


#### Graph-Based Signatures 

- **Goal:** Alert if a new number's social network contains known fraudsters.

- **Method:** Community of interest (COI) signature is maintained for each customer
containing top called numbers and top numbers that called them. Based on the
presence of known fraudsters in a new number's social network, a probability
of fraud is calculated and sent to investigators.

<details>
  <summary>Details</summary>
  <br>
Social networks of fraudsters are another source of information that can help
identify fraud cases. The callgraph network is a conceptualization of the 
call detail data as a graph, where nodes are phone numbers and directed edges 
represent communication between them. To extract the social network of each 
customer easily, a community of interest (COI) signature is used.
This includes the top numbers called by the target number and the top numbers 
that call that number. Thus each phone number has its small graph and the
union of these makes up the callgraph network.

COI signatures are updated by a variant of exponential smoothing combining
old top-k edges with the current day's edges. Based on COI signatures a "guilt
by association" application can be developed. For new numbers with suspicious
calling behavior, an extended social network can be built and visualized as a
graph. By coloring the nodes of known fraudulent numbers, probability of fraud
can be calculated for all new numbers and a ranked list can be sent to the 
fraud investigators.
</details>



#### Graph Matching

- **Goal:** Find "repetitive debtors".

- **Method:** Match COI signatures of new numbers to the signatures in the
repetitive debtors database (RDD).

<details>
  <summary>Details</summary>
  <br>
COI signatures has also been useful in tracking down fraudsters who try to 
cover their tracks by changing their phone number, name or address. The 
RDD is designed to keep a running database of COI signatures for this purpose. 
To find such fraudsters, COI signatures on all new accounts 
are built and matched to the signatures in the RDD.

The distance function is based on the overlap between the two graphs and the 
proportion of the overall communication accounted for by those overlapping 
nodes. Two variants are used, both can be efficiently calculated for large
numbers of candidate pairs, thus tens of thousands of new accounts can be tested
daily. The generated case list of suspected "repetitive debtors" can be handed
off for further investigation.
</details>


