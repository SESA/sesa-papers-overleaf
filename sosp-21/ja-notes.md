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

However, we should note that they fixed sleep state as part of their work rather than letting them be managed.  
   

## David Brooks: Tradeoffs between Power Management and Tail Latency in WarehouseScale Applications -- 2014

"9 of these benchmarks often toggle their cores between short bursts of activity and sleep.  In doing so, they stress sleep selection algorithms and can cause talk latency degradation or missed potential for power savings up to 10% on important workloads like web search" 

"We find the largest potential in DVFS which is cognizant of latency/power tradeoffs on a workload-per-workload basis"

"In an ideal energy-proportional system, server power consumption would perfectly track the arrival patterns of requests"
 
"at full load the processors consume 78% of the system power.  Furthermore, just the dynamic range of processor power between fully idle and fully loaded is 67% of the maximum system power"

"a warehouse scale computer has conflicting requirements between aggressive power savings and aggravated request latency (and missing SLA's)"

"What are the sleep patterns of current WSC applications? Are applications' idle periods short enough to be affected by the choice of a particular sleep state?"

"How much does C-state selection influence request latency and system power on the macro level?  In other words, by how much can proper selection improve latency, and what are the maximal power saving from using idle periods?"

"We evaluate the potential benefits of WSC applications from reducing voltage and frequency during memory-bound phases of execution"

"Finally, we identify that the directions holding the largest promise are workload-specialized and ultra-fine-grained DVFS, which warrant further study."

"SandyBridge microarchitecture exposes 5 sleep states:"



state | residency | wakeup-latency
----------------------------------
C0 | (active) | (active)
------------------------
C1 | 1 us | 1 us
------------------------
C3 | 106 us | 80 us
------------------------
C6 | 345 us | 104 us
------------------------
C7 | 345 us | 109 us
------------------------


"The PCU can ignore software requests for a specific C-state, choosing to enter a shallower one, if it estimates that the residency requirement of the deeper state will note be met ... controlled by a proprietary algorithm set by processor vendors. Furthermore the PCU can choose to transition a core between sleep states without waking it up -- a knob not available in software"

"However, selecting the optimal sleep state requires accurate prediction of sleep lengths... predicting too long may cause a premature wakeup, adding the state wakeup latency to the already time-critical interrupt processing."

Figure 6 seems bogus given that there is no discussion of the sleep state algo/policy

Latency-insensitive applications -- with sleep and active periods well longer than 1ms ... the latency effects of deep sleep are irrelevant"

Latency-sensitive IO-heavy applications -- fraction of sleep and bursts of activity with sub-millisecond lengths

Latency-sensitive CPU-heavy applications -- require very minimal power management


"Linux kernel's support for frequency scaling is based on different premise.  Power-saving governors (ondemand, powersave) use OS reported processor utilization as a proxy for the system's latency sensitivity and scale frequency down when processor utilization is low. "  this is not appropriate for WSC workloads -- services can be very sensitive to latency regardless of how high processor utilization is"


## Hank Hoffman: Racing and Pacing to Idle: Theoretical and Empirical Analysis of Energy Optimization Heuristics -- 2015

"... These studies suggest there is a need to revisit earlier results and better understand when the race-to-idle strategy is appropriate in practice and how much energy can be saved using more sophisticated resource allocation schemes"

"race-to-idle"
"pace-to-idle"
"no-idle"
"optimal"

"Our empirical results confirm that the pace-to-idle strategy always beats race-to-idle by as much as 20% on x86 and 3X on ARM."

"CMOS voltage and frequency scaling for combinational logic (P is proportional to v squared times f) does not apply to traditional SRAM circuits, as reducing the voltage in these circuits leads to unacceptable error rates. As SRAM circuits are used to implement cache, this means significant portions of the processor do not obey the cube-root law. "

no-idle - finish work exactly on time without idling 
Pace-to-idle - use best energy efficient configuration then idle

Theoretical framework does not account for latencies/penalties associated with sleep states.

No I/O driven work in which inter-arrival gaps induce spreading out idleness and thus wake penalties have more complex dynamics on latency implications

Computational kernels no OS or significant IO

## Daniel Wong: μDPM: Dynamic Power Management for the Microsecond Era -- 2019

"Unfortunately, the effectiveness of DVFS is diminishing with improved technology scaling as the operating voltage approaches the transistor threshold voltage" -- not sure that this is really true

"While frequency scaling only reduces dynamic power, static power is equally important. To reduce static power, sleep states are designed to save power during idle periods." 

"For example, DVFS-based techniques and sleep-based techniques have been proposed to slow down processing, or delay processing, so that requests finish just-in-time before the target tail latency."

"we find that existing Dynamic Power Management schemes break down -- specifically, DVFS-based schemes cannot find enough opportunities to slow down, and sleep-based schemes cannot enter a deep enough sleep state to be effective."

"The key insight driving uDPM is that by carefully coordinating DVFS, sleep and request delaying, we can achieve energy savings where all others fail.  "

Uses a hardware system and prototype to calibrate a simulator to develop a dvfs, sleep state and batching control policy to minimize energy for a tail latency target.  Notion of delaying processing seems to come from an 2003 IBM USENIX paper on the use of batching to reduce energy consumption of webservers -- defines the notion of a latency slack that can be exploited via batching to bunch up idle time.

## Bestavros: Peafowl: in-application CPU scheduling to reduce power consumption of in-memory key-value stores -- 2020


# Analysis Notes

Costs of sleep include latency to enter and exit sleep state and potential slow down post sleep --- hard to simulate the true impact of these on a particular workload and software stack

Cloud server workloads while I/O driven can vary greatly in the the nature of computation required to service a request and the degree to which request processing is OS versus  application bound.

Cloud servers can experience a wide range of utilization -- under utilized when load is load to high utilization when load is high or the work is closed loop in nature -- eg. database snapshotting, video streaming, etc.


reducing structural OS overheads (path length, single protection domain processing, run to completion) can lead to an improvement in net request IPC and thus magnify the ability to slowdown to improve energy consumption with little or no impact on performance.

Is our hardware still in commercial data center's such as google?



# Theme / message notes

## Distinguishing aspects of our work 
Cloud service focus -- extends the notion of slowing down to include delaying of request detection using interrupt delaying -- versus only considering power management mechanisms.

OS focused analysis: We focus on understanding how the OS's structure impacts and interacts with the net performance realized and energy consumed.  
(Gernot work examines the knobs but not the impact of changing the OS)

General model that frames software behavior that can be adapt as hardware tradeoffs change 

We use existing interrupt delay feature to delay packet detection when using interrupts.

Exhaustively document the behaviour of real software

Closed loop and open loop model 

# Intro

Network driven services are the hallmark of Cloud Computing.  Achieving the required performance while consuming the least amount of energy is a fundamental goal for a provider.   In this paper, we carefully break down and study network driven processing and demonstrate the positive impacts and interactions that the OS can have when slowing down the CPU and delaying interrupt delivery.  Often one can significantly reduce energy consumption with very little impact on performance.  For example even under higher loads it is possible to serve memcached request at half the energy cost 
Memcached
Best Performance EbbRT      200K QPS  65.9us        @ 1747
Best Energy      EbbRT      200K QPS  444 us        @ 935
Good1            EbbRT      200K QPS  67.7us (1.02) @ 1042 (
Good2            EbbRT      200K QPS  104us         @ 959

EbbRT      200K QPS  
EbbRT-POLL 200K QPS  

While many have studied power management mechanisms and control from an architectural or application perspective we approach the subject from and OS research vantage point.  Our goal is not the construction of a particular policy or mechanism rather it is to quantify and explain how and why slowing down processing can be beneficial.  Our work extends and builds upon that of Sueur et al. and Mootaz et al.  Sueur et al. noted that to guide an OS designer it is critical to analyses the system as a whole, including the workload, to determine whether using mechanisms such as DVFS [Dynamic Voltage Frequency Scaling to slow processing down] will be effective at reducing energy consumption. While Mootaz et al observed that delaying request processing can result in batching effects that permit power savings while maintaining tail latency requirements for web serving.                                                                                                                                  


More recently, Chou et al. proposed a dynamic control policy in the context of micro-services that advocates for a mixing both the slowing down of the CPU and delaying processing in a latency sensitive manner.   The authors focus is one of policy development with little insight into the impact that the OS's design and implementation has.    

To our knowledge we are the first to conduct a detailed experimental study that evaluates how the OS structure and choices interact with slowing cores down using hardware power management (DVFS) and delaying interrupt delivery using NIC settings (IDR).  We experimentally gather data for a very large combination of DVFS and IDR setting across four workloads broken down into two categories open loop and closed loop. Each category consists of one workload that is OS centric and the other that is application centric. Further to tease apart the OS impact  in addition to evaluating a general purpose OS (Linux) we use a Library OS, that we can more easily modify to explore a broader range of systemic OS choices.   

Breaking down request processing into phases we construct a mathematical model that allows us to capture and explain the OS's behavior and its impact on both performance and energy.  The model is both intuitive and consistent with our experimental observations.  

Figure~/ref{timeline} illustrates the typical processing timeline for a network service.  






