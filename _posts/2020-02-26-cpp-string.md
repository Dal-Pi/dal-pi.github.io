---
title: "[C++] std::string 활용"
date: 2020-02-26 11:47:00 -0400
categories: blog

categories:
  - C++
tags:
  - C++
  - string
  - I/O
last_modified_at: 2020-02-26T02:00:00-05:00
---

잡다한 std::string 사용법

## 파일에서 읽기
```cpp
ifstream ifs("input.txt"); // 생성자에서 파일을 연다 확인은 is_open()으로
ofstream ofs("output.txt");

int num = 0;
string inputStr;
ifs >> num; // cin 처럼 받아올 수 있다
ifs.ignore(); // 아래 getline을 쓰기 전에 입력 버퍼를 비운다
getline(ifs, inputStr); // 한 줄을 얻어오고 싶을 때

string outputStr("Output String");
ofs << outputStr; // cout 처럼 파일에 출력할 수 있다
```
