# Lab2B: Step2

### Average bandwidth graph:

![avg_band_width](/Users/pb/Documents/My-study-notes/pictures/avg_band_width.png)

### Total Stall cycles:

![stall_width](/Users/pb/Documents/My-study-notes/pictures/stall_width-8762881.png)

For both Ifmap cache and filter cache, as the L1/(L1+L2) ratio decreases, the average bandwidth increases. This may imply that a smaller L1 and a larger L2 can enhance the hit frequency in the cache, making more efficient use of L2 to reduce the miss rate.



For the stall cycle, with respect to the Ifmap cache, a smaller L1/(L1+L2) ratio can reduce the stall cycles. As for the filter cache, the stall cycles are minimized when L1 and L2 are of the same size. It may be that in the case of weight stationarity, the ifmap cache needs to frequently fetch new data, but the filter cache does not require frequent data retrieval.