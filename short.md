# Telecom Fraud Overview - Short

## Signature Based Alerting

A signature vector can be kept on record for each customer. The signature
is a statistical summary of the customer's previous behavior containing data
such as calling rate, distribution of calls by day of week, hour of day,
distribution of call duration, regions of the world called, most frequent
countries and phone numbers called, etc. Such information can be stored in
about 500 bytes per customer.

A generic fraud signature is defined (or maybe more).

An incoming call is compared both to the current signature of the customer
and the generic fraud signature. If it resembles normal usage pattern more,
then its characteristics are used to update the signature (1). If it
resembles fraudulent usage pattern more, the customer's fraud score is
increased (2).

(1) The customer signature is updated by a version of exponentially weighted
moving average (see here[]), where the previous signature and information
from the current call are combined. Parameters of the expression can be
determined either using the calling rate or by the means of machine learning.
In the case of a new phone number, an initial signature from a set of
empirically built ones is selected based on the first two calls. Old
signatures not reinforced by new calls within a month or so are dropped.

(2) The overall fraud score of a number may increase call by call. To retain
the fraud score's nature of being an indicator of recent fraudulent activity,
fraud scores are decreased every day.

Signature-based alerting tends to produce good results at finding changes in
calling patterns that indicate fraud. AT&T uses many simple detection
algorithms rather than one single complex model. Various algorithms may
generate an alert on the same number, and these alerts are combined into a
single case to be investigated.

