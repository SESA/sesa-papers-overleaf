# Literature notes

## Gernot Heiser: Slow Down or Sleep, that is the Question -- 2011

"compares the effects of using dynamic voltage and frequency scaling and sleep states"

"we find that usage of power-management mechanisms is not clear-cut, and that it is critical to analyses the system as a whole, including the workload, to determine whether using mechanisms such as DVFS will be effective at reducing energy consumption"

"present a trade-off to the OS designer: either run a task at a reduced CPU frequency (consuming less power) but remain active for a longer period of time (ie slow down), or run a task at a high CPU frequency (with higher power draw) and as soon as possible enter a lower-power idle state (an approach known as race-to-halt or sleep)"

"In this paper, we intend to show how DVFS and sleep states can improve energy efficiency for under utilized systems running real-world workloads, such as serving web pages"

"Howerer, the systems that were available 10 years ago are very different from those available today.  Many low-power idle states are now available, and their usage results in significantly reduced power draw.  Furthermore, static power is growing as a proportion of total system power, reducing the impact of DVFS on overall power draw."

"Unfortunately, static power is unaffected by frequency and a longer run time resulting from a lower clock rate increases the energy consumed through static power.  Static power draw is increasing in modern systems and is a mjor factor contributing to the declining effectiveness of DVFS."

"TurboBoost...2.93 GHz model, however, the frequency of one or more cores can be increased up to 3.6 GHz (in steps) depending on power requirements and heat dissipation of the processor"

"while entering C3 causes a core's local L2 cache to be flushed to the shared L3 cache and the powered down.  When entering C6, a core's power supply is completely shut off, reducing leakage as well."  

"Overhead from C state usage varies for a number of reasons. Firstly, in deeper C states, more power-reducing actions are taken, such as cache flushes (allowing caches to be powered down) and voltage/frequency changes.  Flushing caches takes time as cache lines are written to backing stores, and voltage/frequency changes take time as voltage regulators settle and PLLs rlock.  Secondly, workload characteristics determine C state transition rates.  A higher rate of transitions will cause higher overhead.  Thirdly, the workload's memory access pattern will determine how much overhead results from cache flushes.  Workloads which already have a hight cache-miss rate will not be affected as much as workloads with load miss-rate that still rely on the reuse of cached data."

"multi-threaded server workload, which creates idleness due to the sporadic nature of web requests ... these three workloads all allow the CPU's cores to enter idle states during their execution, thus allowing the effectiveness of both DVFS and different C states to be examined."

"... fixed length of time regardless of CPU frequency..."  -- I don't think they are actually ensure that they are considering if the same amount of work was begin done -- later they seem to indicate that they saw no impact to latency and throughputs given under utilized configuration

"Ubuntu Linux (10.10)..."

"connected to their power sources through a power meter. As a result all energy consumption data we report is for the total system"

"We used a custom cpuidle governor to allow us to choose the C state that would be used.  We ran ten iterations of each benchmark, and averaged these results to obtain a final result.  Standard deviation was less than 1% of the mean"

NOT STATED but we assume manual static DVFS setting 

See 5.2 and 5.3 as the core results that relate to us:

## 5.2:  Apache web-server

* single "request rate that results in an under-utilized system"
* 12-28% CPU utilization (depending on frequency and sleep state used)
* sleep state transitions > 1/ms
* "... using C3 and C6 C states caused slightly higher system load.  Despite this, total system energy consumption was minimized at the lowest CPU frequency (1.20 GHz) using the deepest C state (C6).  Additionally, we found that reducing the CPU frequency had not measured impact on either throughput or response latency."
* "Similarly to the MPEG workload, we found that when deeper C states were used, the effectiveness of DVFS at improving energy efficiency was diminished. As C states improve in the future, this will become even more marked"

## 5.3 Spec JBB

... a throughput oriented benchmark.  It stresses CPU and memory hierarchy..."

* utilization depends on the number of warehouses
* 1 warehouse 30% utilization spread across all cores
* as warehouses where increased load increased until contention on other resources became the bottleneck
* 4> more warehouses max 90% CPU utilization
* CPU frequency little effect on utilization but throughput was impacted when CPU frequency was reduced.
* "We found that energy-efficiency was maximized at 2.13 GHz using C6 C state adn 4 warehouses.  ... negligible overhead was observed when C6 was used"


## Comment
This was a very poor experimental evaluation even for USENIX

However, we should note that they fixed sleep state   

## David Brooks: Tradeoffs between Power Management and Tail Latency in WarehouseScale Applications -- 2014

## Hank Hoffman: Racing and Pacing to Idle: Theoretical and Empirical Analysis of Energy Optimization Heuristics -- 2015

## Daniel Wong: μDPM: Dynamic Power Management for the Microsecond Era -- 2019

## Bestavros: Peafowl: in-application CPU scheduling to reduce power consumption of in-memory key-value stores -- 2020


# Analysis Notes

Costs of sleep include latency to enter and exit sleep state and potential slow down post sleep --- hard to simulate the true impact of these on a particular workload and software stack

Cloud server workloads while I/O driven can vary greatly in the the nature of computation required to service a request and the degree to which request processing is OS versus  application bound.

Cloud servers can experience a wide range of utilization -- under utilized when load is load to high utilization when load is high or the work is closed loop in nature -- eg. database snapshotting, video streaming, etc.


reducing structural OS overheads (path length, single protection domain processing, run to completion) can lead to an improvement in net request IPC and thus magnify the ability to slowdown to improve energy consumption with little or no impact on performance.




# Theme / message notes

## Distinguishing aspects of our work 
Cloud service focus -- extends the notion of slowing down to include delaying of request detection using interrupt delaying -- versus only considering power management mechanisms.

We focus on understanding how the OS's structure impacts and interacts with the net performance realized and energy consumed.  
(Gernot work examines the knobs but not the impact of changing the OS)









