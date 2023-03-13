# 这个文档是对于Pb做的Leetcode 题目的记录:happy:

>  **目前使用的语言是**C++

#  template

## 题目类型

### 题号:日期:

> ***网址：***
>
> **难度：**
>
> **思想概括：**
>
> **数据结构和算法：**

#### ==代码实现== :happy:

```c++
int main(){
  return 0;
}
```

#### 知识点整理:up:

* ....
* ....

#### 难点回顾:sagittarius:

```text


```







# 开始： :happy:

___

___





## 优先队列：

### 题号: 1792 最大平均通过率 优先队列（2023/2/21）

> ***网址： “[1792. 最大平均通过率 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-average-pass-ratio/)***
>
> **难度：中等**
>
> 思想概括：我想到了将学生一个个放进班级里，没有想到使用大根堆来减小寻找最大值。
>
> 数据结构和算法：最大堆（优先队列）。

#### ==代码实现== :happy:

```c++
class Solution {
public:
struct banji{
    double addition;
    double rate;
    double pass;
    int all_student;
    banji(int pass,int all_student){
        this->pass = (double)pass;
        this->all_student = all_student;
        this->rate = (double)pass/all_student;//两个int做除法可以将被除数转换为double，就能得到double的结果
        this->addition = ((double)pass+1.0)/(all_student+1.0) -rate;
    }
    friend bool operator<(banji A, banji B){ //作为函数内部的重载函数，this指针会作为parameter进入来索引当前实例
            return A.addition < B.addition;//写friend和不写friend的区别是什么？？？
        }//大顶堆要重载小于号 但是为什么？？
    /*
    bool operator<(const banji &B){ //不知道为什么这样写不行？？？
            return this->addition < B.addition;
        }*/
};
    priority_queue<banji> que;//如果要是写小顶堆怎么写？？？
    //priority_queue<banji, vector<banji>, greater<banji>> que;//最后这个greater<banji>是这样写吗？？
    double maxAverageRatio(vector<vector<int>>& classes, int extraStudents) {  
    int m = classes.size();
    for(int i=0;i<m;i++){
        banji current(classes[i][0],classes[i][1]); 
        //cout<< current.addition <<endl;
        que.push(current);
    }
    for(int i=0;i<extraStudents;i++){
        banji max_one = que.top();//使用priority_queue来提高找到最优值的速度
        //cout<< max_one.rate <<endl;
        que.pop();
        banji new_current(max_one.pass+1.0,max_one.all_student+1.0); 
        que.push(new_current);
    }
    double result = 0.0;
    while(!que.empty()){
        result+=que.top().rate;
        que.pop();
    }
    return result/m;
}
};
```

```c++
priority_queue<int> q;//默认是从大到小

priority_queue<int, vector<int> ,less<int> >q;//从大到小排序 

priority_queue<int, vector<int>, greater<int> >q;//从小到大排序
```



#### 知识点整理:up:

* 大根堆（当要找到多个元素的最大或者最小值的时候，使用大根堆能减少排序或者对比的时间）
* 结构体作为元素放入大根堆的自定义比较函数
* 结构体内部函数重载（重载<,>来作为大根堆排序的要求）
* this指针使用（作为非静态函数的形式参数，静态函数不需要this指针）

#### 难点回顾:sagittarius:

```text
这题主要是写有点难写。要记住使用最大堆！！不要傻傻的循环一个个比。

```



### 题号: 剑指Offer 54， 二叉搜索树的第k大节点 日期:2023/3/1

> ***网址：***[剑指 Offer 54. 二叉搜索树的第k大节点 - 力扣（LeetCode）](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)
>
> **难度：**简单
>
> **思想概括：**使用优先队列进行第k大的数值分析（之前有做过类似的）。需要注意的是，如果再定义优先队列的时候加上greater<int>或者less<int>，需要在前面
>
> **数据结构和算法：**优先队列（谔谔，整理的时候发现，因为是二叉搜索树，右侧的值一定比左侧大，所以其实根本不需要优先队列，直接使用右根左的遍历方法，遍历到第k个节点直接return该数值就可以了）

#### ==代码实现== :happy:

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    priority_queue<int, vector<int>,greater<int>> mini_heap;//greater指的是升序排序，最先出来的是最小值！！
    //优先队列中的vector<int>定义中的int可以换成自定义的类。
    void dfs(TreeNode* root,int k){
        if(root->right) dfs(root->right,k);
        if(mini_heap.size()<k){
            //cout<< "insert"<<root->val <<endl;
            mini_heap.push(root->val);
        }
        else{
            return;//因为是二叉搜索树，本身是有序的，所以采用向右侧遍历，填满que之后就直接return，不用再遍历剩余的
            //这道题其实可以直接使用vector，装满k个 直接return随后一个值就行了。
            //if(root->val > mini_heap.top()){
            //cout<< "pop"<<mini_heap.top()<< "insert"<<root->val<<endl;
            //mini_heap.pop();
            //mini_heap.push(root->val);
        }
        }
        if(root->left) dfs(root->left,k);
    }
    int kthLargest(TreeNode* root, int k) {
    //使用最小堆 当size小于k的时候，往里面加元素。当size等于k之后，如果当前元素比最小值大，就往里面push 再pop
        if(!root) return 0;
        dfs(root,k);
        return mini_heap.top();
    }
};
```

#### 知识点整理:up:

* 要理解优先队列中的比较函数的含义。greater指的是升序排序，所以先出来的是最小值，是小顶堆。
* 使用小顶堆来记住大的值的信息，这种题遇到过一次，这道题加入了树的遍历，我选择了向右侧遍历。
* 对于优先队列定义时的greater理解：升序排列，小的先出来，所以是小顶堆

```c++
priority_queue<class,vector<class>,greater<class>> q_mini;//小顶堆
priority_queue<class,vector<class>,less<class>> q_max;//大顶堆
```



#### 难点回顾:sagittarius:

```text
这道题如果不是二叉搜索树，是无序的时候，需要使用优先队列进行复杂度减少。但是由于是二叉搜索树，其实根本不需要优先队列就可以简单
找到答案。
```





_____

## 动态规划

### 题号:  剑指Offer 47 礼物的最大价值 DP（2023/2/21）

> ***网址： [剑指 Offer 47. 礼物的最大价值 - 力扣（LeetCode）](https://leetcode.cn/problems/li-wu-de-zui-da-jie-zhi-lcof/?envType=study-plan&id=lcof&plan=lcof&plan_progress=jnl46lm)***
>
> **难度：** 中等
>
> 思想概括：我写的dfs，能完成任务，但是算的太慢了，超出时间了。官方的方法通过循环，直接找到了推广的巧妙方法。我感觉比较难想。记录一下，代码的写法上没有什么难点，就是有点难想。
>
> 数据结构和算法：DP动态规划（不是递归中的DP）。

#### ==代码实现== :happy:

```c++
class Solution {
public:
/*
	//这是我一开始写的dfs递归找，画出递归的路线会发现没有利用好两边算的快，从两边扩散到中间的性质。
    int m;
    int n;
    unordered_map<int,int> DP;
    void dfs(vector<vector<int>>& grid, int x, int y,  int pre_num){
        if(x<0 || x>=m||y<0||y>=n) return;
        if(DP.count(x*y)){
            DP[x*n+y] = max(DP[x*n+y],pre_num + grid[x][y]);
        }
        else{
            DP[x*n+y] = pre_num + grid[x][y];
        }
        dfs(grid,x,y+1, DP[x*n+y]);
        dfs(grid,x+1,y, DP[x*n+y]);
    }
    int maxValue(vector<vector<int>>& grid) {
        m = grid.size();
        n = grid[].size();
        if(n ==1 && m==1){
            return grid[0][0];
        }
        int result;
        DP[0] = grid[0][0];
        dfs(grid,0,1,DP[0]);//往右找
        dfs(grid,1,0,DP[0]);//往下找
        return DP[(m-1)*n+(n-1)];
    }*/
    int maxValue(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){//这个是从左边一列一列往右边扩散。
                if(i==0&&j==0) continue;
                if(i == 0){
                    grid[i][j] = grid[i][j]+grid[i][j-1];//左边和上面的最大值
                }
                else if(j == 0){
                    grid[i][j] = grid[i][j]+grid[i-1][j];//左边和上面的最大值
                }
                else{
                    grid[i][j] = grid[i][j]+max(grid[i][j-1],grid[i-1][j]);//左边和上面的最大值
                }
            }   
        }
        return grid[m-1][n-1];
    }
};
```

#### 知识点整理:up:

* 这道题是DP的题目，中等题。但是我感觉算法还是有一点难想的。因为我直觉上就想到了dfs，根本没有往迭代的方向考虑。
* 每一个点只能向右走或者向左走。他思考的角度是一个到达的点，只能是通过左侧往右，或者上侧往下到达的，这样就可以通过比较算出。
* 同时，直接修改原本矩阵，减少了额外空间的使用。

#### 难点回顾:sagittarius:

```text
想法蛮难的。需要经验。。
```

### 题号: 剑指Offer46 把数字翻译成字符（2023/2/23）

> ***网址：***[剑指 Offer 46. 把数字翻译成字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)
>
> **思想概括：**这道题不是很难。是比较典型的后项与前向有关的做法。根据前项得到的数据来计算后项，算是DP算法的使用。
>
> **数据结构和算法：**这道题使用了一个以前没有用过的函数：to_string（用于将数字转化成string），使用的目的数字很难得到最高位的数字，但是string可以轻松得到每一位的数字，所以将int转化为string。为了将得到的char转化成数字，建立了map进行对应。别的方面就是后向与前向的关系。总体来说是不难的一道中等题。

#### ==代码实现== :happy:

```c++
class Solution {
public:
    char word[11] = "0123456789";
    unordered_map<char ,int> char_map;
    int translateNum(int num) {
        string s = to_string(num);
        int length = s.length();
        if(length==1) return 1;
        for(int i = 0;i<10;i++){
            char_map[word[i]] = i;
        }
        int matrix[length];//建一个矩阵，矩阵的大小是总共的位数
        matrix[0] = 1;
        if((char_map[s[0]]*10+char_map[s[1]])<=25 && (char_map[s[0]]*10+char_map[s[1]])>=10){
            matrix[1] = 2;
        }
        else{
            matrix[1] = 1;
        }
        for(int i = 2;i<length;i++){
            if((char_map[s[i-1]]*10+char_map[s[i]])<=25 && (char_map[s[i-1]]*10+char_map[s[i]])>=10){
                matrix[i] = matrix[i-1]+matrix[i-2];
            }
            else{
                matrix[i] = matrix[i-1];
            }
        }
        return matrix[length-1];
    }
};
```

#### 知识点整理:up:

* 使用to_string将int转化为string，这样能得到数字的最高位
* 使用DP，利用matrix记录之前的结果，属于是DP在迭代中的应用，和之前做的从剑指Offer第47题有点相似。

#### 难点回顾:sagittarius:

```text
使用to_string将int转化为string
使用DP，迭代。
```









___



## 单调栈

### 题号: 739 每日温度（2023/2/21）

> ***网址： [1792. 最大平均通过率 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-average-pass-ratio/)***
>
> **难度**： 中等
>
> **思想概括：**使用单调栈，来找到每一个元素后面最近的大于或小于的元素。
>
> **数据结构和算法:**这个单调栈的问题比较直白，之前还做过一道很抽象的单独栈。单调栈画一画就出来了。以找后面更大的元素为例：如果后面的元素比当前栈顶的元素要小，就压入栈。要是比当前的元素要大，就对栈中的元素出栈。

#### ==代码实现== :happy:

```c++
class Solution {   
public:
    vector<int> dailyTemperatures(vector<int>& temperatures){
        int m = temperatures.size();
        stack<pair<int,int>> monotonic_stack;
        vector<int> result(m);
        if(m ==0) return result;
        for(int i=0;i<m;i++){
            pair<int,int> current(temperatures[i],i);
            if(monotonic_stack.empty()){
                monotonic_stack.push(current);
                continue;
            }
            else if(current.first < monotonic_stack.top().first){
                //不能把之前的pop出来
                monotonic_stack.push(current);
                continue;
            }
            else{
                while((!monotonic_stack.empty())&&current.first > monotonic_stack.top().first){
                    result[monotonic_stack.top().second] = i - monotonic_stack.top().second;
                    monotonic_stack.pop();
                }
                //现在要么empty()要么比他大
                monotonic_stack.push(current);
            }
        }
        if(!monotonic_stack.empty()){
            for(int i=0;i<monotonic_stack.size();i++){
                result[monotonic_stack.top().second] = 0;
                monotonic_stack.pop();
            }
        }
        return result;
    }
};
```

#### 知识点整理:up:

* 单调栈的题目。这道题不难，第一次也做出来了（之前有接触了解到单调栈）。但是没有记录过就纪录一下关于单调栈的题目。



#### 难点回顾:sagittarius:

```text
画图，利用栈的记忆性来找到每一个元素后面的更大或者更小的元素。
```

## 双指针

### 题号: 剑指Offer 48. 最长不含重复字符得子字符串（2023/2/23）

> ***网址：***[剑指 Offer 48. 最长不含重复字符的子字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)
>
> **难度：**中等
>
> **思想概括：**这道题目，我之前做过，所以这一次使用两个方法来完成。分别是硬做和巧做。题目还是蛮有意思的，尤其是使用第二种做法，用右侧进来的字母，如果有相同的字母就将左侧字母进行移动，这个想法十分巧妙。
>
> **数据结构和算法：** 左右指针，优先队列（其实可以不用优先队列）

#### ==代码实现== :happy:

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        /*用蠢的方法做一下
        int length =s.length();
        if(length ==1)return 1;
        int result = 0;
        for(int i=0;i<length;i++){
            char current = s[i];
            unordered_set<char> word_count;
            int number =0;
            for(int j =i;j<length;j++){
                if(!word_count.count(s[j])){
                    word_count.insert(s[j]);
                    number++;
                    result = max(result,number);
                    continue;
                }
                else{
                    //result = max(result,number);
                    break;
                }
            }
        }
        return result;
        //没想到硬写竟然过了....
        */
        //尝试用好一点的写法,这种想法蛮有意思的，右边来一个加一个。
        int left =0;
        int right = 0;
        int length = s.length();
        if(length ==1)return 1;
        unordered_set<char> word_count;
        priority_queue<int> que;
        int result = 0;
        while(left<length && right<length && left<=right){
            //cout<< left<< ' '<<right <<endl;
            if(word_count.count(s[right])){//找到重复的了
                que.push(result);
                while(word_count.count(s[right])){
                    word_count.erase(s[left]);
                    result--;
                    left++;
                }
                word_count.insert(s[right]);
            }
            else{//没有重复
                word_count.insert(s[right]);
            }
            right++;
            result++;
        }
        que.push(result);
        return que.top();   
    }
};
```

#### 知识点整理:up:

* 第二种写法，利用了重做往右查找的单调性，可以直接将左侧的字母去掉来使右侧字符进入，这样始终保证了子字符串是最大的长度。

#### 难点回顾:sagittarius:

```text
第一种想法很简单，但是用时很长，第二种方法较为难想，但写起来更容易。
```

### 题号: 剑指 Offer 21 调整数组顺序使奇数位于偶数前面 日期:2023/2/27

> ***网址：***[剑指 Offer 21. 调整数组顺序使奇数位于偶数前面 - 力扣（LeetCode）](https://leetcode.cn/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)
>
> **难度：**简单
>
> **思想概括：** 题目很简单，但是我没有想到双指针的算法！！ 在这种前后满足一些关系的题目中，使用双指针可以达到O(n)的复杂度，不用新建新的数据结构进行实例的储存。
>
> **数据结构和算法：**双指针，链表。

#### ==代码实现== :happy:

```c++
class Solution {
public:
    vector<int> exchange(vector<int>& nums) {
        /*
        //这个是我自己想的方法，太傻逼了
        int l = nums.size();
        vector<int> result;
        vector<int> oushu;
        for(int i=0;i<l;i++){
            if(nums[i] % 2 ==1){
                result.push_back(nums[i]);
            }
            else{oushu.push_back(nums[i]);}
        }
        for(int i=0;i<oushu.size();i++){
            result.push_back(oushu[i]);
        }
        return result;
        */
        //好一点的方法是使用双指针，比较好！
        int left = 0;
        int right = nums.size()-1;//要注意！！是size-1不然后访问没有定义的空间。
        while(left<=right){
            if(nums[left]%2 == 0){
                //如果左边找到一个偶数寻找右侧下一个奇数
                while(nums[right]%2 == 0 && right>left){
                    right--;
                }
                if(left<right){
                    int temp = nums[right];
                    nums[right] = nums[left];
                    nums[left] = temp;
                }
                else{
                    return nums;
                }
            }
            else{
                left++;
            }
        }
        return nums;
    }
};
```

#### 知识点整理:up:

* 使用双指针算法来解决前后数据满足不同要求的题目，这样可以很快地达到O(n)的复杂度。

#### 难点回顾:sagittarius:

```text
题目一点也不难，就是要想到双指针算法，这样时间复杂度是O(n)，也没有使用额外的vector空间。
```







___

## 树

### 题号: 翻转二叉树（简单）(2023/2/24)

> ***网址：[226. 翻转二叉树 - 力扣（LeetCode）](https://leetcode.cn/problems/invert-binary-tree/)***
>
> **难度：** 简单
>
> **思想概括：**关于树的翻转。这道题蛮有意思的，感觉不难，我第一遍思路就对了，但是感觉有点绕。所以记录一下。而且是简单题，所以需要掌握。
>
> **数据结构和算法：**树，DFS.

#### ==代码实现== :happy:

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        //进行二叉树的翻转
        if(!root) return root;
        TreeNode* temp = root->right;
        if(root->left){
            root->right = root->left;//记录当前的右子树，然后赋值，对左子树做递归
            invertTree(root->left);
        }
        else{
            root->right = nullptr;
        }
        if(temp){
            root->left = temp;
            invertTree(root->left);
        }
        else{
            root->left = nullptr;
        }
        return root;
    }
};
```

#### 知识点整理:up:

* 交换节点的时候要进行节点的备份，然后用备份的节点进行判断。
* 对于原函数进行递归，我用的比较少，我一般都再写一个dfs进行递归，也算是比较少见的情况。

#### 难点回顾:sagittarius:

```text
进行右侧节点的保存，在用保存的节点进行递归。要能够分析递归的时候节点的访问顺序。
```



## 链表

### 题号:剑指Offer 25 合并两个排序的链表 日期:2023/2/26

> ***网址：***[剑指 Offer 25. 合并两个排序的链表 - 力扣（LeetCode）](https://leetcode.cn/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/?envType=study-plan&id=lcof&plan=lcof&plan_progress=jnl46lm)
>
> **难度：**简单
>
> **思想概括：**合并两个链表。使用新的head和tail进行链表元素的连接。题目不难，但是我这道题之前做过一遍，但是第二遍做还是有错误。（一开始定义new_ptr和new_tail的时候，我没有进行赋值，导致后面访问是否为null的时候出现问题，如果一开始希望null，就赋值为nullptr）
>
> **数据结构和算法：**链表，对于链表元素进行连接。

#### ==代码实现== :happy:

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if(!l1){return l2;}
        if(!l2){return l1;}
        ListNode* new_head = nullptr;//这个地方需要设为nullptr，不然后面会报错。
        ListNode* new_tail = nullptr;
        while(l1 && l2){
            if(l1->val < l2->val){
                if(!new_head){
                    new_head = l1;
                    new_tail = l1;
                    l1 = l1->next;
                    continue;
                }
                new_tail->next = l1;
                new_tail = new_tail->next;
                l1 = l1->next;
            }
            else{
                if(!new_head){
                    new_head = l2;
                    new_tail = l2;
                    l2 = l2->next;
                    continue;
                }
                new_tail->next = l2;
                new_tail = new_tail->next;
                l2 = l2->next;
            }
        }
        if(!l1){new_tail->next = l2;}
        if(!l2){new_tail->next = l1;}
        return new_head;
    }
};
```

#### 知识点整理:up:

* 使用新的指针进行链表的连接，使用新的指针的时候要首先进行赋值，并且要判断指针是否为第一个。使用方法：使用head进行return，使用tail进行连接。
* 要进行while循环结束后的连接，不要忘记了。

#### 难点回顾:sagittarius:

```text
其实不难，但是要记得每次连接的时候，l1 = l1->next; new_tail = new_tail->next;
```



___

## 字符串

### 题链表号: 剑指 Offer 58 日期: 2023/2/27

> ***网址：***[剑指 Offer 58 - I. 翻转单词顺序 - 力扣（LeetCode）](https://leetcode.cn/problems/fan-zhuan-dan-ci-shun-xu-lcof/)
>
> **难度：**简单
>
> **思想概括：**关于字符串的操作，我很不熟练。可以说，我最不熟练的就是字符串的操作，字符串做的题目也不多。这道题记录一下，因为一开始做的时候我也觉得很烦，原因也是对于字符串不是很熟悉。做完其实感觉把字符串当作string来处理，熟记string里面的一些方法就可以处理好字符串。
>
> **数据结构和算法：**string：从string中使用[]取出来的是char（需要使用' '进行比较）。
>
> string中的方法：a.substr(第几位开始取，取几位); a.pop_back();将string的最后一位弹出（此题中用来取出随后的空格。）

#### ==代码实现== :happy:

```c++
class Solution {
public:
    string reverseWords(string s) {
    int l = s.length();
    string every_word = "";//一开始赋值为空
    stack<string> result_sta;
    for(int i=0;i<l;i++){
        if(s[i]!=' '){//取string中的某一个进行比较时，要使用单引号,数据类型是char！！！！
            every_word = every_word+s[i];//string中进行了运算符重载，可以直接在后面+!!
            continue;
        }
        else {//遇到' '
            if(every_word.length()>0){
                result_sta.push(every_word);//遇到空格进行分析的题目都可以这样做！！遇到空格截断！！
                every_word = "";
            }
        }
    }
    if(every_word.length()>0){
        result_sta.push(every_word);
    }
    l = result_sta.size();
    string result = "";
    for(int i =0;i<l;i++){
        result = result+result_sta.top()+' ';
        //cout<< result_sta.top()<<endl;
        result_sta.pop();
    }
    result.pop_back();//去掉最后的空格
    return result;
}
};
```

#### 知识点整理:up:

* 这是一类遇到空格截断的问题！遇到空格阶段都可以用这种方法进行分析。一位一位取。不是空格就在current_string后面加上当前的。遇到string就把current_string拿出来进行分析。

#### 难点回顾:sagittarius:

```text
对于string的分析不要怕。string就是char封装好的array，可以对于里面的char一位一位分析。这道题目也运用了stack，用于逆序的输出
方法使用：
s.substr(第几位开始取，取几位);
s.pop_back(去掉最后一位);
```

___



## DFS递归查找

### 题号:剑指 Offer 12 矩阵中的路径 日期:2023/2/28

> ***网址：***[剑指 Offer 12. 矩阵中的路径 - 力扣（LeetCode）](https://leetcode.cn/problems/ju-zhen-zhong-de-lu-jing-lcof/)
>
> **难度：**中等
>
> **思想概括：**这道题是使用DFS在二维矩阵中的查找。之前做过一遍，这次做在将二维矩阵打平成一维矩阵的时候出现了打错误，导致debug了很久之后要引以为戒！！！
>
> **数据结构和算法：**DFS，二维数组的打平，递归中的上下左右剪枝。

#### ==代码实现== :happy:

```c++
class Solution {
public:
    int x_matrix[4] = {-1,1,0,0};
    int y_matrix[4] = {0,0,1,-1};
    int l1;
    int l2;
    //bool matrix[6][6];
    //当然可以使用全局的二维数组，这样就不用计算打平成一位的位置，也不用传入数组指针。但是尽量不要将二维数组当作参数传入函数
    //因为二维数组是指针的指针**bool，且传入之后在函数中不能直接通过matrix[i][j]进行访问；
    bool dfs(vector<vector<char>>& board, string word,int i,int j,int index, bool* arrive){
        if(board[i][j] == word[index]){
            //从周围找可能的路径
            if(index == word.length()-1){
                return true;//找到最后一个
            }
            for(int k = 0;k<4;k++){//四个方向进行遍历
                int new_i = i+x_matrix[k];
                int new_j = j+y_matrix[k];
                if(new_i<0 || new_i>=l1 || new_j<0 || new_j>=l2){
                    continue;
                }
                else if(arrive[new_i*l2+new_j]){//如果这个点被标记了，就跳过
                    continue;
                }
                else{
                    if(board[new_i][new_j] == word[index+1]){
                        arrive[new_i*l2+new_j] = true;
                        if(dfs(board,word,new_i,new_j,index+1,arrive)){
                            return true;
                        }
                        arrive[new_i*l2+new_j] = false;
                    }
                    continue;
                }
            }
        }
        return false;
    }
    bool exist(vector<vector<char>>& board, string word) {
        l1 = board.size();
        l2 = board[0].size();
        bool arrive[l1*l2];//二维数组使用一维进行标记！！！ 我老是犯这种低级错误
        memset(arrive,0,sizeof(arrive));//set all locations to false
        for(int i=0;i<l1;i++){
            for(int j=0;j<l2;j++){
                char current = board[i][j];
                if(current == word[0]){
                    arrive[i*l2+j] = true;//！！！i要乘以j这一行的个数！！！相当于每过一行，要加j这一行的个数！！真的要注意
                    if(dfs(board,word,i,j,0,arrive)){
                        return true;
                    }
                    arrive[i*l2+j] = false;
                }
            }
        }
        return false;
    }
};
```

#### 知识点整理:up:

* 将二维数组打平成一维，这样可以将一维数组当作参数输入。(i,j)在一维中的位置:i*l2+j（l2是j的limit）
* 使用数组对于每一个位置进行是否到过的记录，来避免字母的重复使用。

#### 难点回顾:sagittarius:

```text
！！！二维数组的打平，真的是低级错误！！
使用打平的一维数组进行位置信息的记录。
使用dfs完成遍历，使用矩阵进行上下左右位置的遍历。使用||进行剪枝操作。
```

### 题号:剑指 Offer 面试题 13 机器人的运动范围 日期:2023/2/28

> ***网址：***[面试题13. 机器人的运动范围 - 力扣（LeetCode）](https://leetcode.cn/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)
>
> **难度：**中等
>
> **思想概括：**使用递归和vector<vector<bool>>来进行遍历和记录。这道题不难，基本上想法很简单，比上面那一道题也要简单。但是在里面使用了vector的二维数组，我一般能用矩阵解决的时候都会选择用矩阵，但是使用矩阵作为函数参数太麻烦了，当要使用二维数组参数的时候可以使用vector<vector<bool>>可以很好地解决矩阵传参的困难。同时，这道题记录一下vector的大小和数值初始化。
>
> **数据结构和算法：**二维数组，dfs.

#### ==代码实现== :happy:

```c++
class Solution {
public:
    int result = 1;
    int x_move[4] = {1,-1,0,0};
    int y_move[4] = {0,0,1,-1};
    int l1;
    int l2;
    bool cout_sum(int i, int j,int k){
        //计算各个位之和，返回是否满足要求
        int result = 0;
        while(i){
            result += i%10;
            i = i/10;
        }
        while(j){
            result += j%10;
            j = j/10;
        }
        return result<=k;
    }
    void dfs(int i,int j, int k,vector<vector<bool>> &matrix){//！！使用引用，很重要！！！
        if(!cout_sum(i,j,k)) return;
        //这个点符合要求,对四周的点进行扩张
        for(int q=0;q<4;q++){
            int x_new = i+x_move[q];
            int y_new = j+y_move[q];
            if(x_new<0 || x_new>=l1 ||y_new<0||y_new>=l2) continue;
            else if(matrix[x_new][y_new])continue;
            else{
                if(cout_sum(x_new,y_new,k)){
                    //cout<< i<<" "<<j <<endl;
                    result++;
                    matrix[x_new][y_new] = true;
                    dfs(x_new,y_new,k,matrix);
                } 
            }
        }
    }
    int movingCount(int m, int n, int k) {
        l1 =m, l2= n;
        vector<vector<bool>> matrix(l1,vector<bool>(l2,0));
        //vector<int> v1(m, 0); 一维vector初始化大小和数值
        //vector<vector<int> > v2(m, vector<int>(n, 0));二维vector 初始化大小和数值

        //memset(matrix,false,sizeof(matrix));
        matrix[0][0] = true;
        dfs(0,0,k,matrix);
        return result;
    }
};
```

#### 知识点整理:up:

* 传参时，引用的使用！ 当一个数据不需要产生新的副本的时候，使用引用可以避免新的副本出现。尤其是数组，加入引用可以在递归的时候一直修改一个的是同一个数组，而不会拷贝出新的数组。
* 使用vector来生成二维数组进行传参。vector的初始化！！很重要！

```c++
vector<int> v1(m, 0); //一维vector初始化大小和数值。加括号，（大小，初始值）
vector<vector<int> > v2(m, vector<int>(n, 0));//二维vector 初始化大小和数值。加括号，先定义外层大小，再加括号。
//再定义内层vector。
```

#### 难点回顾:sagittarius:

```text
使用引用，来保证不产生副本。
使用vector<vector<bool>>来进行二维数组的传参。
要熟记vector的大小和数值的初始化。
```







___



## 其他

### 题号: （每日一题）1238：循环码排列

> ***网址：[1238. 循环码排列 - 力扣（LeetCode）](https://leetcode.cn/problems/circular-permutation-in-binary-representation/)***
>
> **难度：** 中等
>
> **思想概括：**我使用的是自己想出来的方法。采用的是DP的思想。我觉得这道题能做出来还是蛮牛的。但是官方给出的方法是使用二进制码转格雷码（直接可以变成每个数字差一位的环（相邻和首位））。
>
> **数据结构和算法：** 数字转化为二进制。使用DP思想解题。格雷码

#### ==代码实现== :happy:

```c++
class Solution {
public:
    //这是我自己想出来的做法
    pair<vector<int>,int> recur(int n, string start){//使用递归来算出来之前的vector
        //cout<< start <<endl;
        if(n==1){
            char current_char = start[0];
            int exact_num = (int)current_char-48;
            vector<int> result = {exact_num, 1-exact_num};
            pair<vector<int>,int> result_value;
            result_value.first = result, result_value.second = 1;
            return result_value;
        }
        //需要递归
        pair<vector<int>,int> pre_value = recur(n-1,start.substr(1,n-1));
        vector<int> pre_vector = pre_value.first;
        int add_value = pre_value.second;
        char current_char = start[0];
        int start_num = (int)current_char-48;
        //cout<< pre_vector.size() <<" "<<start_num<<" "<<pre_value.second <<endl;
        int length = pre_vector.size();
        int pre_add = 2*add_value*(start_num);
        int aft_add = 2*add_value*(1-start_num);
        for(int j = length-1;j>=0;j--){//vector翻转对称
            pre_vector.push_back(pre_vector[j]+aft_add);
            pre_vector[j]=(pre_vector[j]+pre_add);
        }
        pair<vector<int>,int> result_value;
        result_value.first = pre_vector, result_value.second = add_value*2;
        return result_value;
    }
    string tobinary(int n,int start){//把数字转化为二进制的string
        if(n == 0){
            return "";
        }
        else {
            string left = tobinary(n-1,start/2);
            int current_num = start%2;
            string current = left+to_string(current_num);
            return current;
        }
    }
    vector<int> circularPermutation(int n, int start) {
        //第一个参数n代表总共有n位二进制位。有n位二进制位就有那么多个数，将这些数进行排序，相邻的数只差一位，第一个数是start
        string binary = tobinary(n,start);//将start转化为二进制的string
        //cout<< binary <<endl;
        pair<vector<int>,int> result = recur(n,binary);
        return result.first;
    }
    /*
    //官方使用格雷码，使用异或，做得很快
    vector<int> ans(1 << n);
        for (int i = 0; i < 1 << n; ++i) {
            ans[i] = i ^ (i >> 1) ^ start;
        }
        return ans;
    */
};
```

#### 知识点整理:up:

* 二进制转string
* string转化成int比较困难，需要一些奇怪的函数。但是char可以自动转化成int

```c++
char a = '0';
int aint = (int) a-48;//-48把ASCII转化成数字
```

* 使用DP，用有的结构做题
* 格雷码

#### 难点回顾:sagittarius:

```text
主要是不知道格雷码。还有掌握了char和int的转化。
```







