# Fraud Detection in Telecommunications: History and Lessons Learned
#### Becker, Volinsky, Wilks (AT&T Labs-Research)


This paper reviews the history of fraud detection at AT&T discussing some of the major fraud schemes and the techniques used to address them. Authors specifically advocate the use of simple, unserstandable models, heavy use of visualization, and emphasize the importance of data management and the need to keep humans in the loop.


## The Nature of Fraud

The most difficult aspect of fighting fraud is identifying it. AT&T's goal was to create a fraud management system that was powerful enough to handle the many different types of fraud that they encountered and flexible enough to potentially apply to things they had not seen yet. Examples of some common varieties of fraud in the telecommunications world:

- **Subscription fraud.** Someone signs up for a service with no intent to pay. Calls associated with the line are fraudulent but are consistent with the profile of the user.
- **Intrusion fraud.** An existing, otherwise legitimate account is compromised by an intruder, wo subsequently makes or sells calls on this account. Legitimate calls may be interspersed with fraudulent calls, calling for az anomaly detection algorithm.
- **Fraud based on loopholes in technology.** As an example, voice mail can be configured in such a way that calls can be made out of the voice mail system. The fraudster compromises a mailbox (e.g. by guessing a weak password) and then uses the system to make outgoing calls. Legally, the owner of the voice mail system is liable for the fraudulent calls.
- **Social engineering** exploits human interaction with the system. The fraudster pretends to be someone he/she is not, such as the account holder or a phone repair person to access a customer's account.
- **Fraud based on new technology.** New technology, such as VoIP enables international telephony at very low cost. Fraudsters realized they could purchase the service at a low proce and then resell it illegally at a higher price to consumers unaware of the new service, unable to get it themselves, or technologically unsophisticated. Detecting this requires monitoring and correlating telephony usage, IP traffic and ordering systems.
- **Fraud based on new regulation.** In 1996, FCC (Federal Comminications Commission) modified payphone compensation rules, requiring payphone operators to be compensated by the telecommunications providers. This spawned a new type of fraud - payphone owners placing spurious calls from payphones to toll-free numbers simply to bring in compensation income from the carriers.
- **Masquerading as another user.** Credit card numbers can by stolen by various means and used to place calls masquerading as the card-holder.

There are many more fraud techniques, some of which are quite sophisticated and combine more than one known method. New types appear regularly, and these schemes evolve and adapt to attempts to stop them.


## Detecting Fraud

In the authors' experience for massive data streams, there is no way to effectively separate data analysis from the larger issues of data delivery, data management, and data storage. In the next sections, they describe the fraud detection framework developed at AT&T over the years and how data analysis fits into the broader scheme.

Key components of a fraud detection system:
- a continuing source of call detail data,
- a database to store the data,
- a set of detection algorithms,
- people to verify fraud and implement corrective measures,
- visualization tools to help people make diagnoses.

Experience in each of these areas is described in the following subsections.


### Data Sources

Analyzed data comprises records of phone calls. Common data formats:

- *Automated message accounting (AMA) format:* comprehensive, used to create phone bills. Nowadays transmitted electronically and often can be sent in real time. Drawback: details for a call are only available after the call is complete.
- *Data from celullar calls:* no standardized form. Equipment vendors for cellular switches have their own complex formats.
- *SS7 signalling data:* used to set up calls, independent of the voice network, similar in volume to AMA, can be collected real-time. Additional advantage: can be sampled at call setup, avoiding the delay until call completion.
- *Line Information Database (LIDB):* gives the status of other calls, such as bill-to-third party, and calling card calls.

Along with all these data, we also must deal with a contunuous stream of metadata. This large data stream brings greatly increased complexity. The tight integration of data analysis and data management is a direct result of the volume and complexity of the data sources.

### Storage
### Detection
### The Role of Humans
### Visualization

## Implementation

## Conclusion
