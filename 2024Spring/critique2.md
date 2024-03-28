## Critique 2: 

### \- Short Summary:

Parallelism and data reuse is very important for computation and energy. For a specific DNN workload and hardware accelerator, the achieved utilization and data-reuse directly depend on DNN computation choice and how we map computation across PEs. Dataflow is very important.

Two types of data reuse: multicasting and reduction.

Three types of data flows: weight-stationary, output-stationary, and input-stationary. There are complex dataflow.

Data-Centric Representation define the dataflow of an accelerator design by focusing on how data is mapped and reused across the processing elements (PEs) and time steps.

The dataflow is described using three key directives: Spatial Map, Temporal Map, Data Movement Order, Clusters. 

Data-Centric Representation simplifies the analytical model and enable fast and accurate estimation. Spatial Map, Temporal Map, Data Movement Order affect the logical design of the dataflow and the physical realization in hardware

MAESTRO has five engines: tensor, cluster, reuse, performance analysis, and cost analysis. It can quantitivily analysis how dataflow affect the DNN efficiency.



###  \- 2 strengths + 2 weaknesses

**Strengths:**

The paper describe the dataflow very well. It use very clear graph to explain the Spatial Map, Temporal Map and Data Movement Order.  And emphasize the importance of dataflow.

The idea of data-centric approach is novel and clear. Use data-centric approach is grabbing the main issue and can improve DNN accelorator.

**Weaknesses:**

Maybe the analysis about MAESTRO can be more precise. And it is hard for engineer to understand the inner theory of MAESTRO and make use of it in the industy. 

The analysis is limited to current DNN. Maybe there will be other kind of DNN created in the future and need new dataflow and analysis.

### - 1 suggested improvement

Maybe this MAESTRO can have more choice. maybe have weight on different perspective. Customizable weighting system to analysis the DNN hardware and maybe can have flexible optimization objectives.





