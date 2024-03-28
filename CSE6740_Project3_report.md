## Homework3 report

I tried various low ranks = [1, 3, 5, 7, 9, 11] and analyzed the data. When the Low Rank is 11 and regularization is false, the Test RMSE is the smallest. Generally speaking, within a certain range, the larger the Low Rank, the more information it can express, and the larger the value of Test RMSE.

![image-20240326031536151](/Users/pb/Documents/My-study-notes/pictures/image-20240326031536151.png)

This is my experiment with different hyperparameters, and I have sorted the test RMSE in ascending order. This is a relatively rough experiment, but I still learned about the impact of regularization, iteration, and learning rate on training. Surprisingly, the lowest test RMSE was obtained when**regularization was false, low rank was 3, and max iteration was 400, learning rate = 0.0005** which was different from my initial prediction. However, at the same time, the result of each experiment can vary due to different random initializations, leading to different training effects. Therefore, if a more accurate analysis is needed, a better initialization strategy is required.

![image-20240326024659317](/Users/pb/Documents/My-study-notes/pictures/image-20240326024659317.png)

This is my print data.

![image-20240326030202659](/Users/pb/Documents/My-study-notes/pictures/image-20240326030202659.png)