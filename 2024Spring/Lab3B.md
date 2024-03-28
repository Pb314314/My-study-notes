(a) Which parallelization strategies result in the highest/lowest speedup compared to a single GPU? C
omment on the reason why these strategies result in the highest/lowest speedup. 

Tensor parallel provides the best speedup. Pipeline parallel provides the worst speedup. 

Tensor parallel efficiently utilizes multiple GPUs, achieving high efficiency with minimal communication costs. Conversely, pipeline parallelism can result in pipeline bubbles that waste cycles, and balancing the computational load across GPUs is challenging. Coordination among GPUs is comparatively difficult. If the computation of operators across different GPUs can be balanced, it can reduce the overall duration. At the same time, communication overhead occupies time. Additionally, warm-up and cool-down phases also contribute to time overhead.



(b) You may find that one of the parallelization strategies results in a slowdown. Is there any reason to 
adopt this strategy over a single GPU strategy even with a potential slowdown? 

Pipeline parallelism can lead to a slowdown. However, compared to a single GPU, pipeline parallelism also has some advantages. For very large models that are difficult to fit on a single GPU, pipeline parallelism enables training across multiple GPUs, which is more space-efficient. At the same time, pipeline parallelism can leverage more GPU resources for computation by distributing operators across different GPUs and improve overall throughput. Moreover, if the communication time is sufficiently short, there are enough GPUs available, and the operators are allocated reasonably, it can also yield good results.



(c) Assume that in an ideal system, the latency of any communication is 0. What is the speedup of mo
del parallelism/pipeline parallelism over a single GPU case on this ideal system? 

If communication time is 0, and there are infinite GPU. 

For model parallelism, every GPU can only train one data and aggregate the gradient. Speed up can be raised to number of data train on single GPU.

For pipeline parallelism, every GPU can only run one operator. Speed up can be raised to number of operators in the model.

And there can be overhead of aggregation and synchronization.



(d) Observe the traces of two GPU workers for data-parallel strategy. Did they execute the same sequences of operators? If not, what is the difference between these two workers, and what makes that difference? 

For data parallelism, different GPUs receive different data, but all GPUs have the same model parameters and run the completed model. They are trained with different data, then the results of the training are aggregated and updated, and a new model is distributed. Then, new training is conducted with the new model.

(e) Observe the traces of two GPU workers for pipeline-parallel strategy. Did they execute the same sequences of operators? If not, what is the difference between these two workers, and what makes th at difference? 

For pipeline parallelism, different GPUs run different parts of the operators, and their operator sequences are different. They run operators in different positions using the same data. They obtain new operator parameters through communication and then train their respective parts of the operator.

(f) How does activation recomputation help when training a larger model? 

Activation recomputation reduces the memory needed to store activations by recomputing them on-the-fly during the backward pass, rather than storing them during the forward pass. This increases the computational time of training since activations need to be recalculated, but it reduces the memory footprint. Each GPU requires more computation to complete its training due to the additional recomputation steps.

(g) Which strategy uses the least amount of memory? 

Tensor Parallel + AR uses the least amount of memory.

Answer the following questions for part B-4: 
(e) What parallelism strategy and model size (number of layers) could you fit on a 2 GPU setup? 

strategy: Tensor Parallel + AR

number of layers: 16

