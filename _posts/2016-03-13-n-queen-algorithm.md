---
layout: post
title:  "N-Queen 알고리즘"
date:   2016-03-14
author: Sooyoung
categories: dev
tags: dev algorithm backtrack
cover:  "/assets/img/queen.png"
---

# N-Queen 알고리즘

## N-Queen 문제 설명

N-Queen 퍼즐은 N x N 크기의 체스판에 N 개의 퀸을, 서로 공격할 수 없도록 올려놓는 퍼즐입니다. (퀸은 체스에서 가장 강력한 기물로, 자신의 위치에서 상하좌우, 그리고 대각선 방향으로 이어진 직선 상의 어떤 기물도 공격할 수 있습니다)

![image](http://algospot.com/media/judge-attachments/bc92d43c2acc9acf45702485b3fb1e9e/nqueen.png)
예를 들어, 위의 그림은 8x8 크기의 체스판에 8개의 퀸을 서로 공격할 수 없도록 올려놓은 예 입니다.

체스판의 크기 N 이 주어졌을 때, N-Queen 퍼즐의 답이 모두 몇 개나 되는지를 계산하고 아래와 같이 출력하는 프로그램을 작성하세오. 한 답은 N개 퀸 모두의 위치로 정의되며, 한 퀸의 위치만 다르더라도 다른 답이라고 가정합니다.


###### 출력

```
n = 01, solution count is 1.
n = 02, solution count is 0.
n = 03, solution count is 0.
n = 04, solution count is 2.
n = 05, solution count is 10.
n = 06, solution count is 4.
n = 07, solution count is 40.
n = 08, solution count is 92.
n = 09, solution count is 352.
n = 10, solution count is 724.
n = 11, solution count is 2680.
n = 12, solution count is 14200.
n = 13, solution count is 73712.
```

## Backtrackting 알고리즘

위의 문제를 풀기위해 생각할수 있는 가장 간단?한 방법은 전수조사를 하는것입니다. 퀸이 올수 있는 모든 경우의 수를 두고, 그 중에서 답을 찾는 방법이죠. N이 4라고 가정한다면 각 퀸은 한 열의 하나씩만 올수 있기 때문에 이때 점검해야할 모든 경우의 수는 4 x 4 x 4 x 4 = 256 가지가 됩니다. 하지만 이와같은 방법은 굳이 탐색할 필요도 없는 경우까지 모두 탐색하기 때문에 비효율적입니다.


![백트렉킹](/assets/img/backtrack.png)

*백트렉킹 출저: https://ko.wikipedia.org/wiki/%ED%87%B4%EA%B0%81%EA%B2%80%EC%83%89*


#### 백트렉킹

백트렉킹이란, 특정 노드에서 유망성(promising)을 점검하고, 유망하지 않다면 그 노드의 부모로 돌아가서(Backtracking) 다음 노드에 대한 검색을 계속하게 되는 절차입니다.

#### 백트렉킹 알고리즘

이처럼 백트렉킹 알고리즘은 전수조사 방법중 하나인 깊이우선탐색으로 시작합니다. 그리고 각 노드에서 유망하지 않은 노드들은 탐색 하지 않고 (이를 **가지치기 Pruning** 라고 합니다) 유망한 노드에 대해서만 탐색을 합니다.

즉 순서는 이렇게 진행됩니다.

1. 처음 깊이 우선 탐색을 시작합니다.
2. 각 노드가 유망한지 검사합니다.
3. 만약 유망하다면 탐색을 계속합니다.
4. 만약 유망하지 않다면 그의  부모 노드로 돌아가서 탐색을 계속합니다.(백트렉킹)

이와같은 방법을 이용하면 깊이우선탐색보다 검색하는 경우의 수를 줄일 수 있습니다.


## N-Queen 문제풀이

자 이제 실제로 문제를 풀어보죠! 
위처럼 백트렉킹알고리즘을 사용하기 위해 유망한지를 검사하는 규칙을 정합니다.

1. 퀸은 같은 열에 있으면 안됩니다. (물론 같은 행에 있어도 안됩니다. 이 조건은 유망성검사를 하기 전에 걸러집니다.)
2. 퀸은 같은 '\' 방향 대각선에 있으면 안됩니다.
3. 퀸은 같은 '/' 방향 대각선에 있으면 안됩니다.

위와 같은 규칙을 가지고 깊이 우선 탐색을 시작합니다. 그러면 N = 4인 경우 아래와 같은 그림이 될 것입니다.

![n-queen](/assets/img/queens4_backtrack.png)
*그림 출저: http://ddmix.blogspot.kr/*

위의 그림처럼 유망한 노드라면 자식 노드로 들어가 다시 탬색 시작하고, 유망하지 않다면 탐색을 그만두고 다시 부모노드로 올라가 다음 노드를 탐색합니다. 이러한 방식으로 끝까지 탐색하는 것이 백트렉킹 알고리즘이자, N-queen문제를 푸는 방법입니다.

##### N-Queen Java 코드

코드를 보면 이렇습니다

```java
public class Main {
    static int count = 0;
    public static boolean isPromising(int[] q, int n) {
        for (int i = 0; i < n; i++) {
            if (q[i] == q[n]) return false;   // 같은 열인지
            if ((q[i] - q[n]) == (n - i)) return false;   // '\' 방향
            if ((q[n] - q[i]) == (n - i)) return false;   // '/' 방향
        }
        return true;
    }

    public static void enumerate(int N) {
        int[] a = new int[N];
        enumerate(a, 0);
    }

    public static void enumerate(int[] q, int n) {
        int N = q.length;
        // n이 끝까지 돌았다면 카운트를 증가한다.
        if (n == N) {
            count++;
        } else {
            for (int i = 0; i < N; i++) {
                q[n] = i;
                if (isPromising(q, n)) enumerate(q, n + 1);   // 유망하다면 계속 탐색(재귀호출)
            }
        }
    }

    public static void main(String[] args) {
        for (short n = 1; n < 14; n++) {
            enumerate(n);
            System.out.println("n = " + (n < 10 ? "0" : "") + n + ", solution count is " + count + ".");
            count = 0;
        }
    }
```

코드를 보면 쉽게 이해할수 있을거라 생각합니다.
**'유망한 노드들만 검사하고, 유먕하지 않다면 부모 노드로 돌아가 탐색을 계속한다'** 이게 백트렉킹 알고리즘의 핵심이 아닐까 싶네요.

사실 저는 가장 기본적인 알고리즘을 사용한 것입니다. 따라서 속도 측면에서 빠르다고 할수 없습니다. 다음 글에서는 N-Queen 문제를 어떻게 하면 빨리풀수 있을지를 설명합니다. (무려 10배나!)



