---
layout: post
title:  "Moving beyond anomaly detection"
categories: Monitoring
tags: Monitoring Alerting SLOs Anomaly Detection
author: Mike
---

This post outlines basic alerting approaches, "anomaly detection", and why SLOs are necessary for appropriate monitoring and alerting

# Basic alerting approaches

Being notified about an issue with your service(s) commonly happened in a couple of ways:

1. Being notified by the customer(s)
  - How embarrassing! This is the worst type of notification -- damage has already been done and customers are frustrated. Monitoring and alerting must be more proactively implemented
1. Alerting based on log record content
  - Simple, but transient errors will introduce alert noise and fatigue. Additionally, clever users can include an alertable keyword such as "ERROR" in their request to trigger a false alert
1. Synthetic monitoring
  - Sure, automated probes checking your services health can be a comforting thought, but it doesn't represent true end user experience
1. Static metric thresholds
  - Noisy and impractical, since compute is naturally bursty/spikey
1. Static metric thresholds with a sliding window (i.e.: alert when a threshold is breached for N minutes)
  - A bit "better" than a windowless static threshold, but introduces delayed detection and requires additional fine-tuning to prevent over-alerting and under-alerting

*More detailed ideas about how signals can be used for alerting can be found [here]({{ site.baseurl }}/2023/10/14/monitoring/#signals)*

# Introducing: anomaly detection
Basic alerting approaches have their limitations, so this is a perfect opportunity for observability vendors to upsell. "Add our instrumentation to your apps, we'll take care of the rest (including alerting)"

But how could a vendor solution automatically detect when your service is not behaving appropriately if it doesn't even know *what* your service's expected behavior is? Machine learning, AI, and other statistical techniques can *help* with identifying abnormal behavior via anomaly detection

Anomaly detection starts by calculating a "performance baseline" based on a systems previous performance data (typically, metrics). As the system continues to emit metrics, these metrics are continuously evaluated against (and incorporated into) the performance baseline in the backend monitoring tool. When the most recent metrics exceed the baseline's standard deviation threshold (outliers), an alert is triggered

Anomaly detection is offered as a fairly effortless solution for alerting, but it's important to be aware of its shortcomings

## Shortcomings

### Uncommon events

High-traffic events such as [holidays, launches, and special sales](https://grafana.com/docs/grafana-cloud/alerting-and-irm/machine-learning/forecasts/holidays/) may:
- Trigger false-positive alerts due to a [business-expected] fluctuation of performance or traffic
- Skew your service's baseline by incorporating these abnormal metrics into its baseline

A way around this shortcoming is to specify "maintenance windows" in your monitoring tool, which will:
- Disable alerts for the specified time period
- Exclude metrics emitted during the specified time period from the baseline calculation

### Boiling frog

If performance *slowly* degrades over time, anomaly detections will incorporate these slightly-degraded metrics into the baseline as "normal". This can lead to the ["boiling frog"](https://en.wikipedia.org/wiki/Boiling_frog) problem

#### Example

The below example outlines how a service latency could *double* over time, while sneaking under anomaly detection's radar

1. Service latency is initially baselined at 500ms
2. Something related to or within the service changes (e.g.: dependency is added, code is changed, infrastructure allocation decreases, etc.), bumping service latency baseline to 550ms (10% increase, no big deal in most systems)
3. Repeat step 2 a couple of weeks later, causing latency to increase by another 10% (605ms)
4. Repeat steps 2 and 3 (slowly increasing the latency by 10%) ~six more times, and your "normal" baseline latency now surpasses 1sec; roughly double the initial baseline

While anomaly detection may not pick up on this issue, your users will

### Complex

The standard-deviation-threshold algorithm mentioned earlier is a basic example of anomaly detection and has it's shortcomings, so why wouldn't a vendor upsell customers on a more "advanced" (complex) algorithm for anomaly detection?

They do. But there are still issues with these more complex models. How do we know the proper ML algorithm is used? How can we ensure our data is correctly labeled for proper evaluation? Who trained these models, and was the model trained on sufficient, valid data?

A vendor could easily hand-pick data into a demo to show these complex models working and alerting perfectly against *their tailored data*, but when your "foreign" metrics are introduced to the model, there's a high chance that the results won't be as promising

*Disclaimer: I'm far from an ML or AI expert; the above concerns are just some thoughts that come to mind*

# Moving on: SLOs

Sure, anomaly detection can provide some initial, low-effort alerts and can also be applied to several problem spaces (e.g.: security and fraud detection), though the best way to gauge and monitor the performance of your systems is through explicitly defined [Service Level Objectives (SLOs)](https://sre.google/sre-book/service-level-objectives/)

While SLOs have shortcomings of their own, I can't think of an alerting approach that's more robust~~, simple, and understandable~~ (okay, there is *some* learning curve when first exploring SLOs) than defining an SLO for what "good" looks like, then alerting on the [error-budget burn rate](https://sre.google/workbook/alerting-on-slos/#6-multiwindow-multi-burn-rate-alerts) for proactive issue detection. No complex models or algorithms, just simple(-ish), white-box math! As a bonus, you can use SLOs to support your SLAs

So rather than having some "magical" anomaly detection alert you when it *thinks* something is wrong, YOU are empowered to (and should!) explicitly define what acceptable behavior is for your systems, through SLOs

* content
{:toc}
