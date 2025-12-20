---
title: "Solving Beecrowd 1232: Rubik’s Cycle"
date: 2025-12-19
tags: [competitive programming, c++, algorithms, math, beecrowd]

---

While solving some problems today, I came across problem 1232 and found it very interesting. It gave me a headache, but it was fun to solve.

In this post, I will explain how to solve this problem using Permutation Cycles and the Least Common Multiple (LCM), avoiding the "Time Limit Exceeded" (TLE) error.


# The Problem

The problem asks a simple question: Given a specific sequence of moves on a Rubik's Cube ("Rotate Front, then Rotate Right"), how many times do we need to repeat that exact sequence for the cube to return to its initial, solved state?

The first instinct is to write a simulation loop:

1.  Initialize the cube.
2.  Apply the sequence.
3.  Check if the cube is solved.
4.  Increment a counter.
5.  Repeat until solved.

That method, of course, will take forever to run.

## Permutation Cycles

We can view the Rubik's Cube not as a 3D object, but as an array of 54 positions .

Any sequence of moves, no matter how complex, is simply a permutation function. It shuffles the stickers from their current positions to new ones.

For the entire cube to be solved, all cycles must complete at the same time. Mathematically, the answer is the Least Common Multiple (LCM) of the lengths of all disjoint cycles in the permutation.

# Implementation Strategy

To implement this, we don't need to simulate millions of steps. We only need to simulate the sequence once.

1. Mapping the Cube

We represent the cube as a vector of integers `vector<int> cube(54)`.

 Initially, `cube[i] = i`.
 We map the indices to faces:
 
    **U (Up):** 0-8

    **L (Left):** 9-17

    **F (Front):** 18-26

    **R (Right):** 27-35

    **B (Back):** 36-44

    **D (Down):** 45-53

Hardcoding moves is boring. We have 6 faces and 2 directions (Clockwise/Upper-case and Counter-Clockwise/Lower-case), totaling 12 transformation functions.

In optimization we only implement the Clockwise functions.
If the input asks for a Counter-Clockwise move (`'f'`), we simply apply the Clockwise move (`'F'`) three times.

# The Solution (C++)

It simulates the sequence once, detects the cycles, and computes the LCM.

```cpp
#include <iostream>
#include <vector>
#include <string>
#include <numeric>

using namespace std;
long long mmc(long long a, long long b) {
    if (a == 0 || b == 0) return 0;
    return (a / std::gcd(a, b)) * b;
}
typedef vector<int> Cubo;
void ciclo4(Cubo &c, int a, int b, int d, int e) {
    int temp = c[e];
    c[e] = c[d];
    c[d] = c[b];
    c[b] = c[a];
    c[a] = temp;
}
void rotacionarFace(Cubo &c, int offset) {
    ciclo4(c, offset + 0, offset + 2, offset + 8, offset + 6);
    ciclo4(c, offset + 1, offset + 5, offset + 7, offset + 3);
}
void moveU(Cubo &c) {
    rotacionarFace(c, 0);
    ciclo4(c, 18, 9, 36, 27);
    ciclo4(c, 19, 10, 37, 28);
    ciclo4(c, 20, 11, 38, 29);
}

void moveL(Cubo &c) {
    rotacionarFace(c, 9);
    ciclo4(c, 0, 18, 45, 44);
    ciclo4(c, 3, 21, 48, 41);
    ciclo4(c, 6, 24, 51, 38);
}

void moveF(Cubo &c) {
    rotacionarFace(c, 18);
    ciclo4(c, 6, 27, 47, 17);
    ciclo4(c, 7, 30, 46, 14);
    ciclo4(c, 8, 33, 45, 11);
}

void moveR(Cubo &c) {
    rotacionarFace(c, 27);
    ciclo4(c, 8, 36, 53, 26);
    ciclo4(c, 5, 39, 50, 23);
    ciclo4(c, 2, 42, 47, 20);
}

void moveB(Cubo &c) {
    rotacionarFace(c, 36);
    ciclo4(c, 2, 9, 51, 35);
    ciclo4(c, 1, 12, 52, 32);
    ciclo4(c, 0, 15, 53, 29);
}

void moveD(Cubo &c) {
    rotacionarFace(c, 45);
    ciclo4(c, 24, 33, 42, 15);
    ciclo4(c, 25, 34, 43, 16);
    ciclo4(c, 26, 35, 44, 17);
}

void aplicarMovimento(Cubo &c, char m) {
    bool horario = (m >= 'A' && m <= 'Z');
    char base = horario ? m : (m - 'a' + 'A');
    int repeticoes = horario ? 1 : 3;

    for (int i = 0; i < repeticoes; ++i) {
        if (base == 'U') moveU(c);
        else if (base == 'L') moveL(c);
        else if (base == 'F') moveF(c);
        else if (base == 'R') moveR(c);
        else if (base == 'B') moveB(c);
        else if (base == 'D') moveD(c);
    }
}

int main() {
    string linha;
    while (cin >> linha) {
        Cubo cubo(54);
        for (int i = 0; i < 54; ++i) cubo[i] = i;
        for (char m : linha) {
            aplicarMovimento(cubo, m);
        }
        long long res = 1;
        vector<bool> visitado(54, false);

        for (int i = 0; i < 54; ++i) {
            if (!visitado[i]) {
                int count = 0;
                int curr = i;
                while (!visitado[curr]) {
                    visitado[curr] = true;
                    curr = cubo[curr];
                    count++;
                }
                res = mmc(res, count);
            }
        }
        cout << res << endl;
    }
    return 0;
}
```

unfortunately, I didn't have time to write in russian today because I was busy studying for an exam.
my bad! the russian posts will be back soon.