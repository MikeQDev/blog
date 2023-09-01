---
layout: post
title:  "Vendor lock-in: a tale and how OpenTelemetry avoids it"
categories: Tooling
tags: Vendors Lock-in Rant OpenTelemetry Pipeline Collector Money Politics
author: Mike
---

<details markdown="1"><summary>Expand for a story, or simply continue reading to get to the details of this post</summary>
## A "Hypothetical" Story
### In search of a solution
Imagine this: you have a problem that needs solving. You scout out several vendors offering similar software solutions. You find a vendor whose offering seems to fit your needs and they promise you the world. You get the purchase approval from your org, then you seal the deal with the vendor. You're likely in a contract for some time, and if for whatever reason their solution doesn't work out: you can always switch vendors in the future, right?

### The gaps in the solution
You immediately get some value and excitement from the new solution you purchased, and all is well -- until it's not. You eventually realize there are gaps with the solution. You contact the vendor and three things are likely to happen:
1. The vendor knows how to solve your problem and generously assists you with filling the gap
2. The vendor upsells you on something you thought would've came included with the initial purchased package
3. The vendor scrambles to shoehorn your request into their feature backlog

Whatever happens, business continues as usual - for both you and the vendor

### Turning a blind eye
While you happily continue to enjoy your (potentially half-baked) solution, you've also unintentionally built a stronger dependency on your vendors solution. OK, no problem, as long as your vendors solution fits your needs and it interfaces with open standards (assuming they exist) - just in case their solution starts to fall short, so you can switch vendors. Note that you may have to research your vendors solution a bit for this information; there's a good chance open standard interfaces aren't shouted-from-the-rooftop (or even offered) by your vendor. Afterall, why would a vendor want to equip their customers with the knowledge and tools to easily switch to a competitor?

Defying existing open standards (or, likely wrapping the open standard in their own proprietary solution) can certainly be part of a vendors business model. But wouldn't this practice earn them a bad reputation? It could - and should - but it's hard to point a finger when most vendors are using similar tactics. So you suck it up, continue working with the vendor, and fall a bit deeper into their solution. All is (somewhat) well, until contract renewal time comes

### The price paid
At contract renewal time, the vendor jacks up their price quite a bit - to the point you likely won't be breaking even with the value their solution provides. "OK, I'll re-evaluate my options to see if I can get a better price" -- but this thought is futile -- you're already deeply locked into your vendors proprietary solution, and the vendor knows this. Good luck not only migrating your data from your current vendors solution to a new one, but also getting your org onboard for a rippling change (think about engineers having to rework every app they've already onboarded with the current vendor solution!) In hindsight, what seemed to be a long-lasting vendor-client relationship being built was actually a salesperson blowing smoke up your ass while sinking their claws in deeper the entire time

...

OK, that's all for story time -- hopefully it gives some context on why you'd want to avoid vendor lock-in. The remainder of this blog post is primarily written with the context of observability tools in mind, though remember vendor lock-in spans plenty types of technology offerings - for example, cloud platforms (AWS, GCP, Azure). Of course you'll likely need to choose at least one solution, just remember there's no need to be on a short leash with any specific vendor. There should always be at least one approach you can execute on to protect yourself from vendor lock-in while still reaping the benefits of a vendor solution - for example, with IaC: why stick with CloudFormation and be bound to AWS when you can use Terraform and flexibly run multi-cloud? Options are available, and it's your responsibility to be vigilant (to not be blindsighted by a vendors "perfect solution"). Anyhow, let's get into the observability part of this post...
</details>

## Reality: an open standard (for observability)
Imagine you need an observability solution for your org. Hopefully you'd avoid the trap mentioned in the above hypothetical story. It's highly probable that you would not want to build your own observability solution or host a FOSS solution yourself. Using a vendors observability (backend) SaaS is an appropriate approach -- assuming you've done the due dilligence to not fall into a vendors nasty lock-in trap as outlined above. What enables such avoidance of vendor lock-in with an observability solution is the open standard: OpenTelemetry (OTel). To understand how OTel avoids vendor lock-in, it's critical to first understand the "pipeline" or lifecycle of telemetry at a high level

### 1. Telemetry generation
To make your systems observable, telemetry must first be generated somewhere within your systems. Your applications or systems must generate [telemetry signals]({{ site.baseurl }}/2023/07/07/signals/)/data (metrics, logs, distributed traces, etc.), then emit this telemetry data to a separate component that will either 1) pre-process the telemetry data or 2) directly store the telemetry data for future retrieval & analysis

#### Available options
- Open standard solution: OpenTelemetry API/SDK + OpenTelemetry Agent
- Vendor solution: vendor-proprietary SDK ([example](https://github.com/Dynatrace/OneAgent-SDK-for-NodeJs)) + agent (which is potentially simply a proprietary abstraction wrapping OpenTelemetry)
- Trade-off between solutions: the open standard solution may be a bit more maintenance, but offers the flexibility needed to fully control your telemetry data. The vendor solution takes away the burden of maintaining an agent, but also limits us greatly in the telemetry we produce by default, often causes us to reinstrument our applications if we choose to switch vendors, and usually limits the forwarding of your telemetry data *only* to said vendors service/backend

### 2. Telemetry processing
Once you have telemetry generated, you may want to pre-process it before persisting it to an observability backend(s) for analysis. Several types of processing can occur, a few being:
- Filtering or sampling requests (since it's not always feasible to to store *every* bit of telemetry you generate)
- Striping out sensitive data from telemetry (e.g.: PII)
- Enhancing telemetry data (e.g.: adding fields/data that'll be useful when performing analysis in the backend)
- Batching telemetry before forwarding it to your observability backend(s) (network optimization)

#### Available options
- Open standard solution: OpenTelemetry collector (optional)
- Vendor solution: vendor-specific DSL used to configure a telemetry processing pipeline (optional)
- Trade-off between solutions: while you'd likely need to manage and scale your OpenTelemetry collector (I haven't seen any managed services for the OTel collector as of now), the OTel collector offers many versatile [processors](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor) out-of-the-box and can be extended to use your own custom processing logic. Vendor solutions likely limits the type of processing available and makes the pipeline configuration non-portable (vendor lock-in)

*Disclaimer: I haven't worked with a vendor telemetry processor before*

### 3. Telemetry storage (and analysis) in an observability "backend"
Whether your telemetry data is pre-processed or not, it still needs to be stored somewhere before making use of it. Some low-latency observability systems may persist this data in memory for low-latency querying (expensive but fast), but more commonly than not, telemetry is stored in a persistent database for durable access in the future. Regardless of the storage medium, these "backends" also offer a UI that enables engineers to view their telemetry data at an aggregate and granular (per-request) basis

#### Available options
- Open standard solution: FOSS backends (ideally offering support for OTLP (OpenTelemetry Line Protocol)) such as Zipkin, Jaeger, and Grafana
- Vendor solution: any vendor solutions. Note that as OpenTelemetry becomes more prevalant, more vendors are offering support for OTLP in addition to their proprietary telemetry protocols
- Trade-off between solutions: open standard allows customization and extension to support your use case (if needed). Open source solutions likely require you to self-host or pay for a managed instance. Vendor solutions may be self-hosted, but are more often managed as a SaaS. If a vendor solution does not support features you want, you may be waiting a while (sometimes indefinitely) before seeing these features

*NOTE: when choosing a telemetry backend, a couple of major things to look for are (but not limited to) high availability, low latency (<=1 minute from the time of telemetry generation to it being queryable in the backend is usually acceptable), and the ability to query your telemetry data effectively on both the aggregate and per-request level. With any of these features missing, a backend tool can be near useless*

### Pulling it together
Now that we have an understanding of the telemetry pipeline, we see where opportunities exist to avoid vendor lock-in. We want to shift non-vendor-specific solutions as left as possible -- **if we start off with a vendors proprietary solution for generating telemetry data, we're likely bound to the vendors pipeline for the rest of the telemetry lifecycle.** If maintaining an agent/component for generating telemetry seems too daunting, know that plenty of [reproducible agents](https://github.com/open-telemetry/opentelemetry-lambda#extension-layer-language-support) exist to make this task almost effortless

Whether you use a telemetry processing stage or not is dependent on your needs -- do you need to enrich/mask/transform your telemetry data before storing it? Or perhaps you need to filter and (tail) sample your telemetry to ensure you don't send overwhelming amounts of telemetry to your backend as your system scales ($$$)!

As long as you have the generation and processing stages of the telemetry pipeline understood, I believe you've earned the choice to leverage either an open source or vendor solution as your backend. Having open standard/vendor-agnostic upstream stages in your telemetry pipeline gives you incredible negotiation power and flexbility with less threat of vendor lock-in. Of course, if you choose to change vendors in the future, it will still likely take *some* effort to migrate your existing data to your next vendor. But honestly, historical telemetry data may not be as valuable as you think. If you are looking to take a leap to another vendor, you can always update your OpenTelemetry collector (or agent) config to forward telemetry to multiple vendor solutions, making the migration as smooth as possible - all with minor overhead

## Moving forward
Vendor lock-in is never good. Unfortunately, most vendors will push for it (for their own monetary interest) at any given opportunity. While it shouldn't be this way, it is -- it's a common, current business model in this industry. In my opinion, instead of vendors playing games to trap unknowing customers, vendors should be utilizing their resources to build a solution that is truly superior (in this case: a powerful, reliable, full-featured observability backend.) This sort of no-BS behavior drives competition (and need I mention, innovation?) in the space. The idea of taking an open standard then wrapping it into something proprietary defeats the whole purpose of an open standard and can arguable be considered offensive. Please say "no" to and call out vendors pedaling these shady tactics - it'll give you the upper hand and may also encourage vendors to shift their strategies so everyone wins. Anyways, the point is: before you even consider using a vendors proclaimed "magic bullet" solution, do your homework and have your escape plan ready.

* content
{:toc}
