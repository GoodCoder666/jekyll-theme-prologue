---
title: 关于我
icon: fa-user
order: 3
---
```cpp
#define OK 200
int TryEverything() {
    tryTheFirstTime();
    string res;
    while(cin >> res && res != "success") tryAgain();
    return OK;
}
```
