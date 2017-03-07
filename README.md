# Fraud Detection in Telecommunications: History and Lessons Learned
#### Becker, Volinsky, Wilks (AT&T Labs-Research)


This paper reviews the history of fraud detection at AT&T discussing some of the major fraud schemes and the techniques used to address them. Authors specifically advocate the use of simple, unserstandable models, heavy use of visualization, and emphasize the importance of data management and the need to keep humans in the loop.


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

TBA:
##### Early Threshold-Based Alerting
##### Signature-Based Alerting
##### Moving to Graph-Based Signatures
##### Catching Fraud with Graph Matching



### The Role of Humans

Over the years, authors have witnessed several attempts to build a system with intelligent "agents" that can learn the actions the human would take in a given situation and apply them automatically thereafter. However, correctly diagnosing fraud is difficult without a person involved - the same calling pattern could be fraud for one customer and normal usage for another. Humans are sensitive and creative in assessing calling behavior. In the authors' experience, this intuition is extremely difficult to codify. 

Another reason to have people in the loop is that it means that the fraud detection system does not have to be perfect (and thus difficult and complex to produce). Instead, algorithms point to potential crime scenes, and the person is an investigator looking for additional clues.


### Visualization

A visualization tool enables a fraud analyst to view and understand perhaps thousands of calls at once. The goal is to display all the recent call detail, to allow the analyst to see the potentially fraudulent calls in the context of the normal calling pattern. For this, AT&T provides a tool based on the Yoix language, which itself is built atop Java. The tool provides a plot with a time axis and interactive histograms of various call characteristics that allow the analyst to display interesting subsets of the data.

![Screenshot](https://github.com/rebarbara/fraud/blob/master/at_t_visualization.png)

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

