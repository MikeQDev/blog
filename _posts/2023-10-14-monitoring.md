---
layout: post
title:  "Monitoring (and alerting)"
categories: Monitoring
tags: Monitoring Alerting Tooling On-call
author: Mike
---

*Note: this post focuses primarily on monitoring and alerting; not observability*

## Reality check
As brash as it sounds: if you don't have adequate monitoring and alerting built into your systems (along with proper incident response), then you don't care about your users

Without proper M+A (Monitoring and Alerting) practices, you risk poor user experiences, which undoubtedly leads to loss of revenue, reputation, and customer trust. Proper M+A techniques are crucial to the success of your business

As a rule of thumb: if it's not monitored, it's not ready for production. Unfortunately, engineers commonly implement monitoring solely to "check the box". This can be even worse than not having any monitoring at all -- think about the noise generated from false alerts, or flat out spinning your wheels to monitor things that yield no value. It's vital that your M+A approach is sound and enables you to serve your customers reliably

## How to do it right?

### An APM Software Solution

APM (Application Performance Monitoring) solutions come in different shapes and sizes, and at the end of the day aim to provide insights about how your systems are performing. From the data an APM solution captures, to the way it's built (FOSS, vendor, in-house, etc.), to how it's deployed (self-managed or as a SaaS), there exist solutions for everyone. Assuming you're not a M+A company, your best option usually is to purchase an APM solution as a SaaS. A managed SaaS solution is unarguably much less expensive than developing and maintaining an APM solution in-house

Before jumping into buying a SaaS solution that [falsely promises you the world]({{ site.baseurl }}/2023/08/12/vendorLockIn/), know that there's no such thing as a tool that offers perfect M+A out-of-the-box (while vendors may try to sell you on this, these tools have zero context about your app or business immediately; you'll need to invest a bit into tuning the solution before you unlock its full potential). The tool(s) that work best for your organization will take some investigation and configuring, and even then, each solution has its limitations -- like everything else in engineering, there are trade-offs to consider

#### One solution is not always enough

One APM solution may not be sufficient for your orgs needs -- you may need to equip your teams with multiple tools to ensure they can achieve operational excellence. Having multiple tools (tool fragmentation) can enable your org with the "best of breed" solutions (keep in mind these tools should work together and correctly correlate your data). For example: you may use ElasticSearch for logs, prometheus for metrics, and a different tool for the alerting. There's nothing wrong with this, as long as the tools work in harmony

Having several tools in the M+A space adds some overhead, but this may be the appropriate approach based on your orgs needs. As long as you're investigating tool(s) that fill a need for your engineering teams -- whilst erring on the side of caution of vendor lock-in -- you should be on the right path

*Disclaimer: I'm not saying to grab as many shiny tools as you can from the get-go; starting small with a single solution is usually a good first step, then evolve as necessary*

### Culture

Everyone in your org needs to be on the same page with your M+A and incident response process. From the engineers to upper-level management -- on-call or not -- the practices must be understood (at different depths, depending on the role) and adopted accordingly. Not having this transparency runs the risk of engineer burnout, underappreciated on-call engineers, excuses, and system downtime

#### Who builds it, runs it
I'm a firm believer of "you build it, you run it" (basically, if you write the software, you should be responsible for it in production as well). The contrary often results in teams writing and deploying software, then expecting a help-desk/support technician to execute steps from a runbook (more on this later) to solve issues when they occur. This is a *terrible* idea!

Firstly, because no matter how well-documented the software and/or runbook is, the software authors likely know how to address its issues best. Having the software author troubleshoot an issue in their application usually results in a lower mean-time to resolution (MTTR) (AKA, happier customers)

Secondly, there's not much incentive for engineers to write resilient software if they're not on the hook for its issues in production -- not only will there be more bugs, but there likely won't be sufficient visibility into the system for the poor soul who has to resolve it's incidents!

##### Handling pushback
As much kicking and screaming engineers may do when hearing about potentially being on-call, the you-build-it-you-run-it methodology is usually the optimal option. If engineers know they're on the hook when something goes wrong at 3AM, they are more incentivized to write better software, which results in less issues and downtime. To get acceptance of on-call, two things come to mind:
1. Additional compensation. Afterall, on-call may seem like a nightmare to many -- some sort of compensation is only fair (regardless of the profession; from nurses to electricians, any sort of on-call duty that disrupts life should be compensated for). Compensation doesn't always have to have to be monetary; it can also be rewarded as a form of additional PTO (Paid Time Off, which incidentally, is a great way to recuperate after an on-call shift)
2. Light at the end of the tunnel. On-call may not be forever. If a development team has proven their software reliable and properly observable, the "reward" may be handing off the software to a dedicated SRE or ops team within their org for production management. Of course, once a products reliability starts to slip, production ownership should rightly slide back to the development team

##### Handling more pushback!
Engineers fighting the introduction of an on-call rotation likely won't be the first occurrence of pushback. Before implementing on-call, practices and concepts must be laid out and agreed upon. The initial discussion of these practices and concepts is highly likely to provoke resistance. People are too often set in their ways, and fearful of change. Most people enjoy the comfort of repeating practices they've been familiar with from legacy systems, but remember: we're in a swim-or-sink industry -- tech is an ever-evolving field that we must keep up with

The more people in your organization, the more cultural shift is required to achieve conformity with practices. Whether individuals (or groups) have uncertainty or doubt around evolving practices, they must be shown the way (and ideally, before they and their systems experience pain past their thresholds). Getting buy-in from people is a three-step process: 1) them understanding why change is necessary, 2) shifting from understanding why change is necessary, to believing that change is necessary, and 3) taking the time and effort to adopt the change. To be successful, this process must be as simple as possible, and there's no better way to do this than by offering easy-to-digest patterns

#### Patterns

No matter the size of your org -- and whether you're running a couple of apps or an enterprise-scale amount of apps -- it makes sense to keep your M+A pattern(s) and solution(s) as common as possible (and unique only when necessary). One-off solutions should be avoided, especially as your technical ecosystem grows. If many one-off solutions exist, not only does it cost more to maintain and ensure each solution is working properly, but it also adds decision friction for teams seeking a solution

The familiarity of a common practice instills confidence and makes collaboration easier. Whether a new engineer is joining your team from another team, or you're looking to assist another team in their troubleshooting, M+A can -- and should -- be a breeze when following familiar patterns

To achieve commonality, it's not unusual for a dedicated monitoring team to lay out the tools and patterns for their engineering community to leverage. Be clear where to draw the line draw here, though -- these "centralized" monitoring teams may provide guidance and operate the APM platforms, but should **not** be responsible for instrumenting actual apps; instrumentation is the responsibility of the development teams (the engineers who know their code and business logic best)

If you don't have a team dedicated to M+A, perhaps assembling a cohort (or "working group") of production enthusiasts across your company will help lead to a solution. Just know that whichever way you get to a common solution, **the solution must be self-service**. Otherwise, you'll end up with frustrated engineers and low levels of adoption


### When?

Monitoring should be designed and implemented into each system as early as possible. A car manufacturer wouldn't wait to add on features such as a speedometer or other gauges until after the car was built and sent to the consumer -- so why should your software be any different? If instrumenting your app with monitoring (and observability) from day one of development is not possible -- for example, if you're prototyping and expecting a lot of refactoring -- it may make sense to wait a bit before implementing, but do not delay including monitoring any later than your apps release to production. Remember: the longer you wait to implement M+A, the more expensive it will be

At a minimum, SLOs (Service Level Objectives) should be defined as soon as system functionality is determined. SLO *definitions* tend to be more of a social agreement than actual implementation. Sometimes SLOs can even influence which technologies to include in your systems stack (e.g.: performance requirements defined in an SLO may help guide which language or components to use in the system) -- better to hash these out sooner rather than later. If you're unsure about SLOs, know they are practices applied by many big tech companies and plenty of [free resources for creating SLOs](https://sre.google/resources/practices-and-processes/art-of-slos/) exist

### How?

#### Signals
[Telemetry signals]({{ site.baseurl }}/2023/07/07/signals/) such as metrics, logs, and traces are useful for monitoring. These signals have the ability to alert engineers when thresholds (preferably, [SLO burn rates](https://sre.google/workbook/alerting-on-slos#4-alert-on-burn-rate)) are at risk of being breached. While there are many signals you can monitor and alert on, my recommendations are as follows, in order:

- Metrics -- metrics come in the form of application metrics (related to your business logic; perhaps even KPIs) and system metrics (related to infrastructure). Your best bet is to capture both, and only alert on what matters most to your end users. A few examples:
  - Latency -- useful; if users experience slowness in your site, they are likely to bounce (leave)
  - Availability -- useful; if users can't access your system, they are likely to bounce (leave)
  - CPU utilization -- *may be* useful, but if you're at 99% CPU usage and systems are still operating smoothly, why bother alerting?
  - Disk storage -- *may be* useful, but if you're in the cloud, using elastic/scalable storage, it's less useful. Instead, maybe monitor the *trend* of how much storage space is utilized (e.g.: disk usage increased by 20gB last night), as well as the prediction of future utilization
  - **Note: while the nature of computing is often spikey (which can lead to false alarms), aggregating metrics into SLIs (for comparison against SLOs) over a period of time is usually the sensible approach for M+A**
  - Other examples can be found in [Google's example SLO document](https://sre.google/workbook/slo-document/)
- Traces -- could alert on traces, though you'll likely want to alert on *aggregate* traces (e.g.: many traces reporting failures, not just a single trace) to ensure the alert was not triggered by a single, transient failure
  - Rather than traces being the *why* of an alert, traces may be better utilized to identify *who* to alert in a distributed system. For example, if TeamA's app is facing issues, but the failing spans are stemming from a dependency owned by TeamB, there's usually not much value in alerting TeamA. Perhaps make TeamA aware via a low-urgency alert, but the high-urgency alert should go directly to TeamB for proper resolution
- Logs -- *could* alert on logs, but it's likely to lead to a lot of noise. For this reason, if you do choose to alert on logs, it should be a low urgency alert (not worthy of paging an on-call engineer), such as auto-generating a ticket or sending an email notification
  - You *could* alert on log levels (e.g.: ERROR, FATAL), but this is less reliable than aggregate metrics, as transient failures tend to happen (i.e.: a dependency goes offline for a few minutes, causing error logs, then comes back online. Probably not a high-priority to alert on since there's nothing to fix)
  - You *could* alert on log string matches such as `"exception"` or `"error"`, but the purpose is defeated if your end users enter these trigger terms into an input field for fun, thus triggering a useless alert
  - Depending on the criticality of your application, whenever a never-before-seen error is logged, it may be worth alerting on. This way, the engineering team is made aware of the new failure mode and can adjust their code to handle it appropriately in the future

Given the above examples, hopefully you've picked up that **alerts must indicate that user experience is in a degraded state, and that the alert is ACTIONABLE by the engineer receiving the alert**. Afterall, there's no point in alerting if the end user isn't experiencing pain, or if a fix is out of reach. An alert should evoke a sense of *actionable* urgency. Anything less than urgent should fire a non-disrupting notification such as a log entry, an email, a message to your internal chat room, or an auto-generated ticket

#### Automate!

##### Onboarding
Automation is a huge step towards successful onboarding. The onboarding of a service to a M+A solution should either be entirely self-service, or in a perfect world: self-registering. Skipping automation here (and therefore risking proper onboarding) is the first sign of shortcuts being taken, and hints that future issues are likely to slip through the cracks

##### Incident response
If you have runbooks that contain manual commands/steps for on-call engineers to execute when things go wrong, get rid of these commands/steps. **If there are any manual commands in your runbook, you're likely doing something wrong**; this is a screaming symptom of inadequate automation and opens your system up to human error. Why would you disrupt (sometimes even wake up) your engineers so they can execute commands ad-hoc, when these commands could've ran automatically?

Whether a quick-fix solution entails a simple restart of a container or a traffic shift to another region, some sort of self-healing enables a much smoother (perhaps unnoticable) experience for everyone. Automation with self-healing is the only way tight SLAs can be respected -- a 99.9% availability allows for 1.44 minutes of downtime a day; that's barely enough time during an incident to spring to your machine, turn it on, and get to troubleshooting! To relate this concept back to money: if Facebook makes $3,000 per second, every minute of downtime costs them $180,000. Talk about a high stress environment if self-healing is not in place!

*Note: while most technical changes can be automated, policies (politics) may block some automation from being feasible in production. For example, the company I currently work for requires a change ticket -- along with approval -- for \*every\* modification to production. A huge disservice for everyone from the customers to the engineers to the business. This is something I will raise up in my org soon, as we must evolve this practice to keep our head above water*

*Update: above note/concern resolved! The process explained to me was: an initial change ticket to \*configure\* self-healing requires an approval, but the automated resolution action performed by the system itself once the configuration is in place would \*not\* require a change ticket and approval to automatically self-heal*

### Where?

Always monitor your systems as close to your users as possible. While it may be easy to identify failed requests at the load-balancer, how do you know users were even able to submit a request? Maybe there's a bug in the client software that makes their 'Submit' button hidden or disabled. Perhaps you're deployed to a single region, resulting in your users on the other side of the globe experiencing higher latencies that make your app less valuable to them. **Many users won't complain -- they'll simply stop using your application, so monitor proactively!** You won't always know your users are experience pain until you monitor as close to your users as possible

Assuming a webapp, the closest place to a user you can monitor is typically in their browser. A couple of things you may want to monitor in a browser application are: DOM load time, user interaction with elements, **stack traces**, and HTTP response latency. While client-side monitoring opens risks for credibility (and sometimes even loss) of telemetry data, techniques exist (but are not covered in this blog post) to mitigate these concerns

*Off-topic of monitoring: to be a bit more proactive, additionally shift left. Perhaps your CICD pipeline can include a post-deployment step in [pre-]production environments that perform performance and E2E testing. This extra step may help catch bugs and performance issues before your users run into them. Afterall, the more changes you make, the more likely things may go wrong*

#### Less is more

Adding more software doesn't necessarily mean you need to add more monitoring. Monitoring does not fix systems, and more of it is not likely to improve anything; it can actually generate unnecessary noise and confusion. While there can be millions of things that can go wrong with within a system (root causes), there are always only a small handful of symptoms that surface (see [The RED Method](https://grafana.com/blog/2018/08/02/the-red-method-how-to-instrument-your-services/) for more details). So keep it simple; don't try to account for everything that can go wrong, and instead focus on monitoring symptoms with SLOs

While searching for a M+A solution, you may be tempted by "AIOps". I don't cover this topic in this post as I haven't had experience with it, and [The Misleading Promise of AIOps](https://thenewstack.io/observability-and-the-misleading-promise-of-aiops/) does a lovely job at summing up at why you should be wary of it. To reiterate from the last paragraph: keep it simple

## Often overlooked
This blog post primarily focused on monitoring metrics from infrastructure (narrowly, compute and storage) and applications. While these metrics are critical to monitor, we can't forget the backbone holding everything together: networking and security. Without either, nothing is possible, so it makes sense to monitor them as well

*Disclaimer: I'm no SME when it comes to security or networking (most people aren't), so please bear with me and take the following ideas with a grain of salt*

Attackers will almost always find a way to get into systems. Once intruded, logs (e.g.: syslog, sshd, auditd, etc.) may be able to help identify the blast radius. ...assuming the logs weren't maliciously deleted or tampered with, and log forwarding services weren't disabled (you do have a way to backup your logs, right?) The extremity of software attacks can go beyond our imaginations, so one way to aide in the recovery of malicious attacks on software is to monitor at the hardware level. [Network TAPs](https://en.wikipedia.org/wiki/Network_tap) are a hardware solution that capture *all* network traffic as it goes over the wire (think of [tee (1)](https://en.wikipedia.org/wiki/Tee_(command)), but for network traffic). For the sake of this blog post, these investigative "monitoring" techniques are simply ideas; to find a strategy that works best for you, consult the proper experts

Performance and reliability considerations are also crucial when monitoring -- not only do they affect your end users experience, but they also affect your compute spendings. Are your systems forwarding telemetry data to a backend far away, causing your critical components to slug along while waiting for the telemetry transfer to complete? Don't be afraid to offload the telemetry forwarding/exporting work to another component. Many patterns exist for this, such as a sidecar'd OpenTelemetry collector that can perform potentially expensive operations such as batching, encrypting, and filtering your telemetry before sending (and also handling retries on transmission failure). An even simpler use-case: a standalone log agent that scrapes logs from disk (or stdout) and forwards them to your logging backend. As long as your engineers can access telemetry data in a timely manner (ideally, no longer than 60 seconds), shedding the responsibility of potentially expensive and error-prone telemetry exports off of your mission-critical apps may be worth investigating

## Final thoughts

To reiterate the first section of this post: if you don't have adequate monitoring and alerting in place, you don't care about the success of your business or users. If you don't have M+A in place, your service is not ready for production. M+A must be a first class citizen when it comes to building and maintaining reliable services, especially as users become more dependent on (and demanding of!) technology

Many tools exist for M+A purposes; choose your tools well, else suffer the consequences. Remember that any number of tools -- no matter how good -- are not a magic bullet; you must be aware of the limitations and tradeoffs of the tools you choose, as well as learn and invest in your tools. Also keep in mind that your M+A journey must be mission-driven, not tool-driven

Get creative with your monitoring data. While a good amount of this post focused on using monitoring data to notify a human, it shouldn't end here. Monitoring data can be used for things such as analytics, or even influencing data-driven events such as self-healing and auto-scaling your compute resources when thresholds reach certain values

If you are unsure of where to start with monitoring: consider business KPIs and user journeys, and leverage SLOs when possible

Lastly, monitoring and alerting is a never-ending evolution. Just as our systems evolve, our M+A practices must evolve with them as well. Sink or swim!

### Further reading

This post was inspired by and contain many ideas from the book [Practical Monitoring by Mike Julian (2017)](https://www.oreilly.com/library/view/practical-monitoring/9781491957349/). Although technology has changed vastly since published, the majority of the concepts in the book still hold true; certainly worth a read

[Monitoring Distributed Systems](https://sre.google/sre-book/monitoring-distributed-systems/) from Google's SRE book

* content
{:toc}
