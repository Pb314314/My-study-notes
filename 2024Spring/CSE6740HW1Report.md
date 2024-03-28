# CSE6740 HW1 Report

1. Within the K-medoids framework, you have several choices for detailed implementation.
Explain how you designed and implemented details of your K-medoids algorithm, including (but not limited to) how you chose representatives of each cluster, what distance measures you tried and chose one, or when you stopped iteration.

I randomly select the indices and use indices to choose the k nodes as centers. Then I assign the nodes to its nearest center. Then  I will find the optimal node in each group which minimize the sum of all distances. I choosed to find the I try to minimize the distance of all the point to the center and choose the best center. I use the Euclidean Distance. The stop condition is when the  clusters' nodes not change, I will stop the iteration.

2. Attach a picture of your own. We recommend size of 320 × 240 or smaller. 

![2e29763d4a45916304ebb133a9186668 (1)](/Users/pb/Documents/My-study-notes/2024Spring/pictures/2e29763d4a45916304ebb133a9186668 (1).jpg)

3. Run your K-medoids implementation with the picture you chose above, with several different
    K. (e.g, small values like 2 or 3, large values like 16 or 32) What did you observe with different K? How long does it take to converge for each K?

  When K = 5:

  For k-means: 0.19394516944885254 s

  For k-medoids :0.16101765632629395 s

  <img src="/Users/pb/Documents/My-study-notes/2024Spring/pictures/image-20240206025325128.png" alt="image-20240206025325128" style="zoom:33%;" />

​	When K = 30:

​	For k-means: 5.354931354522705 s

​	For k-medoids :0.932854175567627 s

As K from 3 to 30, the convergent time get bigger.

​		<img src="/Users/pb/Documents/My-study-notes/2024Spring/pictures/image-20240206030022060.png" alt="image-20240206030022060" style="zoom:50%;" />

4. Run your K-medoids implementation with different initial centroids/representatives. Does
it affect final result? Do you see same or different result for each trial with different initial assignments? (We usually randomize initial location of centroids in general. To answer this question, an intentional poor assignment may be useful.)

Running a K-medoids implementation with different initial centroids can affect the final result, as the initial choice of medoids impacts the algorithm's convergence and the quality of the clusters. I see different result.

5. Repeat question 3 and 4 with K-means. Do you see significant difference between K-medoids
and K-means, in terms of output quality, robustness, or running time?

The K-means time already in answer 3.

The initial choose of center will also affect K-means and will affect the final result. I see different result when assign different initial points.

The K-medoids can converge much faster than K-means.

K-medoids can be more robust to spetial points.

They have overall similar picture output.



