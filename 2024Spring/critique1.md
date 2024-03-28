## Critique 1: In-Datacenter Performance Analysis of a Tensor Processing Unit

### \- Short Summary:

The article explain the following topics:

The function of TPU, TPU hardware structure, data flow within TPU, functions of various modules in TPU, Rooflines of TPU, CPU, and GPU, differential improvement approaches of TPU with different NN models, the need for increased focus on MLP and LSTM, and cost performance of TPU, GPU, and CPU.

The conclusions drawn are that TPU performs well in NN inference, reducing latency. It exhibits superior Roofline performance and is more energy-efficient compared to CPU and GPU. If TPU could have a larger memory bandwidth, its performance would be further enhanced. In the comparison of various NN models, there is a future need for increased attention to the computational performance of MLP and LSTM, while CNN's computational contribution is relatively lower.

###  \- 2 strengths + 2 weaknesses

**Strengths:**

The strength of TPU lies in its compact size, ease of connectivity through PCIe, and convenience. It incorporates numerous MAC units, facilitating matrix calculations.

Due to its nature as a dedicated ASIC, it has low power consumption and simple instructions.

**Weaknesses:**

However, TPU connects to the motherboard via PCIe and requires instructions to be sent through the CPU. Its memory bandwidth is relatively small, leading to many models being limited by memory bandwidth.

There are restrictions on the matrix size for its matrix calculations, and it may not necessarily be suitable for matrices of other shapes.

### \- 1 suggested improvement

Whether TPU can operate independently of the CPU by modifying its control section to execute instructions. Using a faster and more efficient connection method to increase memory bandwidth and reduce weight loading time. Whether it can adaptively compute matrix sizes, making it applicable to a wider range of models.