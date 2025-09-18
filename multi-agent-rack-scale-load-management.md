### Title: Malcolm: Multi-agent Learning for Cooperative Load Management at Rack Scale
#### Year: 2022
#### Authors: University of Waterloo (Ali Hossein Abbasi Abyaneh, Maizi Liao, Seyed Majid Zahedi)

goal:  
Design a **fully decentralized, heterogeneity-aware** rack-scale load balancer (“Malcolm”) that achieves microsecond-scale tail latency and very high throughput by having servers collaboratively schedule and migrate work using multi-agent learning framed as a cooperative stochastic game. :contentReference[oaicite:0]{index=0}

state-of-the-art:  
- **Server-scale schedulers** (e.g., ZygOS, Shinjuku) deliver µs tail latency but don’t scale to rack level.  
- **Centralized rack schedulers** (e.g., RackSched) push policy into programmable ToR switches and approximate cFCFS via power-of-d choices.  
- **Distributed client/dispatcher approaches** exist (e.g., PoD, JSQ, JBT, Sparrow), but they’re tuned for ms-scale jobs and/or assume homogeneity. :contentReference[oaicite:1]{index=1}

problems with state of the art:  
- **Programmable switch dependency** and limited compute/memory at the switch constrain policies; offloading can degrade network throughput.  
- **PoD approximations fail under heterogeneity** (fast/slow servers probed equally → imbalance, worse tails).  
- **Centralization + network RTTs** hurt tail latency when service times are only tens of µs.  
- **Client-based scheduling** causes stale load views and herd/race behaviors; distributed variants often miss µs latency/throughput targets. :contentReference[oaicite:2]{index=2}

experimental methodology (how are they testing their method):  
- **Testbed deployment** on a heterogeneous 7-server cluster; threads pinned; user-space networking.  
- **Synthetic open-loop load generation** to accurately measure tails at high load.  
- **Compare 99th-percentile and median latency vs. load** under multiple service-time distributions, in **homogeneous** and **heterogeneous** rack configs.  
- **Baselines**: RAND, Po2, JSQ, JBT; plus **simulation** comparison against RackSched-like centralized Po2+PS and ideal cFCFS. :contentReference[oaicite:3]{index=3}

what tools did they use?  
- **eRPC** user-space RPC; NICs in InfiniBand mode; user-space networking stacks (e.g., DPDK/RDMA supported by eRPC).  
- **Custom C++ implementation** of the decentralized actor–critic with AVX2 and closed-form gradients for microsecond-scale updates.  
- **Lock-free centralized queue** per node; FCFS by default (compatible with ZygOS/Shinjuku). :contentReference[oaicite:4]{index=4}

what benchmarks did they use?  
- **Service-time distributions**: Exp(75); Bimodal(80:50, 20:250/500); HyperExp-1(50:50, 50:500); HyperExp-2(75:50, 20:500, 5:5000).  
- **Cluster configs**: (1) homogeneous (Type I servers, multiple nodes/threads) and (2) heterogeneous (mix of node sizes across server types).  
- **Tail/median latency vs. load (KTPS)** curves; **RackSched-style** simulations on HyperExp-1 for reference. :contentReference[oaicite:5]{index=5}

is their test reproducible?  
- **Implementation details and configuration** (nodes/threads, NICs, heartbeat period, minibatch sizes) are described; results rely on commodity hardware and open-loop load gen.  
- The **Malcolm code is open-source** (GitHub link provided), aiding reproduction; exact vendor hardware/NICs may vary but are standard datacenter gear. :contentReference[oaicite:6]{index=6}

what was their proposed method?  
- Model inter-node scheduling as a **Markov potential game**; aim for **(parametric) Nash equilibrium** policies.  
- **Decentralized policy optimization**: each node runs an actor–critic loop; periodic **heartbeat** exchanges share loads & migration/steal counts; occasional **consensus** step aligns value functions.  
- **Heterogeneity-aware load metric** (queue length weighted by inverse service rate) guides migration/work-steal decisions.  
- **Probabilistic migration & stealing policies** updated every heartbeat; decisions take **tens of nanoseconds** via CDF sampling. :contentReference[oaicite:7]{index=7}

what were the results of the paper?  
- **Homogeneous racks**: Malcolm matches the best baseline, sustaining **~93% utilization** with low 99th-percentile latency across distributions.  
- **Heterogeneous racks**: Malcolm **reduces tail latency by up to 4×** at lower loads and delivers **up to 60% more throughput** at the same tail-latency target vs. best alternative; Po2/JSQ/JBT degrade due to herd behavior and imbalance.  
- **Against RackSched (simulated)**: Malcolm achieves competitive tail latency **without** specialized switches and avoids centralization penalties. :contentReference[oaicite:8]{index=8}

extra notes?  
- **Instantaneous (not just average) load balancing** is key for µs tails; design targets minimizing temporal imbalance.  
- **Heartbeat period (default 100 µs)** and **minibatch size (default 20)** expose tunables; learning remains stable with **infrequent** comms (tolerant to staleness).  
- **Actor basis includes rank-aware features** (e.g., log rank of peer loads); critic uses polynomial and cross terms; **closed-form gradients** enable few-µs updates.  
- Shows **work-stealing + migration** complement each other; approach is **orthogonal** to intra-node schedulers (FCFS/PS). :contentReference[oaicite:9]{index=9}
