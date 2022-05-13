# PTA刷题

**build for exercise in PTA**

## PTA1010

```c++
/**
 * 这道题开始做失分的点在于两个超时和一个答案错误，超时这个我通过预先以20的步长不断向前探索找到第一个大于源值（source_num）或者
 * 超过long long表示范围的进制，然后再在这个20的步长中搜索合适的进制，这样仍然会超时，可能调整步长大一些会好？至于答案错误的那个，
 * 由于没有用例我也不知道是什么情况。
 * 不过本题的解法还是比较朴实，二分，再注意long long的表示范围即可。
 **/
 
#include <iostream>
#include <string>
#include <cstdlib>
#include <algorithm>

using namespace std;

long long string_to_longlong(string source_string, long long radix)
{
    long long num_source = 0, incre_pow_num = 1;

    reverse(source_string.begin(), source_string.end());

    for(auto &c : source_string)
    {
        num_source += ((isdigit(c))?(c - '0'):(10 + c - 'a'))*incre_pow_num;
        incre_pow_num *= radix;
    }

    return num_source;
}
int main()
{
    string n[2];
    int tag,radix;

    cin>>n[0]>>n[1]>>tag>>radix;

    long long source_num = string_to_longlong(n[tag != 1], radix);
    long long target_num = string_to_longlong(n[tag == 1], 1);

    long long max_radix = source_num + 1;

    int max_num_in_source =  *max_element(n[tag == 1].begin(), n[tag == 1].end());
    long long min_radix = max_num_in_source < '9'?(max_num_in_source - '0' + 1):(max_num_in_source - 'a' + 11);

    long long mid;
    long long res = -1;

    while(min_radix <= max_radix)
    {
        mid = (max_radix + min_radix)>>1;
        target_num = string_to_longlong(n[tag == 1], mid);

        if(target_num == source_num)
        {
            res = mid;
            break;
        }
        else if(target_num < 0 || target_num > source_num)
            max_radix = mid - 1;
        else
            min_radix = mid + 1;
        
    }
    if(res == -1)
        cout<<"Impossible"<<endl;
    else
        cout<<res<<endl;

    return 0;
}
```

## PTA1011

```c++
/*md智障题目，极其直白*/
#include <iostream>
#include <algorithm>
#include <cmath>

using namespace std;

int get_max_index(double *nums)
{
    for(int i = 0; i < 3;i++)
    {
        if(nums[i] >= nums[(i + 1) % 3] && nums[i] >= nums[(i + 2) % 3])
            return i;
    }
    return 0;
}
int main()
{
    double sum = 1;
    double nums[3];
    int max_index = 0;

    char strs[3] = {'W', 'T', 'L'};
    char res_strs[3];

    int count = 0;

    while(count < 3)
    {
        scanf("%lf %lf %lf", nums, nums + 1, nums + 2);
        max_index = get_max_index(nums);
        sum *= nums[max_index];

        res_strs[count++] = strs[max_index];
    }

    for(int i = 0; i < 3; i++)
        printf("%c ", res_strs[i]);

    printf("%.2lf", (sum*0.65 - 1) * 2);

    return 0;
}
```

## PTA1012

```c++
/*其实我还是不太适应使用vector这些类中封装好的一些如max_element或者find函数，尤其是在循环里使用，这在有些时候确实是会限制思路。
在一开始我的思路大致如下，也写了一些：
    1. 讲信息存放在一个二维矩阵当中，设计一个函数，函数中使用插入排序来对每项成绩进行排序，由于相邻同学的相同项成绩在矩阵中的距离是等距的，
    这个过程可以轻易地写成循环，这样我们就能在一个循环中完成对所有项的成绩排序。
    2. 在实现上，由于为了方便插入排序，使用了list结构，list结构中存放地址，这样我们在使用一个成绩时，不可避免地使用到了指针的指针这样的形式。
    3. 在获得每个同学在所有项的排序后，还需要使用一个循环，找出其中优先级最高的项及其排序，并且在最后输出的时候还需要顺序搜索id，这并非我的意愿。
    4. 所以写着写着我就不想这么写了......
下面的代码思路是网上抄的，这个代码在实现上用了大量的max_element和find函数，虽然思路很好，尤其是用distance定位相同元素的这种操作和rank优先级这样的写法，
但这些也就是唯一地优点了。

*/
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
    int N, M;
    scanf("%d %d", &N, &M);

    vector<int> id(N), a(N), c(N), m(N), e(N), sorted(N);

    for(int i = 0 ;i < N; i++)
    {
        scanf("%d %d %d %d", &id[i], &c[i], &m[i], &e[i]);
        a[i] = (c[i] + m[i] +e[i]) / 3;
    }

    vector<int> *vec;

    for(int pri = 0; pri < 4; pri++)
    {
        switch (pri)
        {
            case 0:
                vec = &a;
                break;
            case 1:
                vec = &c;
                break;
            case 2:
                vec = &m;
                break;
            case 3:
                vec = &e;
                break;
        }

        int rank = 1, last_score = 0;
        for(int count = 1; count <= N; count++)
        {
            vector<int>::iterator max_it = max_element(vec->begin(), vec->end());
            int i = distance(vec->begin(), max_it);

            if(last_score != *max_it)
                rank = count;

            last_score = *max_it;
            *max_it = -1;

            if(pri == 0)
                sorted[i] = 100 * rank;
            else
                sorted[i] = 100 * rank + pri < sorted[i] ? (100 * rank + pri) : (sorted[i]);
        }
    }

    int cur_id, dis;
    vector<int>::iterator id_index;
    char name[5] = {'A', 'C', 'M', 'E'};

    for(int i = 0; i < M; i++)
    {
        if(i != 0)
            printf("\n");

        scanf("%d", &cur_id);
        id_index = find(id.begin(), id.end(), cur_id);

        if(id_index != id.end())
        {
            dis = distance(id.begin(), id_index);
            int rk = sorted[dis] / 100;
            int pri = sorted[dis] % 10;
            printf("%d %c", rk, name[pri]);
        }
        else
            printf("N/A");
    }

    return 0;
}
```

## PTA1013

```c++
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cmath>
#include <list>

using namespace std;

int map[1001][1001] = {0};
int been_connected[1001] = {0};
int N, M, K;

void dfs(int root)
{
    been_connected[root] = true;

    for(int i = 1; i <= N; i++)
    {
        if(!been_connected[i] && map[root][i] > 0)
            dfs(i);
    }

}

int main()
{
    cin>>N>>M>>K;

    int st, ed;
    for(int i = 1; i <= N; i++)
        map[i][i] = 1;

    for(int i = 0; i < M; i++)
    {
        cin>>st>>ed;
        map[st][ed] = map[ed][st] = 1;
    }

    int lost_city;

    for(int i = 0; i < K; i++)
    {
        if(i != 0)
        {
            for(int j = 1; j <= N; j++)
            {
                map[j][lost_city] += 1;
                map[lost_city][j] += 1;
                been_connected[j] = false;
            }
        }

        cin>>lost_city;

        for(int j = 1; j <= N; j++)
        {
            map[j][lost_city] -= 1;
            map[lost_city][j] -= 1;
        }

        int count = -1;

        for(int j = 1 ; j <= N; j++)
        {
            if(j != lost_city && !been_connected[j])
            {
                dfs(j);
                count++;
            }
        }
        
        cout<<count<<endl;

    }
    return 0;
}
```

## PTA1014

```c++
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cmath>
#include <cstdio>

using namespace std;

typedef struct customer_
{
    int index;
    int cost_time;
}customer;

int main()
{
    int n, m, k, q;
    cin>>n>>m>>k>>q;

    vector<vector<int>> customer_list(n + 1);
    vector<vector<int>> customer_time_list(n + 1);

    vector<int> time_list(n, -1000);
    vector<int>::iterator min_index_iterator;

    for(int i = 0; i < n; i++)
        customer_time_list[i].push_back(0);
    
    int cost_time = 0;
    for(int i = 1;i <= k; i++)
    {
        cin>>cost_time;

        min_index_iterator = min_element(time_list.begin(), time_list.end());

        int customer_list_index = distance(time_list.begin(), min_index_iterator);

        int cur_time = (*(customer_time_list[customer_list_index].end() - 1) + cost_time);

        customer_list[customer_list_index].push_back(i);
        customer_time_list[customer_list_index].push_back(cur_time);

        *min_index_iterator = (customer_list[customer_list_index].size() < m)?(-1000 + customer_list[customer_list_index].size()):(cur_time - cost_time);

    }

    int customer_index;
    vector<int>::iterator customer_index_iterator;
    for(int i = 0; i < q; i++)
    {
        cin>>customer_index;
        for(int i = 0; i < n; i++)
        {
            customer_index_iterator = find(customer_list[i].begin(), customer_list[i].end(), customer_index);
            if(customer_index_iterator != customer_list[i].end())
            {
                int time = customer_time_list[i][distance(customer_list[i].begin(), customer_index_iterator)];

                if(time >= 540)
                    cout<<"Sorry"<<endl;
                else
                {
                    time = customer_time_list[i][distance(customer_list[i].begin(), customer_index_iterator) + 1];
                    printf("%02d:%02d\n", 8 + time/60, time%60);
                }
                break;
            }
        }
    }

    return 0;
}
```

## PTA1015

```c++
//这题阴间题意，没看懂，找了题解才懂，意思是要在特定进制下reverse后再转回10进制判断是否为素数
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cmath>

using namespace std;

#define MAX_NUM 10000000

bool isPrime[ MAX_NUM + 10 ];

void make_prime_table()
{
    for(int i = 2; i <= MAX_NUM; i++)
        isPrime[i] = 1;

    for(int i = 2; i <= MAX_NUM; i++){
        if( isPrime[i] )
            for(int j = 2 * i; j <= MAX_NUM; j += i)
                isPrime[j] = 0;
    }
}

int reverse(int n,  int d)
{
    int sum = 0;
    int nums[100];
    
    int cnt = 0;
    while(n > 0)
    {
        nums[cnt++] = n % d;
        n /= d; 
    }

    for(int i = 0; i < cnt; i++)
    {
        sum = nums[i] + d*sum;
    }

    return sum;
}

int main()
{
    int n,  d;

    make_prime_table();

    while(true)
    {
        scanf("%d", &n);
        if(n < 0)
            break;
        else
        {
            scanf("%d", &d);

            if(isPrime[n] && isPrime[reverse(n, d)])
                printf("Yes\n");
            else
                printf("No\n");

        }
    }
    return 0;   
}
```

## PTA1016

```c++
//吃大亏，没看单位，一直写错。其实以下的实现思路也是我一开始想的，但觉得再用一个map来分很多与就没这么写，但最错误的在于计算花费的时候的方式
//没有像这样选取一个共同的起点，就硬算，就没了。
//还有当牵涉到下标越界的问题时，可以考虑先确定一个下表组合的后面的下标，再往前遍历元素。

#include <iostream>
#include <vector>
#include <string>
#include <algorithm>
#include <cmath>
#include <map>

using namespace std;

#define OFF_LINE 0
#define ON_LINE 1

typedef struct reocord_
{
    bool is_used;
    string name;
    int month;
    int day;
    int hour;
    int minute;
    int time;
    int status;
}Record;

float rate[25] = {0};

bool compare(Record &a, Record &b)
{
    if(a.name < b.name)
        return true;
    else if(a.name == b.name)
    {
        if(a.time < b.time)
            return true;
    }

    return false;
}

double cal(Record &r)
{
    double total = rate[r.hour]*r.minute+rate[24]*60*(r.day - 1);

    for(int i=0;i<r.hour;i++){
		total +=rate[i]*60;
	}

    return total/100.0;
}

int main()
{
    for(int i = 0; i < 24; i++)
    {
        cin>>rate[i];
        rate[24] += rate[i];
    }

    int record_n;
    cin>>record_n;

    vector<Record> records;

    Record cur_record;
    char c_str_name[30], c_str_status[30];

    for(int i = 0 ; i < record_n; i++)
    {
        scanf("%s %d:%d:%d:%d %s", &c_str_name, &cur_record.month, &cur_record.day, &cur_record.hour, &cur_record.minute, &c_str_status);

        cur_record.time = cur_record.day*24*60 + cur_record.hour*60 + cur_record.minute;
        cur_record.name = string(c_str_name);
        cur_record.is_used = false;
        cur_record.status = (string(c_str_status) == "on-line")?(ON_LINE):(OFF_LINE);
        
        records.push_back(cur_record);
    }

    sort(records.begin(), records.end(), compare);

    map<string, vector<Record>> customer;

    for(int i = 1; i < record_n; i++)
    {
        if(records[i - 1].name == records[i].name && records[i - 1].status == ON_LINE && records[i].status == OFF_LINE)
        {
            customer[records[i - 1].name].push_back(records[i - 1]);
            customer[records[i].name].push_back(records[i]);
        }
    }

    for(auto &it:customer)
    {
        vector<Record> temp = it.second;
        printf("%s %02d\n", it.first.c_str(), temp[0].month);

        double total = 0;

        for(int i=1;i<temp.size();i+=2){
			double t = cal(temp[i]) - cal(temp[i-1]);
			printf("%02d:%02d:%02d %02d:%02d:%02d %d $%.2f\n",temp[i-1].day,temp[i-1].hour,temp[i-1].minute,temp[i].day,temp[i].hour,temp[i].minute,temp[i].time - temp[i-1].time,t);
			total+=t; 
		}   

        printf("Total amount: $%.2f\n",total);
    }
    return 0;
}
```

