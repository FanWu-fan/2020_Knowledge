# 1. c++面向对象高级开发
## 1.1 complex
```c++
main.cpp
#include <iostream>
#include "complex.h"
using namespace std;

int main(int argc,char** argv){
    complex a(1,2);
    cout<<a;
    complex b = a+4;
    cout<<b;
    cout<<a+b;
}
```

```c++
complex.h
#ifndef TESTCPP_COMPLEX_H
#define TESTCPP_COMPLEX_H
#include <iostream>
using std::endl,std::ostream,std::cout;

class complex {
public:
    explicit complex(double r=0.,double i=0.):re(r),im(i){}
    complex& operator += (const complex& );
    double real() const {return re;};
    double imag () const{return im;};

private:
    double re,im;
    friend complex& __doapl (complex*,const complex&);

};


inline complex&
complex::operator += (const complex& r){
    return __doapl(this,r);
}

complex & __doapl(complex * ths, const complex & r) {
    ths->re += r.re;
    ths->im += r.im;
    return *ths;
}

inline complex
operator + (const complex &x, const complex & y){
    return complex(x.real() + y.real(),
                   x.imag()+y.imag());
}

inline complex
operator + (double x, const complex & y){
    return complex(x + y.real(),
                   y.imag());
}

inline complex
operator + (const complex &x, double y){
    return complex(x.real() + y,
                   x.imag());
}

inline ostream&
operator << (ostream& os,const complex &x){
    return os<<'('<<x.real()<<','<<x.imag()<<')'<<endl;
}
#endif //TESTCPP_COMPLEX_H

```

## 1.2 string
```cpp
//
// Created by wf on 2020/8/18 0018.
//

#ifndef TESTCPP_STRING_H
#define TESTCPP_STRING_H
#include <iostream>
#include <string.h>


class String {
public:
    explicit String(const char* cstr=nullptr);
    String(const String& str);  //拷贝构造函数
    String& operator=(const String& str);   //拷贝赋值
    ~String();
//    char* get_c_str() const {return m_data;}

private:
    char* m_data;
};

inline
String::String(const char *cstr) {
    if (cstr){
        m_data = new char [strlen(cstr) + 1];
        strcpy(m_data,cstr);
    }
    else{
        m_data = new char [1];
        *m_data = '\0';
    }
}

String::~String() {
    std::cout<<*m_data<<"析构了"<<std::endl;
    delete [] m_data;
}

inline
String::String(const String &str) {
    m_data = new char [strlen(str.m_data) + 1];
    strcpy(m_data,str.m_data);
}

String &String::operator=(const String &str) {
    if (this == &str)
        return *this;
    delete [] m_data;
    m_data = new char [strlen(str.m_data) +1];
    strcpy(m_data,str.m_data);
    return *this;
}
#endif //TESTCPP_STRING_H


```

```

#include <iostream>
#include "String.h"

using std::cout;
using std::endl;

int main(int argc,char** argv) {
    String s1();
    String s2("w");

    String *p = new String("x");
    delete p;


//    String s3(s1);
//    cout<<s3<<endl;
//    s3=s2;
//    cout<<s3<<endl;

}
```