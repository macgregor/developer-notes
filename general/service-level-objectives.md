# Service Level Objectives
Service level objectives are a way to define what behavior/performance you expect of a
service. From Google's Site Reliability Engineering book:

>We use intuition, experience, and an understanding of what users want to define service level indicators (SLIs), objectives (SLOs), and agreements (SLAs). These measurements describe basic properties of metrics that matter, what values we want those metrics to have, and how we’ll react if we can’t provide the expected service. Ultimately, choosing appropriate metrics helps to drive the right action if something goes wrong, and also gives an SRE team confidence that a service is healthy. [1](https://landing.google.com/sre/book/chapters/service-level-objectives.html)

## Indicators (SLIs)
> An SLI is a service level indicator—a carefully defined quantitative measure of some aspect of the level of service that is provided.

These are the actual metrics gathered and queried to discern the health of a system.
Raw data is generally gathered and aggregated to determine averages or percentiles.
Common SLIs include error rate (e.g. 1% of requests return 500 response codes),
system throughput (e.g we handle 1000 requests per second) and availability (e.g.
  the system has 99.999% availability).

## Objectives (SLOs)
> An SLO is a service level objective: a target value or range of values for a service level that is measured by an SLI. A natural structure for SLOs is thus SLI ≤ target, or lower bound ≤ SLI ≤ upper bound.

This is a stated objective for the SLIs you have defined. So you might have a goal of
availability > 99% or request latency < 100 milliseconds. Setting these objectives and
making users aware of them gives the user an idea of what to expect. It can help reduce
unfounded complaints about slow performance (because you can point them to your SLO
and current metrics and say "this is expected"). Without these expectations, clients
tend to form their own in their mind to justify this is slow or it is down all the
time.


## Agreements (SLAs)
> SLAs are service level agreements: an explicit or implicit contract with your users that includes consequences of meeting (or missing) the SLOs they contain

This is a penalty for when SLOs are not met. Generally between a paying client and
a company providing service. Many cloud platforms provide financial compensation to
clients if their services do not meet the recommended availability, for example. It
doesn't have to be in the form of an explicit contract though, google search doesn't
have an explicit agreement with each user using the product but there are huge
financial ramifications if it goes down (advertising revenue, reputation, etc).




## Sources
1. [Site Reliability Engineering at Google](https://landing.google.com/sre/book/chapters/service-level-objectives.html)
