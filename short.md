# Telecom Fraud Overview

## Signature Based Alerting

A signature vector can be kept on record for each customer. The signature
is a statistical summary of the customer's previous behavior containing data
such as calling rate, distribution of calls by day of week, hour of day,
distribution of call duration, regions of the world called, most frequent
countries and phone numbers called, etc. Such information can be stored in
about 500 bytes per customer.

A generic fraud signature is defined *(or maybe more)*.

An incoming call is compared both to the current signature of the customer
and the generic fraud signature. If it resembles normal usage pattern more,
then its characteristics are used to update the signature. If it
resembles fraudulent usage pattern more, the customer's fraud score is
increased.

Signature-based alerting tends to produce good results at finding changes in
calling patterns that indicate fraud. AT&T uses many simple detection
algorithms rather than one single complex model. Various algorithms may
generate an alert on the same number, and these alerts are combined into a
single case to be investigated.

