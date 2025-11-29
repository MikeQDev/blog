---
layout: post
title: "Incident Response Management"
categories: Production-Support
tags: Alerting SLOs Incidents Production Support Uptime
author: Mike
---

Incident Response Management (IRM) is a non-negotiable for all critical production systems. The goal of IRM is to **provide high service reliability** through rapid response to production incidents, without burning out humans through alert fatigue and unsustainable on-call burden.

## Why consider Incident Response Management?

You can bake as many resilience practices into a system as you want, but **outages are inevitable in complex systems**. When incidents occur it's **crucial that the incidents are responded to with a structured, controlled manner**.

Without a structured IRM process, chaos ensues: downtime is extended longer than necessary and mistakes are likely to be made. After all, even the best engineers make mistakes while under stress. Therefore, it's vital that proper preparation, response, and learning occurs throughout the incident response lifecycle.

This article focuses on the concepts of building an effective IRM process that helps cultivate calm, fast, and safe recoveries during incidents.

## How to implement Incident Response Management

Simply creating an "on-call rotation" isn't enough; creating a successful IRM framework takes effort.

This section describes the IRM cycle: starting with tools, onboarding, handling and incident, and ending with postmortems.

### Tools needed

**Establish tooling before defining process.** Without proper tooling, incident response cannot occur effectively.

The following tools are critical for successful incident response:

- Incident management platform (e.g.: PagerDuty, Opsgenie) - plans on-call schedules and pages the on-call responder during incidents.
  - Alerts must sent in a _timely_ manner.
  - Alerts must be _actionable_.
  - In most cases, alerts should be triggered by user-facing symptoms based on SLO error-budget concerns.
- Documentation management system (e.g.: Confluence) - create, save, and collaborate on incident information and postmortems.
  - IRM-related documents act as **transparent, centralized, single sources of truth**.
  - Templates for IRM documents should be available for fast and consistent documenting.
    - An example incident response template may contain sections for incident summary, timeline, hypotheses, mitigation steps, and ownership.
- Communication platforms:
  - Dedicated chatrooms (e.g.: Slack, IRC, MSTeams) for async communication.
  - Conference calls for real-time coordination and decision making.
  - Email or social media for communicating with stakeholders and users.
  - Optionally, health/status pages.
- (Goes without saying) an observability platform with proper telemetry for diagnosing the incident.

To simplify IRM tooling, all-in-one solutions such as [Grafana IRM](https://grafana.com/docs/grafana-cloud/alerting-and-irm/) exist to provide all of the tools necessary for incident response out-of-the-box.

Additionally, it may be worth setting up a secondary monitoring platform -- a monitor for your monitor, ideally deployed in separate regions. A potential pitfall of having a single monitoring instance is if the single monitoring instance fails, you may not know about the failure immediately.

### IRM Onboarding

Once you have the required IRM tools available, your services can start being onboarded to your IRM system with the following steps:

#### 1. Identify critical services

Depending on organization size, you may have few to many services. Get the most incident response value ASAP by identifying and focusing on onboarding your mission critical services to IRM first.

##### Define SLOs

Without an SLO, it's near impossible to define what good[-enough] reliability is for each service. Establishing clear targets for service reliability and performance will ensure alignment between your service and customer needs, and may ultimately influence SLAs.

#### 2. Define an alert policy

Alert policies are jointly determined by SRE teams and business stakeholders. A few recommended alert guidelines are as follows:

- Alerts should be SLO-based.
  - Alerts based on signals other than SLOs are [likely to lead to noise](/blog/2024/06/16/beyond-anomaly-detection/).
- Alerts must be worthy of waking up a human.
  - If a human doesn't need to act immediately or if the alert is not actionable, do NOT alert the person on-call; create a ticket instead.
- Alerts must be related to user-facing symptoms.
  - Exception: infrastructure-based alerts such as disk space nearing exhaustion, resource constraints preventing a cluster from scaling, high amounts of pod evictions, etc. are OK to alert on if you're responsible for infrastructure.
- Alerts must be sent timely manner.
- Alerts must be responded to in a timely manner.
  - Example response time agreements may specify that on-call must respond to incidents within:
    - 5 minutes for incidents on services that impact revenue.
    - 30 minutes for degraded but not catastrophic incidents.

#### 3. Accept a service into on-call

**Note:** "Accepting" a service into on-call is primarily applicable if a dedicated SRE team will be on-call for an application team's service.

Each critical system going to production should follow some sort of "production readiness check" before going live. For example:

- Ensure proper observability that [emits, processes, and stores](/blog/2023/08/12/vendorLockIn/#1-telemetry-generation) [telemetry signals](/blog/2023/07/07/signals/).
- Potential failure points are known, documented, and resilience practices are put into place.
  - Use forms of stress testing such as load testing and chaos engineering as tools to identify gaps.
- **Signoff/approval from a senior Site Reliability Engineer.**
- Any items mentioned in [Google's SRE book's Production Readiness Review (PRR)](https://sre.google/sre-book/evolving-sre-engagement-model/#:~:text=The%20PRR%20Model).

_Note that if an application supported by a dedicated SRE team consistently fails to meet it's agreed-upon SLO or becomes unsustainable for the SRE team to continue supporting, "handing the pager back" to the application team is an option until appropriate reliability fixes have been implemented to resolve the issue(s)._

#### 4. Create runbooks

A runbook is traditionally a detailed, step-by-step document that guides engineers through common tasks during incident response. Most of the runbook tasks should be automated when possible, so that the responder can focus on solving the issue rather than performing tedious tasks.

With automation in place, runbooks should be slimmed down to information that will get the incident responder on the right track for diagnosing and solving the incident as soon as possible. Some resources you'd find in a runbook could be links to useful telemetry, dashboards, documentation, and escalation contacts.

#### 5. Be proactive

If you're lucky, the systems you're on-call for will have very few incidents. The caveat to not having many incidents is that **incident response skills atrophy if not exercised regularly**, leading to unprepared response when a real incident occurs.

Incident response should be second nature. Introducing incident response drills by purposely injecting failure into systems or hosting SRE-involved activities such as the [Wheel of Misfortune](https://sre.google/sre-book/postmortem-culture/#wheel-of-misfortune). The cadence that these drills are performed can be set by your organization, and should be performed monthly to quarterly depending on incident frequency and team size.

### Handling an incident

#### Severity levels

Before explaining how incidents should handled, it's important that your organization establishes incident severity levels to help determine:
- Who should be paged.
- Response time expectations.
- Escalation paths.
- Communication requirements.

A common severity classification framework is as follows:

- **P0/SEV1 (Critical)** - complete service outage or severe degradation affecting all or most users. Immediate response required.
  - Examples: entire application down, data loss occurring, security breach.
  - Response: page entire on-call team, establish incident command, update status page.
- **P1/SEV2 (High)** - significant feature degradation or partial outage affecting a subset of users. Urgent response required.
  - Examples: critical feature broken, performance severely degraded, authentication failing intermittently.
  - Response: page primary on-call, consider incident commander for coordination.
- **P2/SEV3 (Medium)** - minor feature issues or performance degradation with workarounds available. Timely response required.
  - Examples: non-critical feature broken, elevated error rates within SLO threshold.
  - Response: page primary on-call, can typically be handled during business hours.
- **P3/SEV4 (Low)** - minor issues with minimal user impact. Can be addressed during normal work hours.
  - Examples: cosmetic bugs, minor inconveniences.
  - Response: create ticket, no page required.

**Your organization's severity levels should align with your SLOs and business needs.**

#### People

Though machines and AI are becoming more useful, humans are still at the core of IRM. [Google's Incident Management Guide](https://sre.google/resources/practices-and-processes/incident-management-guide/) describes the following common people-roles in incident response:

- **Incident Commander (IC)** - coordinates the overall incident response.
- **Operations Lead (OL)** - focuses on mitigating the issue, minimizing user impact, and resolving the problem.
- **Communication Lead (CL)** - provides regular updates to stakeholders and acts as a point of contact for incoming communications.
  - May also be responsible for updating incident docs and external customers.

Additionally, a planner role may be introduced to assist in the tracking of work, filing bugs, managing shifts/handoffs (for incidents that span many hours or days), and coordinating long-term tasks.

**The incident response roles in your organization may deviate from the roles defined above depending on your organization's size and IRM configuration.**

#### "The 3 C's"

It's worth noting that the [Incident Command System (ICS) standard used by the US government](https://nctr.pmel.noaa.gov/education/IOTWS/ICS/ICS_Factsheet_Feb07.pdf) to respond to emergencies has proven successful and reproducible enough to be used in software incident response; even Google's incident response system is based on ICS. ICS responds to emergencies using the "three C's":

- Coordinate - organize the response effort, assign roles, manage tasks, and keep everyone aligned.
- Communicate - ensure information flows to the right people by providing clear, timely updates to incident responders, stakeholders, and users.
- Control - maintain focus and overall contol of the incident by preventing chaos.
  - Some examples of chaos may be confused ownership of roles and taking premature actions.

No matter the roles and size of your incident response team, the 3 C's ought to be followed to help ensure smooth incident response.

#### Incident response workflow

When an incident occurs, a typical response workflow is:

1. **Acknowledge the alert** - confirm receipt of the alert through your incident management platform to stop further escalation.
2. **Assess severity** - quickly determine the incident severity level using your organization's severity classification framework. This determines response urgency and who else needs to be involved.
3. **Establish communication channels** - set up dedicated incident communication:
   - Create an incident-specific chat channel.
   - Start a conference bridge if needed for real-time coordination.
   - Update user-facing status page if it exists.
4. **Assign roles** - for higher severity incidents, explicitly assign incident response roles (Incident Commander, Operations Lead, Communication Lead).
5. **Begin investigation** - use observability tools to:
   - Identify affected components and scope of impact.
   - Review recent changes (deployments, config changes, infrastructure updates).
   - Formulate and test hypotheses about the root cause.
   - Document findings in the incident document.
6. **Implement mitigation** - take action to restore service:
   - Stop the bleeding; prioritize restoring service over finding root cause.
   - Consider safe, reversible actions first (e.g., rollback, failover, scaling).
   - **Communicate planned mitigation steps before executing.**
   - Verify each mitigation action's effect before proceeding.
7. (Optional) **escalate the incident** if:
   - Impact is worse than initially assessed.
   - Mitigation efforts are unsuccessful after reasonable attempts.
   - Additional expertise or authority is needed.
   - Incident spans multiple teams or systems.
8. **Verify resolution** - confirm the incident is resolved:
   - Check telemetry to verify that the symptoms have cleared.
     - Monitor error rates, metrics, traces, and logs.
   - If applicable, verify with affected users and/or stakeholders.
9. **Hand off or close** - properly close out the incident:
   - Update status page and stakeholders.
   - Mark incident as resolved in incident management platform.
   - If further investigation is necessary, hand off to appropriate team.
   - Schedule postmortem review.

### Postmortems

A postmortem is a **blameless** analysis of an incident, with a focus on learning and improving system reliability.

After each incident is resolved, there's still work to be done; for each incident, it's crucial to understand:

- What incident occured.
- Why the incident occurred.
- The impact the incident had.
- How the incident was solved.
- How the incident (and similar incidents) can be prevented in the future.

_Note that postmortems do not need to be "perfect"; postmortems should have sufficient details to be useful, while not taking too much time to write. Timeboxing may be an appropriate time-management technique to use when writing postmortems._

Good postmortems are:

- Written immediately after the incident was resolved, so that details can be captured accurately with fresh minds.
- Distributed and transparently available inside of the organization, so that other teams can learn from the incident.
  - Sensitive user data MUST be redacted from the postmortem.
- Reviewed and collaborated on by the appropriate parties.
  - Shared ownership and joint postmortem reviews help foster a successful IRM culture.
- Blameless (see following section).
- Followed up on (see following section).

*Note: Google's SRE book has an [Example Postmortem](https://sre.google/sre-book/example-postmortem/) in it's appendix.*

#### Blameless

Attaching personal blame to incidents creates a threat to psychological safety, encourages engineers to hide issues instead of address issues, and stunts learning.

Postmortems **must** be blameless. Postmortems are meant to fix systems, not people. If a person "caused" an incident, the true cause is within the system/process. Acting blamelessly encourages the hardening of systems and processes to prevent similar incidents from happening in the first place.

#### Follow-ups

After your organization gathers a few postmortems, it's useful to look through your collection of postmortems to identify patterns. You may find recurring patterns such as monitoring gaps, issues with automation, or insufficient guardrails. Once common patterns are identified, solutions to these common failure patterns can be prioritized with appropriate effort (process hardening, automation, etc.) to improve the system's reliability.

The investment in following up on postmortems is crucial for continuous improvement; no postmortem should go unreviewed.

## Final thoughts

A well-defined IRM framework is a requirement when supporting critical production systems. Mutual agreement between application engineers, SREs, and business stakeholders must occur to foster a successful IRM culture and process. IRM is one component of Site Reliability Engineering that ultimately contributes to business continuity and satisfied customers through high service reliability.

* content
{:toc}
