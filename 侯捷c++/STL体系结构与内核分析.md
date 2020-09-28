# 1. 认识headers，版本，重要资源

* C++的标准库的header files不带.h。例如： `#include<vector>`
* 新式C header files 不带有附件名。例如 `#include<cstdio>`

常用的两个网站：
1. http://cplusplus.com/
2. https://en.cppreference.com/w/
3. https://gcc.gnu.org/

STL有6大部件。
* 容器   (Containers)
* 分配器 (Allocator)
* 算法   (Algorithms)
* 迭代器 (Iterators)
* 适配器 (Container Adapters)
* 仿函数  (Functor Adapters)
![](picture/2020-09-16-15-00-24.png)


> 前闭后开 区间 [ )
> 
![](picture/2020-09-16-16-02-38.png)


对于 Array 而言，申请创建的时候就必须确定占用内存的大小；
对于 Vector 而言，可以在尾部进行扩张，但是删除元素的时候，由于其内存分布为连续的，因此必须将后的数据都拷贝移动一遍。
对于Deque是可以在首和尾部都进行扩张，连续内存。
对于List是双向链表。
对于Forward-List是首部确定的单向链表
![](picture/2020-09-27-09-27-57.png)



# 2. 测试容器
## 2.1 array
1. 注意 array 只能使用确定的大小，需要在申请 array空间的时候就提供

![](picture/2020-09-28-14-23-55.png)
```cpp
#include <vector>
#include <algorithm>
#include <functional>
#include <iostream>
#include <array>
using namespace std;

long get_a_target_long()
{
    long target=0;
    cout<<"taget (0~"<<RAND_MAX<<");";
    cin>>target;
    return target;
}

string get_a_target_string()
{
    long target=0;
    char buf[10];
    cout<<"target (0~"<<RAND_MAX<<"):";
    cin>>target;
    snprintf(buf,10,"%d",target);   //将long型转化为 字符串
    return string(buf);
}

int compareLongs(const void* a,const void* b)
{
    return (*(long*)a - *(long*)b);
}

int compareStrings(const void* a,const void* b)
{
    if (*(string*)a > *(string*)b)
        return 1;
    else if (*(string*)a < *(string*)b)
        return -1;
    else
        return 0;
}

int main(int argc, char** argv){
    const  int ASIZE=500000;
    cout<<"\ntest_array()....... \n";
    array<long,ASIZE> c{};

    clock_t timeStart = clock();
    for(long i=0;i<ASIZE;i++){
        c[i] = rand();
    }

    qsort(c.data(),ASIZE,sizeof(long),compareLongs);
    cout<< "milli-seconds: "<<(clock()-timeStart)<<endl;
    cout<<"array.size()= "<<c.size()<<endl;
    cout<<"array.front()= "<<c.front()<<endl;   //第一个
    cout<<"array.back()= "<<c.back()<<endl; //最后一个
    cout<<"array.data()= "<<c.data()<<endl; //返回这个数组在内存里面的起点 地址

    long target = get_a_target_long();
    timeStart = clock();    //毫秒
    qsort(c.data(),ASIZE,sizeof(long),compareLongs);    //快排,
    long* PItem = (long*)bsearch(&target,(c.data()),ASIZE,sizeof(long),compareLongs);   //二分查找法 必须是有序的
    cout<<"qsort() + bsearch(),milli-seconds: "<< (clock()-timeStart)<<endl;
    if(PItem != NULL)
        cout<<"found, "<< *PItem<<endl;
    else
        cout<<"not found! "<<endl;
}
```
## 2.2 vector
1. vector只能向后扩展，因此只有 push_back，并且是连续的内存。
2. vecotr的空间增长不是线性的，大多是是 两倍增长。
3. vector有时候不能在原地成长两倍，必须在内存空间中找个一个合适的位置，然后整体搬迁过去，

![](picture/2020-09-28-14-23-40.png)

```cpp
#include <vector>
#include <algorithm>
#include <functional>
#include <iostream>
#include <array>
using namespace std;

long get_a_target_long()
{
    long target=0;
    cout<<"taget (0~"<<RAND_MAX<<");";
    cin>>target;
    return target;
}

int compareLongs(const void* a,const void* b)
{
    return (*(long*)a - *(long*)b);
}

int compareStrings(const void* a,const void* b)
{
    if (*(string*)a > *(string*)b)
        return 1;
    else if (*(string*)a < *(string*)b)
        return -1;
    else
        return 0;
}

namespace jjo2{
    void test(long &value){
        cout<<"\ntest_vector().......\n";
        vector<string> c;
        char buf[10];
        auto timeStart  =clock();
        for(long i =0;i<value;++i)
        {
            try{
                snprintf(buf,10,"%d",rand());
                c.push_back(string(buf));
            }
            catch (exception& p) {
                cout<<"i="<<i<<" "<<p.what()<<endl; //避免内存不够用的情形，会出现 std:: bad_alloc 异常
                abort();    //退出程序
            }
        }
        cout<< "milli-seconds: "<<(clock()-timeStart)<<endl;
        cout<<"vector.size()= "<<c.size()<<endl;    //真正元素的个数
        cout<<"vector.front()= "<<c.front()<<endl;   //第一个
        cout<<"vector.back()= "<<c.back()<<endl; //最后一个
        cout<<"vector.data()= "<<c.data()<<endl; //返回这个数组在内存里面的起点 地址
        cout<<"vector.capacity= "<<c.capacity()<<endl;  //空间的大小
    }
}

int main(int argc, char** argv){
    long a = 50000;
    jjo2::test(a);
}
```

## 2.3 list
1. List可以双向扩张，不是连续的内存空间，双向链表
2. List不需要连续内存

![](picture/2020-09-28-14-24-17.png)

```cpp
#include <vector>
#include <algorithm>
#include <functional>
#include <iostream>
#include <array>
#include <list>
#include <typeinfo>

using namespace std;

long get_a_target_long()
{
    long target=0;
    cout<<"taget (0~"<<RAND_MAX<<");";
    cin>>target;
    return target;
}

string get_a_target_string()
{
    long target=0;
    char buf[10];
    cout<<"target (0~"<<RAND_MAX<<")";
    cin>>target;
    snprintf(buf,10,"%d",target);
    return string (buf);
}

int compareLongs(const void* a,const void* b)
{
    return (*(long*)a - *(long*)b);
}

int compareStrings(const void* a,const void* b)
{
    if (*(string*)a > *(string*)b)
        return 1;
    else if (*(string*)a < *(string*)b)
        return -1;
    else
        return 0;
}

namespace jj03{
    void test(long & value){
        cout<<"\n test_list() ... ... \n";
        list<string> c;
        char buf[10];
        clock_t timeStart = clock();
        for(long i=0;i<value;i++){
            try {
                snprintf(buf,10,"%d",rand());
                c.push_back(buf);
            }
            catch (exception& p) {
                cout<<"i="<<i<<" "<<p.what()<<endl;
                abort();
            }
        }
        cout<< "milli-seconds: "<<(clock()-timeStart)<<endl;
        cout<<"list.size()= "<<c.size()<<endl;    //元素的个数
        cout<<"list.max_size()= "<<c.max_size()<<endl;  //最大放置的元素个数
        cout<<"list.front()= "<<c.front()<<endl;   //第一个
        cout<<"list.back()= "<<c.back()<<endl; //最后一个

        string target = get_a_target_string();
        timeStart = clock();
//        _List_iterator<basic_string<char>> PItem = ::find(c.begin(), c.end(), target);
        auto PItem = ::find(c.begin(), c.end(), target);

        cout<<"::find(), milli-seconds : "<<(clock()-timeStart)<<endl;
        cout<<typeid(PItem).name()<<endl;
        if(PItem != c.end())
            cout<<"found, "<<*PItem<<endl;
        else
            cout<<"not found!"<<endl;
        timeStart = clock();
        c.sort();   //标准库有一个sort,容器本身也有一个 sort; 用容器自己的sort更好
        cout<<"c.sort(), milli-seconds: "<<(clock()-timeStart)<<endl;
    }
}

int main(int argc, char** argv){
    long a = 1000000;
    jj03::test(a);
}
```
## 2.4 forward——list
1. 单向链表，只提供 push_front
2.  

![](picture/2020-09-28-15-14-40.png)

```cpp
#include <vector>
#include <algorithm>
#include <functional>
#include <iostream>
#include <array>
#include <list>
#include <typeinfo>
#include <forward_list>

using namespace std;

long get_a_target_long()
{
    long target=0;
    cout<<"taget (0~"<<RAND_MAX<<");";
    cin>>target;
    return target;
}

string get_a_target_string()
{
    long target=0;
    char buf[10];
    cout<<"target (0~"<<RAND_MAX<<")";
    cin>>target;
    snprintf(buf,10,"%d",target);
    return string (buf);
}

int compareLongs(const void* a,const void* b)
{
    return (*(long*)a - *(long*)b);
}

int compareStrings(const void* a,const void* b)
{
    if (*(string*)a > *(string*)b)
        return 1;
    else if (*(string*)a < *(string*)b)
        return -1;
    else
        return 0;
}

namespace jj04{
    void test(long & value){
        cout<<"\n test_list() ... ... \n";
        forward_list<string> c;
        char buf[10];
        clock_t timeStart = clock();
        for(long i=0;i<value;i++){
            try {
                snprintf(buf,10,"%d",rand());
                c.push_front(buf);
            }
            catch (exception& p) {
                cout<<"i="<<i<<" "<<p.what()<<endl;
                abort();
            }
        }
        cout<< "milli-seconds: "<<(clock()-timeStart)<<endl;
        cout<<"list.max_size()= "<<c.max_size()<<endl;  //最大放置的元素个数
        cout<<"list.front()= "<<c.front()<<endl;   //第一个

        string target = get_a_target_string();
        timeStart = clock();
//        _List_iterator<basic_string<char>> PItem = ::find(c.begin(), c.end(), target);
        auto PItem = ::find(c.begin(), c.end(), target);

        cout<<"::find(), milli-seconds : "<<(clock()-timeStart)<<endl;
        cout<<typeid(PItem).name()<<endl;
        if(PItem != c.end())
            cout<<"found, "<<*PItem<<endl;
        else
            cout<<"not found!"<<endl;
        timeStart = clock();
        c.sort();   //标准库有一个sort,容器本身也有一个 sort; 用容器自己的sort更好
        cout<<"c.sort(), milli-seconds: "<<(clock()-timeStart)<<endl;
    }
}

int main(int argc, char** argv){
    long a = 1000000;
    jj04::test(a);
}
```

## 2.5 deque
1. 一个容器占用内存以后不能在原地扩充，比如 vector需要整体搬迁到其他的位置进行扩张。
2. Deque是分段有序，但是让使用者感觉是 整体有序，每次扩充 一个 Buffer, 


 ![](picture/2020-09-28-15-20-49.png)


![](picture/2020-09-28-15-21-51.png)

## 2.6 stack 和 queue
1. 一个Deque包含了 stack,和queue.因此 Deque 直接被复用了。

![](picture/2020-09-28-15-32-58.png)

![](picture/2020-09-28-15-33-56.png)










