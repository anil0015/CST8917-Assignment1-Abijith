# [Abijith]
**Student Number:** [041194855]  
**Course Code:** CST8917  
**Assignment:** Assignment 1: Serverless Computing - Critical Analysis  
**Date:** February 13, 2026

---

## Part 1: Paper Summary

Hellerstein et al.'s 2019 research paper *"Serverless Computing: One Step Forward, Two Steps Back"* offers a ground-breaking analysis of first-generation Function-as-a-Service (FaaS) systems. The key thesis is that while serverless has taken a huge **"one step forward"** by providing an autoscaling, pay-as-you-go paradigm that abstracts server management, it has also taken **"two steps back"** by disregarding fundamental advances in data-centric and distributed computing. The authors contend that by rigorously separating storage and computing while enforcing statelessness, cloud providers have unintentionally created a "hobbled" platform for complicated, data-intensive applications. The authors note various drawbacks that prohibit first-generation FaaS from serving as a general-purpose innovation platform.

### Key Limitations Identified
The authors note some specific restrictions that prohibit first-generation FaaS from serving as a general-purpose platform for innovation:
* **Execution Time Constraints:** Platforms like as AWS Lambda have severe constraints on how long a function can execute (often 5 to 15 minutes). This prevents long-running processes such as machine learning training, large-scale video encoding, and complex scientific simulations that require hours of continuous processing.
* **Communication & Network Limitations:** Functions in a FaaS context lack "direct addressability." Because functions are ephemeral and their IP addresses are neither exposed or stable, they cannot connect with one another directly over TCP or UDP. As a result, every communication must go through sluggish, expensive storage intermediaries (such as S3 or DynamoDB), incurring a significant latency penalty.
* **The "Data Shipping" Anti-Pattern:**"Shipping code to data" is a fundamental principle in high-performance computing. FaaS, on the other hand, forces the opposite: because functions are isolated from storage, huge datasets must be transferred across the network into the function's environment with each execution. This "data shipping" results in significant I/O overhead and higher prices.
* **Limited Hardware Access:** D FaaS products often limit customers to conventional CPU/RAM slices. There is no native support for specialist hardware, such as GPUs, TPUs, and FPGAs, which are required for modern AI and data-processing tasks.
* **Distributed Computing Challenges:** Stateful workloads are challenging to manage since the global state must be constantly read and written to external databases.

### Proposed Future Directions
To fully realize the cloud's potential, the authors propose:
1.  **Fluid Code and Data Placement:** Enabling the infrastructure to physically combine code and data to reduce latency.
2.  **Long-Running, Addressable Virtual Agents:** Persistent software agents with identifiable identities capable of communicating at raw network rates.
3.  **Heterogeneous Hardware Support:**  Allows serverless functions to define and use specific hardware accelerators.
   
---

## Part 2: Azure Durable Functions Deep Dive

| Topic | Research & Explain |
| :--- | :--- |
| **Orchestration Model** | Durable Functions employs a **Orchestrator** to coordinate **Activity** and **Client** functions. The Orchestrator establishes workflow logic, whereas Activities carry out particular tasks. This responds to the paper's complaint about the difficulties of building sophisticated applications from separate, independent components. |
| **State Management** |It handles the state via **Event Sourcing** and **Checkpointing**. When an orchestrator waits for a job, it saves its work to storage; once completed, it "replays" its code to reconstruct the state. This clearly responds to the paper's claim that FaaS is natively stateless and requires manual external state management. |
| **Execution Timeouts** | Orchestrator functions avoid normal FaaS timeouts by sleeping during waits and resuming using replay. While Activity functions have limitations, the Orchestrator can theoretically run for days or months, hence addressing the authors' "Limited Lifetimes" constraint. |
| **Communication between Functions** |Orchestrators and Activities communicate using an internal **Task Hub** (queues and tables). While this reduces complexity, it still requires a storage intermediate. This improves the *usability* of communication while only partially addressing the *latency* concerns raised by the "Communication Through Slow Storage" complaint. |
| **Parallel Execution (Fan-out/Fan-in)** |The **Fan-out/Fan-in** pattern enables an orchestrator to launch numerous actions concurrently and wait for each to complete before aggregating results. This introduces a native mechanism for coordination, addressing the criticism that distributed computing is hampered by a lack of fine-grained communication. |

---

## Part 3: Critical Evaluation

### Limitations that Remain Unresolved
Despite Azure Durable Function's advancement, two significant criticisms raised in the study remain unresolved:

1.  **High-Latency Communication (Storage Bottleneck):**Durable Functions continues to rely on "Data Shipping" through the Task Hub (Azure Storage). Every communication sent between an orchestrator and an activity results in a write/read action to storage. The authors advocated for point-to-point networking because Durable Functions, which must ensure durability by writing to disk, cannot yet match the "raw network performance" required for high-performance distributed shuffling or real-time coordination.
2.  **Lack of Hardware Heterogeneity:** Azure Durable Functions is largely a CPU-based orchestration engine. It does not natively provide the fluid access to GPUs or specialized accelerators that the authors believe are necessary for modern AI and data-centric innovation. It controls "standard" computing instances, not specialized hardware.

In my opinion, Azure Durable Functions is a **sophisticated workaround** rather than a fundamental architecture shift. 

Hellerstein et al. proposed rethinking the cloud's "physics," in particular, low-latency networking and code-to-data delivery. Durable Functions delivers the *developer experience* of stateful, long-running agents, but it does so on top of the paper's critiqued "hobbled provider infrastructure" (slow storage queues). While it successfully closes the usability and orchestration gaps (the "One Step Forward"), the underlying performance remains "Two Steps Back" in terms of I/O bottlenecks and disaggregated computation. It is an excellent bridge for business logic, but it is not yet the revolutionary platform for data-rich innovation envisioned by the authors.

---

## References
* Hellerstein, J. M., et al. (2019). [Serverless Computing: One Step Forward, Two Steps Back](https://www.cidrdb.org/cidr2019/papers/p119-hellerstein-cidr19.pdf). CIDR.
* Microsoft Learn. (2025). [Durable Functions Overview](https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-overview).
* Microsoft Learn. (2025). [Orchestrator function replay](https://learn.microsoft.com/en-us/azure/azure-functions/durable/durable-functions-orchestrations).

## AI Disclosure Statement
I used AI tools to help generate the table structure for Part 2, for analysis(summary) of the paper and to generate check for grammatical errors in my final analysis. All technical evaluations and the final verdict were written and researched independently.
