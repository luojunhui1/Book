# LeetShitCode
**build for exercise in LeetCode** 

## 壹 · 剑指Offer

### 重建二叉树

[剑指 Offer 07. 重建二叉树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

```c++
/*
二叉树的特性是子树和原树的结构的相似性，注意合理应用这个特点进行递归计算，
把问题拆分成对每个子树来解决。unordered_map可以用于查找的情况下，时间复杂度为O(1)。
*/
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
private:
    unordered_map<int,int> index;
public:
    TreeNode *myBuildTree(vector<int>& preorder, vector<int>& inorder, int left_pre, int right_pre, int left_in, int right_in)
    {
        //退出条件
        if(left_pre > right_pre)return nullptr;
		
        //根节点
        int pre_root = left_pre;
        
        //根节点在中序遍历表中的位置
        int in_root = index[preorder[left_pre]];
        
        //构造新的树的根节点
        TreeNode *root = new TreeNode(preorder[left_pre]);
        
        //左子树大小
        int size_left_sub_tree = in_root - left_in;
        
        root->left = myBuildTree(preorder, inorder, left_pre + 1, left_pre + size_left_sub_tree, left_in, in_root - 1);
        root->right = myBuildTree(preorder, inorder, left_pre + size_left_sub_tree + 1,right_pre,
        in_root + 1, right_in);

        return root;

    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = inorder.size();
        
        //建立unordered_map用于查找，对海量数据尤其有用
        for(int i = 0; i < n; i++)
        {
            index[inorder[i]] = i;
        }
        return myBuildTree(preorder, inorder, 0, n - 1, 0 , n-1);
    }
};
```

```c++
/*这种解法比较直观其实，不过推导过程需要仔细，并且它的实现使用栈*/
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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if(!preorder.size())
            return nullptr;
        
        TreeNode *root = new TreeNode(preorder[0]);
        stack<TreeNode*> stk;
        
        stk.push(root);

        int inorderIndex = 0;
        for(int i = 1; i < preorder.size(); ++i)
        {
            int preorderVal = preorder[i];
            TreeNode *node = stk.top();
            if(node->val != inorder[inorderIndex])
            {
                node->left = new TreeNode(preorderVal);
                stk.push(node->left);
            }
            else
            {
                while(!stk.empty() && stk.top()->val == inorder[inorderIndex])
                {
                    node = stk.top();
                    stk.pop();
                    ++inorderIndex;
                }
                node->right = new TreeNode(preorderVal);
                stk.push(node->right);
            }
        }
        return root;
    }
};
```

### 两个栈实现队列

[剑指 Offer 09. 用两个栈实现队列 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

```c++
class CQueue {
private:
    int cur_delete_top = -1;
    stack<int> origin_stack;//用于插入的栈
    stack<int> contrary_stack;//用于删除的栈
public:
    CQueue() {

    }
    
    void appendTail(int value) {
        origin_stack.push(value);
    }
    
    int deleteHead() {
        if(contrary_stack.empty())
        {
            //删除栈为空，更新之并清空插入栈
            while(!origin_stack.empty())
            {
                contrary_stack.push(origin_stack.top());
                origin_stack.pop();
            }
        }
        if(contrary_stack.empty())
            return -1;
        
        cur_delete_top = contrary_stack.top();
        contrary_stack.pop();
        return cur_delete_top;
    }
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
```

### 斐波那契

[剑指 Offer 10- I. 斐波那契数列 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

```c++
class Solution {
public:
    int fib(int n) {
        long long int val0 = 0,val1 = 1;
        long long int cur;
        if(n < 2 )return n;
        n = n - 1;
        while(n -- )
        {
            cur = val1;
            val1 += val0;
            //实际上这里使用std::move会增加内存消耗，对于简单基本数据类型，move会增加开销，move的实际使用
            //是为了减少构造函数的使用，一般用于复杂点的数据类型，由于cur在每次循环开头被赋值，无法比较指针
            //引用对它的影响如何
            val0 = std::move(cur);
            if(val1 > 1000000007)
                val1 = val1%1000000007;
        }    
        return val1;
    }
};
```

### 青蛙跳台阶

[剑指 Offer 10- II. 青蛙跳台阶问题 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/qing-wa-tiao-tai-jie-wen-ti-lcof/)

```c++
//也可以使用高精度加法，不过返回值是int就没必要了
class Solution {
int N[101];
public:
    int numWays(int n) {
        int val0 = 1,val1 = 1;
        N[0] = N[1] = 1;
        for(int i = 2;i <= n; i++)
        {
            N[i] = N[i - 1]%1000000007 + N[i - 2]%1000000007;
        }
        return N[n]%1000000007;
    }
}; 
```

### 旋转数组最小数字

[剑指 Offer 11. 旋转数组的最小数字 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

```c++
/*细节之处有两个：
1. 由于mid计算时向下取整，所以mid是会默认左偏的，用left位置的值和mid位置的值判断会造成最后mid=left循环
无法跳出的情况。解决办法是用right判断。
2. 当right位置的值等于mid位置的值时，无法判断最小值位置，需要通过right--来减小判断范围
*/

class Solution {

public:
    int minArray(vector<int>& numbers) {
        if(numbers.size() == 0)
            return NULL;
        int left = 0, right = numbers.size() - 1;
        int mid;
        
        if(numbers[left] < numbers[right])return numbers[left];

        while(left < right)
        {
            mid = (left + right)/2;
            if(numbers[right] > numbers[mid])
                right = mid;
            else if(numbers[right] < numbers[mid])
                left = mid + 1;   
            else right--;
        }

        return numbers[left];
    }
};
```

### 矩阵中的路径

[剑指 Offer 12. 矩阵中的路径 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

```c++
/*是比较常见的dfs题，但对于dfs模板并不熟悉，所以实现很粗糙*/
class Solution {
private:
    bool visited[201][201];
    vector<int> start_x;
    vector<int> start_y;
    int row,col;

    int dx[4] = {0,1,0,-1};
    int dy[4] = {1,0,-1,0};
public:
    void ClearVisited()
    {
        memset(visited,false,201*201);
    }

    bool dfs(vector<vector<char>>& board, string& word,int start_x, int start_y, int count)
    {
        if(count == word.length())
            return true;
        
        int cur_x,cur_y;
        for(int i = 0; i < 4; i++)
        {
            cur_x = start_x + dx[i];
            cur_y = start_y + dy[i];

            if(cur_x >= 0 && cur_x < row && cur_y >= 0 && cur_y < col && !visited[cur_x][cur_y])
            {
                if(board[cur_x][cur_y] == word[count])
                {
                    visited[cur_x][cur_y] = true;
                    if(dfs(board,word,cur_x,cur_y,count + 1))
                        return true;
                    visited[cur_x][cur_y] = false;
                }
            }    
        }

        return false;
    }
    bool exist(vector<vector<char>>& board, string word) {
        row = board.size();
        if(row == 0)return false;
        col = board[0].size();

        for(int i = 0; i < row; i++)
        {
            for(int j = 0; j < col; j++)
            {
                if(board[i][j] == word[0])
                {
                    start_x.push_back(i);
                    start_y.push_back(j);
                }
            }
        }

        for(int i = 0; i < start_x.size();i++)
        {
            ClearVisited();
            visited[start_x[i]][start_y[i]] = true;
            if(dfs(board,word,start_x[i],start_y[i],1))
                return true;
        }

        return false;
    }
};
```

以下是评论中的解法，通过破坏性修改原矩阵中的值，减少了visited数组的开销；并且，由于访问时对当前位置的四个方向做搜索的操作大致相同，均为：

	1. 判断越界
 	2. 判断是否访问过及是否为目标字符
 	3. 继续深度搜索

这里利用了操作符操作的顺序和递归特性，将循环变化为一个语句，妙！

```c++
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        rows = board.size();
        cols = board[0].size();
        for(int i = 0; i < rows; i++) {
            for(int j = 0; j < cols; j++) {
                if(dfs(board, word, i, j, 0)) return true;
            }
        }
        return false;
    }
private:
    int rows, cols;
    bool dfs(vector<vector<char>>& board, string word, int i, int j, int k) {
        if(i >= rows || i < 0 || j >= cols || j < 0 || board[i][j] != word[k]) return false;
        if(k == word.size() - 1) return true;
        
        board[i][j] = '\0';
        
        bool res = dfs(board, word, i + 1, j, k + 1) || dfs(board, word, i - 1, j, k + 1) || 
                      dfs(board, word, i, j + 1, k + 1) || dfs(board, word, i , j - 1, k + 1);
        
        board[i][j] = word[k];
        return res;
    }
};
```

淦，有个问题，由于他那个运算符的写法（我猜测），导致程序会进入很多次无用递归，导致空间和时间开销甚至都比我的大:sweat:

![image-20210401163921484](https://cdn.jsdelivr.net/gh/luojunhui1/BlogPicture/Windows/20210401163942.png)

### 机器人的运动范围

[剑指 Offer 13. 机器人的运动范围 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

这个下面的是错了，原因是这种方法（不用搜索的话）难以判断区域的连续性，其实可以声明一个长度为10的int型数组来维护在每一行上的连续性，每一列上的连续性已经由循环中的if条件保证，但到了这份上写下去就没有意义了。放在这是为了纪念我死去的题解嘤嘤嘤。


```c++
class Solution {
private:
    int sum = 0;
public:
    int movingCount(int m, int n, int k) {
        int last_limit,count;
        int row_limit;
        for(int i = 0; i < n;i++)
        {
            last_limit = k,count = 0;
            while(1)
            {
                row_limit = (m < count*10)?(0):(m - count*10);
                if(last_limit < 9)
                {
                    sum += min(last_limit + 1,row_limit);
                    break;
                }
                else
                {
                    sum += min(10,row_limit);
                    last_limit--;
                    if(++count*10 > m && last_limit < 0)break;
                }
            }
            k--;
            if(k < 0)return sum;
        }
        return sum;
    }
};	
```

```c++
//正确解法 DFS版
class Solution {
public:
    int movingCount(int m, int n, int k) {
        vector<vector<bool>> visited(m, vector<bool>(n, 0));
        return dfs(0, 0, 0, 0, visited, m, n, k);
    }
private:
    /*si表示行坐标各数位之和，sj表示列坐标各数位之和*/
    int dfs(int i, int j, int si, int sj, vector<vector<bool>> &visited, int m, int n, int k) {
        if(i >= m || j >= n || k < si + sj || visited[i][j]) return 0;
        visited[i][j] = true;
        return 1 + dfs(i + 1, j, (i + 1) % 10 != 0 ? si + 1 : si - 8, sj, visited, m, n, k) +
                   dfs(i, j + 1, si, (j + 1) % 10 != 0 ? sj + 1 : sj - 8, visited, m, n, k);
    }
};

//正确解法 BFS版
class Solution {
public:
    int movingCount(int m, int n, int k) {
        vector<vector<bool>> visited(m, vector<bool>(n, 0));
        int res = 0;
        queue<vector<int>> que;
        que.push({ 0, 0, 0, 0 });
        while(que.size() > 0) {
            vector<int> x = que.front();
            que.pop();
            int i = x[0], j = x[1], si = x[2], sj = x[3];
            if(i >= m || j >= n || k < si + sj || visited[i][j]) continue;
            visited[i][j] = true;
            res++;
            que.push({ i + 1, j, (i + 1) % 10 != 0 ? si + 1 : si - 8, sj });
            que.push({ i, j + 1, si, (j + 1) % 10 != 0 ? sj + 1 : sj - 8 });
        }
        return res;
    }
};

```

### 剪绳子

[剑指 Offer 14- I. 剪绳子 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/jian-sheng-zi-lcof/)

```c++
/*这是一道数学题运用贪心的思想来求解，这样最快
但更符合直觉得解法是动态规划
*/
//贪心解法
class Solution {
public:
    int cuttingRope(int n) {
       if(n <= 3)
            return n - 1;
        if(n%3 == 1)
            return pow(3,n/3 - 1)*4; 
        if(n%3 == 0)
            return pow(3, n/3);
        return pow(3,n/3)*2;
    }
};

//动态规划
class Solution:
    def cuttingRope(self, n: int) -> int:
        dp = [0] * (n + 1)
        dp[2] = 1
        for i in range(3, n + 1):
            for j in range(2, i):
                dp[i] = max(dp[i], max(j * (i - j), j * dp[i - j]))
        return dp[n]
```

### 二进制中1的个数

[剑指 Offer 15. 二进制中1的个数 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

```c++
/*普通解法，逐位判断*/
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int count = 0;
        while(n != 0)
        {
            if(n&1)
                count++;
            n = n>>1;
        }
        return count;
    }
};

/*秀儿解法，利用n&(n - 1)会将n的二进制中最后一个1及其后面的0反位的性质*/
class Solution:
    def hammingWeight(self, n: int) -> int:
        res = 0
        while n:
            res += 1
            n &= n - 1
        return res
/*武器带师解法*/
class Solution {
public:
    int hammingWeight(uint32_t n) {
        return ((bitset<32>)n).count();
    }
};
```

### 数值的整数次方

[剑指 Offer 16. 数值的整数次方 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

```c++
class Solution {
public:
    double myPow(double x, int n) {
        if(n == 0)return 1;
        double res = 1;
        
        /*用N代替N是因为若n = -2^31次方时，直接转换为正数会超出int型的表示范围*/
        long long int N = n;
        if(N < 0)
        {
            N = -1*N;
            x = 1.0/x;
        }

        double cur = x;
        while(N/2 > 0)
        {
            if(N%2 != 0)
                res *= x;
            N = N/2;
            x *= x;
        }
     
        return res*x;
    }
};
```

打印从1到最大的n位数

[剑指 Offer 17. 打印从1到最大的n位数 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/da-yin-cong-1dao-zui-da-de-nwei-shu-lcof/)

```c++
/*这道题的第一反应是暴力，然后就是dfs，但没有这个题目的考大数表示的敏感性(其实题目本意应该也不是，
只是评论区觉得改成大数更有水平一点)，大数转换为字符串表示，递归到第n次，将字符串转换为数字，push到
vector中，ok*/

/*我， 武 器 带 师*/
class Solution {
public:
    vector<int> printNumbers(int n) {
        vector<int> res;
        res.resize(pow(10,n) - 1);
        std::iota(res.begin(), res.end(), 1);
        return res;
    }
};

/*面向考点解法，解决大数问题*/
class Solution {
public:
    vector<int> res;

    void dfs(int n, int idx, string path) {
        if (idx == n) {
            int val = std::stoi(path);
            if (val != 0) {
                res.push_back(val);
            }
            return;
        }
        for (int i = 0; i < 10; ++i) {
            path[idx] = i + '0';
            dfs(n, idx+1, path);
        }
    }

    vector<int> printNumbers(int n) {
        string path(n, '0');
        dfs(n, 0, path);
        return res;
    }
};
```

### 删除链表中的节点

[剑指 Offer 18. 删除链表的节点 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

```c++
/*最基础的链表题，但写起来并不熟练*/

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
    ListNode* deleteNode(ListNode* head, int val) {
        ListNode *pre,*aft;
        if(head == nullptr)return nullptr;

        pre = head;
        aft = pre->next;
        if(pre->val == val)
            return pre->next;
        
        while(aft != nullptr && aft->val != val)
        {
            pre = aft;
            aft = aft->next;
        }
        if(aft != nullptr)
       		pre->next = aft->next;
        return head;
    }
};
```

### 正则表达式匹配

[剑指 Offer 19. 正则表达式匹配 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

```c++
/*这道题出上手最直接的想法是使用堆栈，类似于递归的思想一步步匹配，但情况过多，最终放弃*/
/*题解使用动态规划的思路，对于*号的特点，选择从后往前分析，这就是经验了*/

class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.size();
        int n = p.size();

        auto matches = [&](int i, int j) {
            if (i == 0) {
                return false;
            }
            if (p[j - 1] == '.') {
                return true;
            }
            return s[i - 1] == p[j - 1];
        };

        vector<vector<int>> f(m + 1, vector<int>(n + 1));
        f[0][0] = true;
        for (int i = 0; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (p[j - 1] == '*') {
                    f[i][j] |= f[i][j - 2];
                    if (matches(i, j - 1)) {
                        f[i][j] |= f[i - 1][j];
                    }
                }
                else {
                    if (matches(i, j)) {
                        f[i][j] |= f[i - 1][j - 1];
                    }
                }
            }
        }
        return f[m][n];
    }
};

//但看题解中的递归解法，感觉分析好了，使用适当的工具，也还是可以的
//JAVA
class Solution {
    public boolean isMatch(String A, String B) {
        // 如果字符串长度为0，需要检测下正则串
        if (A.length() == 0) {
            // 如果正则串长度为奇数，必定不匹配，比如 "."、"ab*",必须是 a*b*这种形式，*在奇数位上
            if (B.length() % 2 != 0) return false;
            int i = 1;
            while (i < B.length()) {
                if (B.charAt(i) != '*') return false;
                i += 2;
            }
            return true;
        }
        // 如果字符串长度不为0，但是正则串没了，return false
        if (B.length() == 0) return false;
        // c1 和 c2 分别是两个串的当前位，c3是正则串当前位的后一位，如果存在的话，就更新一下
        char c1 = A.charAt(0), c2 = B.charAt(0), c3 = 'a';
        if (B.length() > 1) {
            c3 = B.charAt(1);
        }
        // 和dp一样，后一位分为是 '*' 和不是 '*' 两种情况
        if (c3 != '*') {
            // 如果该位字符一样，或是正则串该位是 '.',也就是能匹配任意字符，就可以往后走
            if (c1 == c2 || c2 == '.') {
                return isMatch(A.substring(1), B.substring(1));
            } else {
                // 否则不匹配
                return false;
            }
        } else {
            // 如果该位字符一样，或是正则串该位是 '.'，和dp一样，有看和不看两种情况
            if (c1 == c2 || c2 == '.') {
                return isMatch(A.substring(1), B) || isMatch(A, B.substring(2));
            } else {
                // 不一样，那么正则串这两位就废了，直接往后走
                return isMatch(A, B.substring(2));
            }
        }
    }
}
```

### 表示数值的字符串

[剑指 Offer 20. 表示数值的字符串 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)

```c++
/**
	无论使用怎样的方法，对于“数值”的组成必须清楚，才能构造相应的正规式，比如在上手时，认为可以出现多个e(E)，
	这对分析多少造成了影响，只有构造出正确的正规式，才能使用如下的简单的逻辑判断或者使用自动状态机。
**/
class Solution {
private:
    // 整数的格式可以用[+|-]B表示, 其中B为无符号整数
    bool scanInteger(const string s, int& index){

        if(s[index] == '+' || s[index] == '-')
            ++index;

        return scanUnsignedInteger(s, index);
    }
    
    bool scanUnsignedInteger(const string s, int& index){

        int befor = index;
        while(index != s.size() && s[index] >= '0' && s[index] <= '9')
            index ++;

        return index > befor;
    }
public:
    // 数字的格式可以用A[.[B]][e|EC]或者.B[e|EC]表示，
    // 其中A和C都是整数（可以有正负号，也可以没有），而B是一个无符号整数
    bool isNumber(string s) {

        if(s.size() == 0)
            return false;
        int index = 0;

        //字符串开始有空格，可以返回true
        while(s[index] == ' ')  //书中代码没有该项测试
            ++index;

        bool numeric = scanInteger(s, index);

        // 如果出现'.'，接下来是数字的小数部分
        if(s[index] == '.'){

            ++index;

            // 下面一行代码用||的原因：
            // 1. 小数可以没有整数部分，例如.123等于0.123；
            // 2. 小数点后面可以没有数字，例如233.等于233.0；
            // 3. 当然小数点前面和后面可以有数字，例如233.666
            numeric = scanUnsignedInteger(s, index) || numeric;
        }

        // 如果出现'e'或者'E'，接下来跟着的是数字的指数部分
        if(s[index] == 'e' || s[index] == 'E'){

            ++index;

            // 下面一行代码用&&的原因：
            // 1. 当e或E前面没有数字时，整个字符串不能表示数字，例如.e1、e1；
            // 2. 当e或E后面没有整数时，整个字符串不能表示数字，例如12e、12e+5.4
            numeric = numeric && scanInteger(s ,index);
        }

        //字符串结尾有空格，可以返回true
        while(s[index] == ' ')
            ++index;
        //cout << s.size() << " " << index;   //调试用

        return numeric && index == s.size();
    }
};
```

### [调整数组顺序使奇数位于偶数前面](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

```c++
class Solution {
public:
    vector<int> exchange(vector<int>& nums) {
        int i = 0;
        for(int k = 0; k < nums.size(); k++)
        {
            if(nums[k]%2 != 0)
            {
                while(i < nums.size() && nums[i]%2 != 0){i++;}
                if(i < k)
                {
                    swap(nums[i++],nums[k]);
                }
            }
        }
        return nums;
    }
};
```

### [链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

```c++
/*经典快慢指针*/
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode *pre,*las;
        pre = las = head;
        while(las->next != nullptr && k-- > 0)
        {
            las = las->next;
        }
        if(las->next == nullptr && k != 0)
            return pre;
        while(las != nullptr)
        {
            pre = pre->next;
            las = las->next;
        }

        return pre;
    }
};
```

### [反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/)

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
private:
    ListNode *newHead;
public:
    ListNode* func(ListNode* head)
    {
         if(head->next == nullptr)
        {
            newHead = head;
            return head;
        }
        func(head->next)->next = head;
        return head;
    }
    ListNode* reverseList(ListNode* head) {
        if(head == nullptr)
            return nullptr;
        func(head);
        head->next = nullptr;
        return newHead;
    }
};
```

### [合并两个排序的链表](https://leetcode-cn.com/problems/he-bing-liang-ge-pai-xu-de-lian-biao-lcof/)

```c++
/**插入排序**/
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
        ListNode *head;

        if(l1 == nullptr)
            return l2;
        if(l2 == nullptr)
            return l1;

        ListNode *ptr,*cur;

        if(l1->val > l2->val)
        {
            head = l2;
            l2 = l2->next;
        }
        else
        {
            head = l1;
            l1 = l1->next;
        }

        ptr = head;
        
        while(l1 != nullptr && l2 != nullptr)
        {
            if(l1->val > l2->val)
            {
                cur = l2;
                l2 = l2->next;
            }
            else
            {
                cur = l1;
                l1 = l1->next;
            }

            ptr->next = cur;
            ptr = ptr->next;
        }

        while(l1 != nullptr)
        {
            cur = l1;
            l1 = l1->next;
            ptr->next = cur;
            ptr = ptr->next;
        }

        while(l2 != nullptr)
        {
            cur = l2;
            l2 = l2->next;
            ptr->next = cur;
            ptr = ptr->next;
        }

        return head;
    }
};
```

### [树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

```c++
/**虽然粗糙，但它快乐**/
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
    bool dfs(TreeNode* A, TreeNode* B)
    {
        if(B == nullptr)
            return true;
        if(A == nullptr)
            return false;

        return A->val == B->val && dfs(A->left,B->left) && dfs(A->right, B->right);
    }
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if(A == nullptr || B == nullptr)
            return false;
        return dfs(A,B) || isSubStructure(A->left,B) || isSubStructure(A->right, B);
    }
};
```

### [二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

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
    void exChange(TreeNode* root)
    {
        if(root == nullptr)
            return;
        
        TreeNode *cur = root->left;
        root->left = root->right;
        root->right = cur;

        exChange(root->left);
        exChange(root->right);
    }
    TreeNode* mirrorTree(TreeNode* root) {
        exChange(root);
        return root;
    }
};
```

### [对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

```c++
/**使用两个栈保存左子树节点和右子树节点，二者入栈顺序相反，依次比较即可**/
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
private:
    stack<TreeNode*> left;
    stack<TreeNode*> right;
public:
    bool isSymmetric(TreeNode* root) {
        if(root  == nullptr)
            return true;
        left.push(root->left);
        right.push(root->right);

        TreeNode *lnode,*rnode;

        while(left.size() > 0 && left.size() == right.size())
        {
            lnode = left.top();rnode = right.top();
            if(lnode == rnode&& lnode== nullptr)
            {
                left.pop();right.pop();
                continue;
            }
            if((lnode == nullptr && rnode != nullptr) 
            || (lnode != nullptr && rnode == nullptr))
                return false;

            if(lnode->val == rnode->val)
            {
                left.pop();right.pop();

                left.push(lnode->left);left.push(lnode->right);

                right.push(rnode->right);right.push(rnode->left);
            }else
            {
                return false;
            }    
        }
        if(left.size() != right.size())
            return false;
        return true;
    }
};

//===================================精选题解===============================================
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == NULL) return true;
        //使用一个栈来保存节点也可行
        stack<TreeNode*> st; // 这里改成了栈
        st.push(root->left);
        st.push(root->right);
        
        while (!st.empty()) {
            TreeNode* leftNode = st.top(); st.pop();
            TreeNode* rightNode = st.top(); st.pop();
            
            //两节点均为空
            if (!leftNode && !rightNode) {
                continue;
            }
            
            //判断为false条件
            if ((!leftNode || !rightNode || (leftNode->val != rightNode->val))) {
                return false;
            }
            
            //按序push入栈中
            st.push(leftNode->left);
            st.push(rightNode->right);
            st.push(leftNode->right);
            st.push(rightNode->left);
        }
        return true;
    }
};
```

### [顺时针打印矩阵](https://leetcode-cn.com/problems/shun-shi-zhen-da-yin-ju-zhen-lcof/)

```c++
/*丑是丑，能用就行*/
class Solution {
private:
    int direct[4][2] = { {1,0}, {0,1}, {-1,0}, {0,-1}};
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        if(matrix.size() == 0)
            return vector<int>();
        
        //用于控制行列插入vector的长度
        int w = matrix[0].size();
        int h = matrix.size();
		
        //用于记录当前行列插入个数
        int count = 0;
		
        //用于退出条件
        int maxCount = w*h;
        h = h - 1;
        
        //i,j表示当前元素行列位置
        int i = 0, j = -1;
        //表示行列前进方向
        int di = 0;
        //表示当前插入次数
        int cur = 0;
        //返回vector
        vector<int> res;
        
        while(cur < maxCount)
        {
            count++;
            i += direct[di][1];
            j += direct[di][0];
            
            res.push_back(matrix[i][j]);
            cur++;
            
            if(di%2 == 0 && count == w)
            {
                di = (di + 1)%4;
                w--;
                count = 0;
            }
            else if(di%2 == 1 && count == h)
            {
                di = (di + 1)%4;
                h--;
                count = 0;
            }
        }

        return res;
    }
};
```

### [栈的压入、弹出序列](https://leetcode-cn.com/problems/zhan-de-ya-ru-dan-chu-xu-lie-lcof/)

```c++
class Solution {
private:
    int out_index = 0;
    int in_index_low = 0;
    int in_index_high = 0;
public:
    bool validateStackSequences(vector<int>& pushed, vector<int>& popped) {
        stack<int> tmp;
        int i=0;
        for(auto s:pushed){
            tmp.push(s);
            while(!tmp.empty()&&popped[i]==tmp.top()){
                tmp.pop();
                i++;
            }
        }
        return tmp.empty();
    }
};
```

### [从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

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
    vector<int> levelOrder(TreeNode* root) {
        vector<int>res;
        queue<TreeNode*>que;
        if(!root)    return res;
        que.push(root);
        while(!que.empty()){

            TreeNode* tmp = que.front();
            que.pop();
            res.push_back(tmp->val);
            if(tmp->left)    que.push(tmp->left);
            if(tmp->right)   que.push(tmp->right);           
        }
        return res;
    }
};
```

### [从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

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
    vector<vector<int>> levelOrder(TreeNode* root) {
if (!root)  return {};
        vector<vector<int>> res;    // 结果数组
        queue<TreeNode*> que;       // 辅助队列
        que.push(root);             
        while (!que.empty())
        {
            int cnt = que.size();   // 当前层的节点数
            vector<int> level;      // 保存当前层的元素
            // 处理当前层的元素，每个元素出队前将其左右子节点入队，作为下一层的元素
            while (cnt)
            {
                auto node = que.front();
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
                level.push_back(node->val);
                que.pop();
                cnt--;
            }
            res.push_back(level);   // 得到了一层的元素
        }
        return res;
    }
};
```

### [从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

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
    vector<vector<int>> levelOrder(TreeNode* root) {
      if(root == nullptr)
        return vector<vector<int>>();

        stack<TreeNode*> layerVals;
        vector<int> rowVals;
        vector<TreeNode*> rowPtrs;
        vector<vector<int>> res;
        layerVals.push(root);

        bool direction = true;
        int cur;
        while(layerVals.size() > 0)
        {
            rowVals.clear();
            rowPtrs.clear();
            while(!layerVals.empty())
            {
                if((layerVals.top()) != nullptr)
                {
                    rowPtrs.push_back((layerVals.top()));
                    rowVals.push_back((layerVals.top())->val);
                }
                layerVals.pop();
            }
            if(direction)
            {
                for(int i = 0; i < rowPtrs.size(); i++)
                {
                    layerVals.push(rowPtrs[i]->left);
                    layerVals.push(rowPtrs[i]->right);
                }
            }else
            {
                for(int i =0; i < rowPtrs.size() ; i++)
                {
                    layerVals.push(rowPtrs[i]->right);
                    layerVals.push(rowPtrs[i]->left);
                }
            }
            direction = !direction;
            if(rowVals.size() > 0)
                res.push_back(rowVals);
        }

        return res;
    }
};
```

### [二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

```c++
/*树的题，需要好好分析树的子结构*/
class Solution {
private:
    bool dfs(vector<int>& postorder, int start, int end)
    {
        // 单个结点，必然是二叉树
        if (start >= end)
        {
            return true;
        }
        // 找左子树的结束位置
        int p = start;
        int rootVal = postorder[end];
        while (postorder[p] < rootVal)
        {
            ++p;
        }
        // 设置左子树结束位置
        int leftEnd = p - 1;

        // 找右子树的结束位置
        while (postorder[p] > rootVal)
        {
            ++p;
        }

        // 右子树结束位置应该正好在end这里
        return p == end && dfs(postorder, start, leftEnd) && dfs(postorder, leftEnd+1, end-1);
    }

public:
    bool verifyPostorder(vector<int>& postorder) {
        return dfs(postorder, 0, postorder.size()-1);
    }
};
```

### [复杂链表的复制](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

```c++
/**有一说一，我没看懂题目，题目要求深拷贝链表，难点在于如何想到把新增的节点加入到原有的链表中，
即复制后的节点位于原节点之后，这样就可以利用原链表来直接获取random成员的值，此外，还可以使用hash map来
实现对radom成员的复制，hash mp用于实现原链表节点地址和新链表节点地址的映射**/
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if(head == nullptr)
            return head;
        
        Node *p = head,*copyPtr;
        
        while(p != nullptr)
        {
            copyPtr = new Node(p->val);
            copyPtr->next = p->next;
            p->next = copyPtr;
            p = copyPtr->next;
        }

        p = head;
        while(p != nullptr)
        {
            p->next->random = (p->random != nullptr)?(p->random->next):(nullptr);
            p = p->next->next;
        }

        p = head;
        copyPtr = head->next;
        Node *newHead = copyPtr;
        while(copyPtr->next != nullptr)
        {
            p->next = p->next->next;
            copyPtr->next = copyPtr->next->next;

            copyPtr = copyPtr->next;
            p = p->next;
        }
        
        p->next = nullptr;

        return newHead;
    }
};
```

```c++
/**在思路上很简单，但在工具上，一开始想到的是使用数组，但动态的数组过于麻烦，静态的数组则至少需要200*sizeof(int)
的大小，故可以是使用c++的new 和 delete 工具， 方便地处理数据的内存申请和释放**/
#define MIN(X,Y) ((X > Y)?(Y):(X))
class MinStack {
public:
    class Node
    {
        public:
        int val;
        int min;
        Node *next;

        Node(int val_, int min_, Node *next_)
        {
            val = val_;
            min = min_;
            next = next_;
        }
    };

    Node *head;
public:
    /** initialize your data structure here. */
    MinStack() {
        head = nullptr;
    }
    
    void push(int x) {
        if(head == nullptr)
            head = new Node(x,x,nullptr);
        else
            head = new Node(x,MIN(x,head->min),head);
    }
    
    void pop() {
        Node *cur = head->next;
        delete head;
        head = cur;
    }
    
    int top() {
        return head->val;
    }
    
    int min() {
        return head->min;
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->min();
 */
```

### [二叉树中和为某一值的路径](https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/)

```c++
/**1. 数据可能为负数，需仔细查阅题目数据说明
   2. 临界区情况仔细考察，因未仔细考察临界区sum先加还是先判断的情况导致代码中有bug
**/
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
    vector<vector<int>> res;
    vector<int> curRoute;

    int target;
    void dfs(TreeNode *root, int sum)
    {
        if(root == nullptr)
            return;

        curRoute.push_back(root->val);

        sum += root->val;

        if(sum == target && root->left == nullptr && root->right == nullptr)
            res.push_back(curRoute);
        
        dfs(root->left, sum);
        dfs(root->right, sum);

        curRoute.pop_back();
    }
public:
    vector<vector<int>> pathSum(TreeNode* root, int target) {
        this->target = target;

        dfs(root,0);

        return res;
    }
};
```

### [二叉搜索树与双向链表](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

```c++
/**如何在二叉树中得到一个有序序列，当然中序遍历即可，但如何将他们保存在一个队列中，只需要在对dfs中
遍历完左节点后将数据更新即可，这道题纠结了一会如何得到这样的一个有序链表，对二叉树的遍历特点极其陌生**/
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;

    Node() {}

    Node(int _val) {
        val = _val;
        left = NULL;
        right = NULL;
    }

    Node(int _val, Node* _left, Node* _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
private:
    Node *head,*tail;
    void midorder(Node *root)
    {
        if(root != nullptr)
        {
            midorder(root->left);
            if(head == nullptr)
                tail = head = root;
            else
            {
                root->left = tail;
                tail->right = root;
                tail = root;
            }

            midorder(root->right);
        }
    }
public:
    Node* treeToDoublyList(Node* root) {
        if(root == nullptr)
            return nullptr;

        head = tail = nullptr;
        midorder(root);

        head->left = tail;
        tail->right = head;

        return head;
    }
};
```

### [序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/)

```c++
/**更好的方法是使用‘，’来对字符串分割，第一次未注意负数的情况，第二次加上负数判断即可，在逻辑上比较容易想到**/
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Codec {
private:
    string str;
    queue<TreeNode*> s;
    queue<TreeNode*> des;
public:

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        str = "[";
        s.push(root);
        int layerCount = 1, i = 0;
        while(!s.empty())
        {
            if(s.front() != nullptr)
            {
                str += std::to_string(s.front()->val) + ",";
                s.push(s.front()->left);
                s.push(s.front()->right);
            }
            else
            {
                str += "null,";
            }
            s.pop();
        }
        str.erase(str.end() - 1, str.end());
        str += "]";
        return str;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        int len = data.length();
        int count = 1;
        bool leftCompleted = false;
        string cur;
        TreeNode *head = nullptr, *ptr = nullptr;

        while(count < len)
        {
            cur.clear();
            while(isdigit(data[count]) || data[count] == '-')
            {
                cur += data[count++];
            }
            if(data[count] == 'n')
            {
                if(head == nullptr)
                {
                    return nullptr;
                }
                
                if(leftCompleted)
                    des.pop();

                leftCompleted = !leftCompleted;
                count += 4;
            }
            else
            {
                int number = stoi(cur);
                if(head == nullptr)
                {
                    head = new TreeNode(number);
                    ptr = head;
                    des.push(ptr);
                }
                else
                {
                    ptr = new TreeNode(number);
                    if(!leftCompleted)
                        des.front()->left = ptr;
                    else
                    {
                        des.front()->right = ptr;
                        des.pop();
                    }
                    leftCompleted = !leftCompleted;
                    des.push(ptr);
                }
               // count += cur.length();
            }
            count += 1;
        }

        return head;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec codec;
// codec.deserialize(codec.serialize(root));    
```

### [字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

```c++
/**要点如下：
1. 如何防止重复，从方法上，就是字符串上每个位置上的字符与其之后位置的字符交换，遇到已经交换过的字符则跳过
2. 如何判断已经交换过，使用set或者visited数组标记都可
3. 如何生成排列的字符串，可以通过交换不断生成新的字符串，也可以在dfs的每一层向目标字符串中加入新的字符来生成
**/
class Solution {
public:
    vector<string> res;

    void dfs(string s, int index)
    {
        if(index == s.size() - 1)
        {
            res.push_back(s);
            return;
        }
        set<int> alphas;
        for(int i = index; i < s.size();i++)
        {
            if(alphas.find(s[i]) != alphas.end())continue;
            alphas.insert(s[i]);
            swap(s[index], s[i]);
            dfs(s, index + 1);
            swap(s[index], s[i]);
        }
    }
    vector<string> permutation(string s) {
        dfs(s, 0);

        return res;
    }
};
```

### [数组中出现次数超过一半的数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)

```c++
/**摩尔投票法，基本思想在于让不相同的两个数同归于尽，这样最后剩下来的相同的或者唯一的数必然就是出现
次数超过半数的数。**/
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int count = 0,num = 0;
        for(auto &i: nums)
        {
            if(count == 0)
            {
                num = i;
            }

            count += (i == num)?(1):(-1);
        }

        return num;
    }
};
```

### [数据流中的中位数](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

```c++
/*算法重点在于构造大顶堆和小顶堆，其中大顶堆中存放着数据流中数值较小的那一半的数据，小顶堆中存放着数据流中
数值较大的那部分的数据，每次插入时要保持两个堆的数据平衡。
如何保证大顶堆中的数据是较小的那一半的数据：每次向大顶堆中插入数据之前，首先将其插入到小顶堆中，再将小顶堆
的顶部节点取出插入到大顶堆中即可。

其它思路:插入排序
*/
class MedianFinder {
private:
    priority_queue<int, vector<int>, greater<int> > smallTopHeap;
    priority_queue<int, vector<int>, less<int> > largeTopHeap;

public:
    /** initialize your data structure here. */
    MedianFinder() {

    }
    
    void addNum(int num) {
        int top;

        if(smallTopHeap.size() == largeTopHeap.size())
        {
            smallTopHeap.push(num);
            top = smallTopHeap.top();
            smallTopHeap.pop();
            largeTopHeap.push(top);
        }
        else
        {
            largeTopHeap.push(num);
            top = largeTopHeap.top();
            largeTopHeap.pop();
            smallTopHeap.push(top);
        }
    }
    
    double findMedian() {
        if(smallTopHeap.size() == largeTopHeap.size())
            return smallTopHeap.top()*0.5 + largeTopHeap.top()*0.5;
        else
            return largeTopHeap.top();
    }
};
```

### [最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)

```c++
/**较为直接的方法就是直接使用小顶堆就可，但也可以使用基于快速排序的TopK算法，如下：**/
class Solution {
public:
    vector<int> getLeastNumbers(vector<int>& arr, int k) {
        if (k >= arr.size()) return arr;
        return quickSort(arr, k, 0, arr.size() - 1);
    }
private:
    vector<int> quickSort(vector<int>& arr, int k, int l, int r) {
        int i = l, j = r;
        while (i < j) {
            while (i < j && arr[j] >= arr[l]) j--;
            while (i < j && arr[i] <= arr[l]) i++;
            swap(arr[i], arr[j]);
        }
        swap(arr[i], arr[l]);
        if (i > k) return quickSort(arr, k, l, i - 1);
        if (i < k) return quickSort(arr, k, i + 1, r);
        vector<int> res;
        res.assign(arr.begin(), arr.begin() + k);
        return res;
    }
};
```

### [连续子数组的最大和](https://leetcode-cn.com/problems/lian-xu-zi-shu-zu-de-zui-da-he-lcof/)

```c++
/**较为简单的动态规划，当dp[i - 1] < 0时，可以将当前数字添加到连续数组序列中，否则将连续数组序列
更新为当前数组值**/
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int curMax = 0;
        int curSum = 0;
        int max  = nums[0];

        for(auto &num:nums)
        {
            curMax = (curMax > 0)?(curMax + num):(num);
            max = (curMax > max)?(curMax):(max);
        }

        return max;
    }
};
```

### [1～n 整数中 1 出现的次数](https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

```c++
/**分析找规律，规律基本如下：
1. 考虑当前位为0时，此时前位为high，后位为low，（10234中，当前位为0，即千位，high=1，low=234），那么在
这个位上出现1的次数有这样几种情况：
	- 当前位的范围为[0，high - 1]时，当前位可以始终为1，当前位为1时，共有digit种情况（10234种，即共有
	1000种情况，从1000~1999）。
	- 当前位 = high时，当前位不能为1，所以不存在这种情况当前位还为1的情况。
2. 考虑当前位为1时，此时发生变化的是当前位 = high时，当前位可以为1（11234种，当前位为千位，数值可以为11000
~11234），所以当前位=high时，增加了后位 + 1种当前位为1的情况（000~234共235种情况）
3. 当前位为[2,9]时，与情况2发生的变化是，当当前位取1时，后位的变化范围由[0，low]变为了[0,dight - 1]
**/
class Solution {
public:
    int countDigitOne(int n) {
        int cur = n%10;
        int high = n/10;
        int low = 0;
        long long digit = 1;
        int sum_count = 0;

        while(digit <= n)
        {
            switch(cur)
            {
                case 0:
                {
                    sum_count += high*digit;
                    break;
                }
                case 1:
                {
                    sum_count += high*digit + low + 1;
                    break;
                }
                default:
                {
                    sum_count += (high + 1)*digit;
                    break;
                }
            }
            digit *= 10;
            high = n/digit/10;
            low = n%digit;
            cur = (n/digit)%10;
        }

        return sum_count;
    }
};
```

### [数字序列中某一位的数字](https://leetcode-cn.com/problems/shu-zi-xu-lie-zhong-mou-yi-wei-de-shu-zi-lcof/)

```c++
/**虽然不清楚为什么内存消耗有6.1MB那么大，不过在思路上和其它代码是一样的，找规律实现即可**/
class Solution {
public:
    int findNthDigit(int n) {
        if(n < 10)return n;
        long long boundary_index = 0;
        int begin_index = 0;
        int number_length = 1;
        
        while(boundary_index < n)
        {
            boundary_index = boundary_index + pow(10,number_length - 1)*9*(number_length);
            number_length++;
        }
        number_length--;
        
        begin_index = boundary_index - pow(10,number_length - 1)*9*(number_length) + 1;
        
        int number = pow(10,number_length - 1) + (n - begin_index)/number_length;

        int index = (n - begin_index)%number_length;
		
        string s = std::to_string(number);
        
        return s[index] - '0';
    }
};
```

### [把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

```c++
/**在利用字符串本身的排序特性时，一直在考虑如3和30这种情况，在排序中，3在30前面，但这样拼接出的字符串（330）
却并不是最小的。可以通过利用字符串排序的连续性的快速排序算法，在排序时就比较拼接结果，完成这道题目**/
class Solution {
public:
    string minNumber(vector<int>& nums) {
        vector<string> strs;
        string res;
        for(int i = 0; i < nums.size(); i++)
            strs.push_back(to_string(nums[i]));
        sort(strs.begin(), strs.end(), [](string& x, string& y){ return x + y < y + x; });
        for(int i = 0; i < strs.size(); i++)
            res.append(strs[i]);
        return res;
    }
};
```

### [把数字翻译成字符串](https://leetcode-cn.com/problems/ba-shu-zi-fan-yi-cheng-zi-fu-chuan-lcof/)

```c++
/**类似于青蛙跳台阶**/
class Solution {
public:
    int translateNum(int num) {
        int currVal = 1, prevVal = 0, temp;
        int currPos, prevPos = 0;
        while (num > 0) {
            currPos = num % 10;
            num /= 10;
            if (currPos * 10 + prevPos > 25 || currPos == 0) prevVal = currVal;
            else {
                temp = currVal;
                currVal += prevVal;
                prevVal = temp;
            }
            prevPos = currPos;
        }
        return currVal;
    }
};
```

### [礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)

```c++
/**简单DP，但我赶时间一开始想的DP没想清楚转移方程，直接暴力dfs结果超时，不过dp思路确实还是比较简单**/
class Solution {
public:

    int maxValue(vector<vector<int>>& grid) {
        int row = grid.size();
        int col = grid[0].size();

        vector<int> dp(col, 0);

        for(int i = 0; i < row; i++)
        {
            for(int j = 0; j < col; j++)
            {
                dp[j] = (j == 0)?(dp[j] + grid[i][j]):(grid[i][j] + max(dp[j - 1], dp[j]));
            }
        }

        return dp[col - 1];
    }
};
```

### [最长不含重复字符的子字符串](https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/)

```c++
/**关于我思路正确就是调不出来代码这件事，写动态规划代码最好还是手写一遍，脑推容易出问题**/
class Solution {
public:
    int lengthOfLongestSubstring(string s)
    {
        //这两句话其实没有必要，因为当length==0时，直接返回max_length=0;
        //length==1时，由于letter_table中初始化为-1， 即0 - -1 = 1，也不会出问题。
        if(s.length() < 2)
            return s.length();
        //acall码表共有128种字符，实际申请128就足够了
        int letter_table[150];
        
        //初始化为-1是因为之后要使用end_index - begin_index来计算字符长度，当字符中没有重复字符时
        //直接用index - 0会造成长度减一，所以初始化为-1
        memset(letter_table, -1, 600);
		
        //类似于双指针，begin_index只当前字符上一次出现的下标，end_index为当前下标
        int begin_index = 0, end_index = 0;
        
        //max_length为最大不重复子串长度，pre为以当前字符为结尾往前推的不重复的子串长度
        int max_length = 0, pre = 0;
		
        
        //遍历字符串
        for(end_index = 0; end_index < s.length(); end_index++)
        {
            //更新begin_index
            begin_index = letter_table[s[end_index]];
            //更新字符表的状态（在字符串中上一次出现的位置，未出现则为-1）
            letter_table[s[end_index]] = end_index;
            //以当前字符为结尾往前推的不重复的子串长度
            pre = (pre >= end_index - begin_index)?(end_index - begin_index):(pre + 1);
            //更新max_length
            max_length = max(max_length,pre);
        }

        return max_length;
    }
};
```

### [丑数](https://leetcode-cn.com/problems/chou-shu-lcof/)

```c++
/**动态规划，2，3，5三个数据需要用三个计算通道比较，更新**/
class Solution {
public:
    int nthUglyNumber(int n) {
        int a = 0, b = 0, c = 0;
        int dp[n];
        dp[0] = 1;
        for(int i = 1; i < n; i++) {
            int n2 = dp[a] * 2, n3 = dp[b] * 3, n5 = dp[c] * 5;
            dp[i] = min(min(n2, n3), n5);
            //这里不用if else是为了消除相同的数
            if(dp[i] == n2) a++;
            if(dp[i] == n3) b++;
            if(dp[i] == n5) c++;
        }
        return dp[n - 1];
    }
};
```

### [数组中的逆序对](https://leetcode-cn.com/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

​	利用归并排序的特点，将逆序对分为三部分：

- 分治点左边的逆序对
- 分治点右边的逆序对
- 分治点右边对左边的逆序对

计算左边和右边的逆序对实际上就可以通过分治算法的内涵来完成，因为左边和右边子区间实际就是下一次递归进入的区间，对于分治点右边对左边的逆序对，则通过归并排序的特点和双指针来完成，具体参见题解。

[(3 封私信 / 78 条消息) 树状数组的原理是什么？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/54404092)

[树状数组详解 - Xenny - 博客园 (cnblogs.com)](https://www.cnblogs.com/xenny/p/9739600.html)

```c++
class Solution {
private:
    vector<int> temp;
public:
    int merget_sort(vector<int> &q, int left, int right)
    {
        if(left >= right)
            return 0;
        
        //取分支点
        int mid = (left + right) / 2;

        //返回左子区间和右子区间的逆序对之和
        int ans = merget_sort(q, left, mid) + merget_sort(q, mid + 1, right);

        int k = 0, i = left, j = mid + 1;

        while(i <= mid && j <= right)
        {
            if(q[i] <= q[j])
            {
                temp[k++] = q[i++];
            }
            else
            {
                ans += mid - i + 1;
                temp[k++] = q[j++];
            }
        }

        while(i <= mid)temp[k++] = q[i++];
        while(j <= right)temp[k++] = q[j++];

        for(int i = left, j = 0; i <= right;)
        {
            q[i++] = temp[j++];
        }

        return ans;
    }
    int reversePairs(vector<int>& nums) 
    {
        temp.assign(nums.size(), 0);
        int res = merget_sort(nums, 0, nums.size() - 1);
        return res;
    }
};
```

### [两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/)

```c++
//分析只，当使用如下解法时，当curA、curB到达相同节点时，二者刚好都走过了L + L1 + L2的长度，其中L1，L2为
//两个链表中相同节点前的节点数
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
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA == nullptr || headB == nullptr)
            return nullptr;
        
        ListNode *curA = headA;
        ListNode *curB = headB;
        
        int count = 0;
        while(curA != curB && count < 2)
        {
            curA = curA->next != nullptr?(curA->next):(count++,headB);
            curB = curB->next != nullptr?curB->next:headA;
        }

        if(count == 2)
            return nullptr;
        return curA;
    }
};
```

### [在排序数组中查找数字 I](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

```c++
//也可直接使用vector容器的upper_bound()和lower_bound()函数直接解。
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int index_left = 0,index_right = nums.size() - 1,index;

        while(index_left <= index_right)
        {
            index = (index_left + index_right)/2;

            if(nums[index] == target)
            {
                int front = index - 1;
                int last  = index + 1;
                int res = 1;
                while(front >= index_left && nums[front] == target)
                {
                    front--;
                    res++;
                }

                while(last <= index_right && nums[last] == target)
                {
                    last++;
                    res++;
                }

                return res;
            }
            if(nums[index] < target)
            {
                index_left = index + 1;
            }else
            {
                index_right = index - 1;
            }
        }
        return 0;
    }
};
```

### [0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int l,r,index;
        l = 0,r = nums.size() - 1;

        while(l <= r)
        {
            index = (l + r)/2;
            if(nums[index] > index)
            {
                r = index - 1;
            }
            else if(nums[index] == index)
            {
                l = index + 1;
            }
        }
        return l;
    }
};
```

```c++
/*
假设，你花了点时间，练习了二叉树的三种遍历方式： a. 前序遍历 b. 中序遍历 c. 后续遍历
你也学习了二叉搜索树，深入研究了二叉树搜索树的特性，并且深刻知道二叉搜索树的一个特性：
通过中序遍历所得到的序列，就是有序的。
*/
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
private:
    int count = 0;
    int res = 0;
public:
    int kthLargest(TreeNode* root, int k) {
        if(root != nullptr)
        {
            kthLargest(root->right, k);
            if(++count == k)
                res =  root->val;
            kthLargest(root->left, k);
        }
        return res;
    }
};
```

### [二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

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
    int maxDepth(TreeNode* root) {
        if(root == nullptr)
            return 0;
        return max(maxDepth(root->left),maxDepth(root->right)) + 1;
    }
};

//非递归写法
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) {
            return 0;
        }
        Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
        stack.add(new Pair<>(root, 1));
        int h = 0;
        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();

            h = Math.max(pair.getValue(), h);

            if (pair.getKey().right != null) {
                stack.push(new Pair<>(pair.getKey().right, pair.getValue() + 1));
            }
            if (pair.getKey().left != null) {
                stack.push(new Pair<>(pair.getKey().left, pair.getValue() + 1));
            }
        }
        return h;
    }
}
```

### [平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

```c++
//实际上就是递归计算每个子树是否为平衡二叉树，要想到平衡二叉树的子树也都是平衡二叉树
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
    int depth(TreeNode *root)
    {
        if(root == nullptr)
            return 0;
        return max(depth(root->left),depth(root->right)) + 1;
    }
    bool isBalanced(TreeNode* root) {
        if(root == nullptr)
            return true;
        if(abs(depth(root->left) - depth(root->right)) > 1)
            return false;
        return isBalanced(root->left) && isBalanced(root->right);
    }
};
```

### [数组中数字出现的次数](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

```c++
/**这道题其实不会，一开始容易想到位运算，然后发现对于题目给出的数据来说，全都位运算一遍能够去除那些出现过
两次的数据的信息，但如何将两个只出现过一次的数据提取出来？又想到将数据分组异或，分组要符合以下条件：
1. 相同的数必须分到同一组，保证在每组异或完成后能够剔除出现过两次的数据信息
2. 两个只出现过一次的数据必须分到不同的组
如此，问题转换为了如何分组，由于所有数据异或之后的结果中，其中为1的数据位必然两个只出现过一次的数据中的一个
数据有，故而，为方便起见，利用lowbit得到异或结果的为1的最低位的数据作为mask对源数据分组分别异或即可。
**/
class Solution {
public:
    vector<int> singleNumbers(vector<int>& nums) {
        int m = 0;
        
        for(auto &i: nums)
        {
            m = m^i;
        }
        
        int mask = m&(-m);

        vector<int> res(2, 0);
        
        for(auto &i:nums)
        {
            if(i & mask)
                res[0] = res[0]^i;
            else
                res[1] = res[1]^i;
        }

        return res;
    }
};
```

### [数组中数字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

```c++
/**简明解法**/
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int temp[32] = {0};

        for(auto &num:nums)
            for(int i  = 0; i < 32; i++)
                temp[i] += (num>>i & 1)?1:0;

        int res = 0;
        for(int i = 0; i < 32 ; i++)
            if(temp[i] % 3 != 0)
                res |= 1<<i;

        return res;
    }
};

/**状态机解法**/
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ones = 0, twos = 0;
        for(auto num : nums){
            ones = ones ^ num & ~twos;
            twos = twos ^ num & ~ones;
        }
        return ones;
    }
};
```

### [和为s的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)

```c++
/**直接想到双指针，但没想到一个从前往后，一个从后往前，以下是变形的双指针其实，将两个指针都变成了从前向后**/
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int l = 0,r = 1;

        vector<int> temp,res;

        for(auto &num:nums)
            temp.push_back(target - num);

        int len = nums.size();

        for(int i = 0,j = len - 1; i < len && j > 0;)
        {
            if(temp[i] > nums[j])
                i++;
            else if(temp[i] < nums[j])
                j--;
            else
            {
                res.push_back(nums[j]);
                res.push_back(nums[i]);
                break;
            }

        }
        return res;
    }
};

/**正常双指针解法**/
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int l = 0; int r = nums.size() - 1;
        while (l < r) {
            if (target == nums[l] + nums[r]) {
                return vector<int>{nums[l], nums[r]};
            }
            else if (target > nums[l] + nums[r]) {    
                l++;
            }
            else if (target < nums[l] + nums[r]) {    
                r--;
            }
        }
        return vector<int>();
    }
};

```

### [和为s的连续正数序列](https://leetcode-cn.com/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/)

```c++
class Solution {
public:
    vector<vector<int>> findContinuousSequence(int target) 
    {
        vector<vector<int>> res;
        int maxLen =  sqrt(2*target) + 1;

        int left = 0;
        for(int len = maxLen; len >= 2; len--)
        {
            if((target*2)%len == 0)
            {
                vector<int> tmp;
                left = target*2/len - len + 1;
                if(left%2 == 0 && left > 0)
                {
                    left /= 2;
                    for(int i = 0; i < len; i++)
                    {
                        tmp.push_back(left + i);
                    }
                    res.emplace_back(tmp);
                }
            }
        }

        return res;
    }
};
```

### [翻转单词顺序](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/)

```c++
class Solution {
public:
    string reverseWords(string s) {
        int len = s.length();
        int left, right;
        string res;

        for(int i = len - 1; i >= 0; i--)
        {
            if(s[i] == ' ')
                continue;
            else
            {
                right = i;
                while(i >= 0 && s[i] != ' '){i--;}
                left = i + 1;

                res += s.substr(left, right - left + 1);
                res += ' ';
            }
        }

        return res.substr(0, res.length() - 1);
    }
};
```

### [左旋转字符串](https://leetcode-cn.com/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

```c++
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        if(n == 0)
            return s;
        int len = s.length();

        return s.substr(n, len - n) + s.substr(0, n);
    }
};
```

### [滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

```c++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {

        if(nums.empty())
            return vector<int>();

        vector<int> res;
        int len = nums.size();
        int curMax,curMaxIndex;
        std::vector<int>::iterator begin = nums.begin();

        std::vector<int>::iterator curLarget = std::max_element(begin , begin + k);
        
        curMaxIndex= curLarget - begin;
        curMax = *curLarget;
        res.push_back(curMax);

        for(int i = 1; i <= len - k; i++)
        {
            if(nums[i + k - 1] >= curMax)
            {
                curMax = nums[i + k - 1];
                curMaxIndex = i + k - 1;
            }else if(nums[i + k - 1] < curMax && curMaxIndex < i)
            {
                curLarget = std::max_element(begin + i, begin + i + k);
                curMax = *curLarget;
                curMaxIndex = curLarget - begin;
            }

            res.push_back(curMax);
        }

        return res;
    }
};

//使用列表数据结构，在插入时保持单调性，每次窗口滑动后去列表首元素即可
class Solution
{
public:
    vector<int> maxSlidingWindow(vector<int> &nums, int k)
    {
        vector<int> res;
        int n = nums.size();
        deque<int> d;
        k--; // 只是方便后面用
        for (int i = 0; i < n; i++)
        {
            // 删除不在窗口内的元素，无论它多大都没用了
            if (!d.empty() && d.front() < i - k)
            {
                d.pop_front();
            }

            // 添加元素，并保持队列是单调递减的
            while (!d.empty() && nums[d.back()] < nums[i])
            {
                d.pop_back();
            }
            d.push_back(i);

            // 队列首部对应的元素为本窗口最大值，因为咱们一直保持队列是单调递减的嘛
            if (i >= k)
            {
                res.push_back(nums[d.front()]);
            }
        }
        return res;
    }
};

```

### [队列的最大值](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/)

```c++
class MaxQueue {
    queue<int> que;
    deque<int> deq;
public:
    MaxQueue() { }
    int max_value() {
        return deq.empty() ? -1 : deq.front();
    }
    void push_back(int value) {
        que.push(value);
        while(!deq.empty() && deq.back() < value)
            deq.pop_back();
        deq.push_back(value);
    }
    int pop_front() {
        if(que.empty()) return -1;
        int val = que.front();
        if(val == deq.front())
            deq.pop_front();
        que.pop();
        return val;
    }
};
```

### [n个骰子的点数](https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/)

```c++
/**思路上较为简单，但在实现上走了弯路，想着使用两个vector相互更新，在dp的写法上不够熟练**/
class Solution {
public:
    vector<double> dicesProbability(int n) {
        vector<vector<int>> dp(15,vector<int>(70,0));
        for(int i=1;i<=6;i++)   {dp[1][i]++;}
        for(int i=2;i<=n;i++)
        {
            for(int j=i;j<=i*6;j++)  //j为点数和，i个点数最小和为i,最大为i*6
            {
                for(int k=1;k<=6;k++)
                {
                    if(j<=k)    {break;}
                    dp[i][j]+=dp[i-1][j-k];
                }
            }
        }
        vector<double> ret;
        double count=6.0;
        for(int i=1;i<n;i++)    {count*=6.0;}
        for(int i=n;i<=n*6;i++)
        {
            ret.push_back(double(dp[n][i])/count);
        }
        return ret;
    }
};
```

### [扑克牌中的顺子](https://leetcode-cn.com/problems/bu-ke-pai-zhong-de-shun-zi-lcof/)

```c++
/**STL迭代器和容器使用不熟练，问题分析不够具体彻底**/
class Solution {
public:
    bool isStraight(vector<int>& nums) {
        int minIndex = 0;
        sort(nums.begin(), nums.end());
        for(int i = 0; i < nums.size() - 1; ++i)
        {
            if(nums[i] == 0)    ++minIndex;   
            else if(nums[i] == nums[i+1])    return false;   //除开为0的情况，存在重复时
        }
        return nums[4] - nums[minIndex] <= 4;
    }
};
```

### [圆圈中最后剩下的数字](https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/)

```c++
/**约瑟夫环问题，其实是简单dp，关键在于两个状态间的更新策略，选择从当前位置开始数数最后留下的数字的位置作为
更新传递的信息来更新，避免了两个状态间的数字、位置不连续的问题**/
class Solution {
public:
    int f(int n, int m)
    {
        if(n == 1)
            return 0;
        int x = f(n - 1, m);

        return (x + m)%n;
    }

    int lastRemaining(int n, int m) {
        return f(n, m);
    }
    
};
```

### [股票的最大利润](https://leetcode-cn.com/problems/gu-piao-de-zui-da-li-run-lcof/)

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0;
        int curMin,curProfit = 0;
        curMin = prices[0];

        for(auto price:prices)
        {
            if(price - curMin > curProfit)
                curProfit = price - curMin;
            if(price < curMin)
                curMin = price;
        }

        return curProfit;
    }
};
```

### [求1+2+…+n](https://leetcode-cn.com/problems/qiu-12n-lcof/)

```c++
/**运算符短路**/
class Solution {
public:
    int sumNums(int n) {
        n && (n += sumNums(n-1));
        return n;
    }
};

/**快速乘**/
class Solution {
public:
    int sumNums(int n) {
        int ans = 0, A = n, B = n + 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        (B & 1) && (ans += A);
        A <<= 1;
        B >>= 1;

        return ans >> 1;
    }
};

```

### [不用加减乘除做加法](https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/)

```c++
/**内联汇编，骚东西**/
class Solution {
public:
    int add(int a, int b) {
        int ans;
        __asm__ __volatile__(
            "add %%ebx,%%eax"
            :"=a"(ans)
            :"a"(a),"b"(b)
        );
        return ans;
    }
};

/**位运算**/
class Solution {
    public int add(int a, int b) {
        while(b != 0) { // 当进位为 0 时跳出
            int c = (a & b) << 1;  // c = 进位
            a ^= b; // a = 非进位和
            b = c; // b = 进位
        }
        return a;
    }
}
```

### [构建乘积数组](https://leetcode-cn.com/problems/gou-jian-cheng-ji-shu-zu-lcof/)

```c++
//先从左至右获得左边数的乘积，再从右至左获得右边数的乘积
class Solution {
public:
    vector<int> constructArr(vector<int>& a) {
        int len = a.size();
        vector<int> res(len, 1);

        int cur = 1;
        for(int i = 0; i < len; i++)
        {
            res[i] = cur;
            cur *=  a[i]; 
        }

        cur = 1;
        for(int i = a.size() - 1; i >= 0; i--)
        {
            res[i] *= cur;
            cur *= a[i];
        }

        return res;
    }
};
```

```c++
//题目比较无聊抄的题解
class Solution {
public:
    int strToInt(string str) {
        /*
        @tempRet:用于暂存转换得来的数
        @flag:记录是否已经出现了数字,已经出现为true
        @simbol:记录+-符号,0为未出现+-,1为出现+,2为出现-
        */
        double tempRet=0,simbol=0;
        bool flag=false;
        for(char& c:str){
            if(c<='9'&&c>='0'){
                flag=true;
                tempRet=tempRet*10+c-'0';
            }
            else if(c==' '||c=='+'||c=='-'){
                //前面已经出现数字或+-
                if(flag||simbol>0) break;
                if(c=='+'||c=='-') simbol=c=='+'?1:2;
            }
            else{
                break;
            }
        }
        tempRet=(simbol==2)?-tempRet:tempRet;
        int ret=(tempRet<=INT_MAX&&tempRet>=INT_MIN)?tempRet:
                ((tempRet>INT_MAX)?INT_MAX:INT_MIN);
        return ret;
    }
};

//这份内存消耗最少的代码的判断越界的方法值得参考
class Solution {
public:
    int strToInt(string str) {
        int res = 0, n = str.size(), i = 0;
        while (str[i] == ' ') {
            ++i;
        }
        bool isNegative = false;
        if (i < n) {
            if (str[i] == '-') {
                isNegative = true;
                ++i;
            } 
            else if (str[i] == '+') {
                ++i;
            }
        }

        while (i < n && (str[i] >= '0' && str[i] <= '9')) {
            int x = str[i] - '0';
            if (isNegative == true) {
                x = -x;
            }
            // 溢出
            if (res > INT_MAX / 10 || (res == INT_MAX / 10 && x > 7)) {
                return INT_MAX;
            } 
            if (res < INT_MIN / 10 || (res == INT_MIN / 10 && x < -8)) {
                return INT_MIN;
            }
            res = res * 10 + x;
            ++i;
        }
        return res;
    }
};
```

### [二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

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
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root->val<p->val&&root->val<q->val){
            return lowestCommonAncestor(root->right,p,q);
        }
        if(root->val>p->val&&root->val>q->val){
            return lowestCommonAncestor(root->left,p,q);
        }
        return root;
    }
};
```

### [二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

```c++
//本题中有curK == 2和res == nullptr来防止res的错误更新，在后序遍历之后如何更新数据需要谨慎
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
private:
    int k;
    TreeNode *res;
void dfs(TreeNode* root, TreeNode* p, TreeNode* q)
{
    if(root == nullptr || k == 0)
        return;
    
    int curK  = k;
    
    dfs(root->left, p ,q);
    dfs(root->right, p ,q);

    if(root->val == p->val || root->val == q->val)
        k--;
    
    if(curK == 2 && k == 0 && res == nullptr)
        res = root;

}
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        k = 2;
        res = nullptr;

        dfs(root, p , q);
        return res; 
    }
};
```



