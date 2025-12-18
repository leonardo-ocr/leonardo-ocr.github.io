---
title: "Start: Code, English & Russian | Início | Начало"
date: 2025-12-18
draft: false
tags: ["Introduction", "Beecrowd", "Languages", "Math"]
---

# My Learning Log 
# Мой журнал обучения 

Welcome to my evolution space. Here I combine my three current challenges: **programming, english, and russian.**

Добро пожаловать в моё пространство развития. Здесь я совмещаю три моих текущих вызова: **программирование, английский и русский языки.**

---

### Português
Estou criando essa página para me motivar a treinar codar diariamente, ao mesmo tempo que evoluo minha escrita em idiomas estrangeiros. Mostrarei meu avanço diário explicando problemas do **Beecrowd**. Meu foco principal será em problemas de matemática, pois acho essa área mais interessante e desafiadora.

### 🇺🇸 English
I am creating this page to motivate myself to code daily while improving my writing skills in foreign languages. I will share my daily progress by explaining problems from **Beecrowd**. My main focus will be on mathematical problems, as I find this area more interesting and challenging.

### 🇷🇺 Русский
Я создаю эту страницу, чтобы мотивировать себя программировать каждый день и одновременно улучшать навыки письма на иностранных языках. Я буду делиться своим ежедневным прогрессом, объясняя задачи с платформы **Beecrowd**. В основном я буду сосредоточен на математических задачах, так как считаю эту область наиболее интересной и увлекательной.

---

## Challenge: Beecrowd 1346 - Child's Play
[Link](https://judge.beecrowd.com/pt/problems/view/1346)

### English
In this problem, the goal is to balance two sets of tiles (similar to dominoes) so that the sum of the upper side equals the sum of the lower side. The mathematical core of the solution lies in calculating the **difference** between the two values on each tile. 

### Русский
В этой задаче цель состоит в том, чтобы сбалансировать два набора плиток (похожих на домино) так, чтобы сумма верхней стороны была равна сумме нижней стороны. Математическая суть решения заключается в вычислении **разности** между двумя значениями на каждой плитке.

---

### Implementation (C++)

**Why C++?**
Most of these mathematical problems will be solved using C++. I prefer this language because of the precision and control it offers in complex calculations, as well as its efficiency in handling large volumes of data. 

**Почему C++?**
Большинство этих математических задач будут решены на C++. Я предпочитаю этот язык из-за точности и контроля, которые он предлагает в сложных вычислениях, а также из-за его эффективности при обработке больших объемов данных.

```cpp
#include <iostream>
#include <vector>
#include <numeric>
#include <algorithm>
#include <bitset>

using namespace std;

const int SOMA_MAXIMA = 400005;

struct Peca {
    int x, y, id;
    int diferenca; // v - u: the potential shift between top and bottom sums
    int soma;      // u + v: total value contributed by this tile
};

bool compararPecas(const Peca& a, const Peca& b) {
    if (a.soma != b.soma) return a.soma < b.soma;
    if (a.x != b.x) return a.x < b.x;
    return a.y < b.y;
}

void resolver() {
    int N;
    while (cin >> N && N != 0) {
        vector<Peca> pecas(N);
        int soma_total_diferencas = 0;
        int soma_total_valores = 0;

        for (int i = 0; i < N; ++i) {
            int u, v;
            cin >> u >> v;
            if (u > v) swap(u, v); 
            pecas[i] = {u, v, i, v - u, u + v};
            soma_total_diferencas += (v - u);
            soma_total_valores += (u + v);
        }

        bool tudo_possivel = false;
        if (soma_total_diferencas % 2 == 0) {
            bitset<SOMA_MAXIMA> dp;
            dp[0] = 1;
            int alvo = soma_total_diferencas / 2; 
            
            for (const auto& p : pecas) {
                dp |= (dp << p.diferenca); 
            }
            
            if (dp[alvo]) {
                cout << soma_total_valores / 2 << " discard none" << endl;
                tudo_possivel = true;
            }
        }

        if (tudo_possivel) continue;

        sort(pecas.begin(), pecas.end(), compararPecas);
        vector<bitset<SOMA_MAXIMA>> prefixo(N + 1);
        prefixo[0][0] = 1;
        for (int i = 0; i < N; ++i) {
            prefixo[i+1] = prefixo[i] | (prefixo[i] << pecas[i].diferenca);
        }

        vector<bitset<SOMA_MAXIMA>> sufixo_rev(N + 1);
        sufixo_rev[N][SOMA_MAXIMA - 1] = 1; 

        for (int i = N - 1; i >= 0; --i) {
            sufixo_rev[i] = sufixo_rev[i+1] | (sufixo_rev[i+1] >> pecas[i].diferenca);
        }

        bool encontrado = false;
        for (int k = 0; k < N; ++k) {
            int soma_diferenca_atual = soma_total_diferencas - pecas[k].diferenca;
            if (soma_diferenca_atual % 2 != 0) continue;
            
            int alvo = soma_diferenca_atual / 2;
            int deslocamento = SOMA_MAXIMA - 1 - alvo;

            if ((prefixo[k] & (sufixo_rev[k+1] >> deslocamento)).any()) {
                cout << (soma_total_valores - pecas[k].soma) / 2 << " discard " << pecas[k].x << " " << pecas[k].y << endl;
                encontrado = true;
                break;
            }
        }
        if (!encontrado) cout << "impossible" << endl;
    }
}

int main() {
    ios_base::sync_with_stdio(false); 
    cin.tie(NULL);
    resolver();
    return 0;
}
```


---
that's it for today! ;)
I'll be back tomorrow with another problem.

На сегодня это всё! ;) 
Завтра я вернусь с новой задачей.