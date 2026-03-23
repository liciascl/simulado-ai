# Antes de sair fazendo a prova!
Respire fundo, leia as rúbricas com calma, crie a sua estratégia para conquistar a maior quantidade de pontos, você pode consultar o site da disciplina, o seu GitHub e suas anotações, só não utiliza o google, nem qualquer tipo de IA, não tente se comunicar com ninguém, não se esforce para receber um código de ética, não vale a pena a essa altura da vida. Te desejo boa sorte e prometo que vou corrigir a prova com muita boa vontade.


## SuperComp-Sub-2025-2
### Avaliação Subistitutiva de Supercomp, 2 semestre de 2025
Abaixo temos um código base implementado de forma sequencial em CPU, da forma menos eficiente que eu consegui imaginar, ele realiza o cálculo da soma, média, quantidade de picos e quantidade de vales de um sensor fictício. Quando executado na fila **gpu** do cluster Franky, o programa produz os seguintes resultados:

```bash
Processando vetor de 123456789 elementos...
Soma final: 7.60016e+09
Média final: 61.5613
Quantidade de picos: 123456
Quantidade de vales: 123456
Tempo CPU sequencial: 991.454 ms
```



```cpp
#include <iostream>
#include <vector>
#include <cmath>
#include <chrono>
#include <iomanip>

// Função ineficiente: inicializa vetor por valor
std::vector<float> inicializar_vetor(std::vector<float> v, size_t N) {
    for (size_t i = 0; i < N; i++) {
        v[i] = (i % 1000) * 0.123f;
    }
    return v; // retorna uma cópia
}

// Função muito ruim: processa os dados por valor
std::vector<float> processar_dados(
    std::vector<float> v,
    size_t N,
    double &soma,
    size_t &num_picos,
    size_t &num_vales
) {
    std::vector<float> out(N, 0.0f);

    soma = 0.0;
    num_picos = 0;
    num_vales = 0;

    for (size_t i = 1; i < N - 1; i++) {

        float a = v[i - 1];
        float b = v[i];
        float c = v[i + 1];

        // Média local
        float media_local = (a + b + c) / 3.0f;

        // Pico
        bool pico = (b > a && b > c);

        // Vale
        bool vale = (b < a && b < c);

        if (pico) num_picos++;
        if (vale) num_vales++;

        out[i] = media_local + (pico ? b : 0.0f);

        soma += out[i];
    }

    return out; // retorna outra cópia gigante </3
}

// Função horrivel: calcula média por valor
double calcular_media(std::vector<float> out, double soma, size_t N) {
    return soma / N;
}


int main() {
    const size_t N = 123'456'789;

    // Vetor inicial
    std::vector<float> v(N);

    std::cout << "Processando vetor de " << N << " elementos...\n";

    // Inicialização
    v = inicializar_vetor(v, N);

    auto t0 = std::chrono::high_resolution_clock::now();

    double soma = 0.0;
    size_t num_picos = 0;
    size_t num_vales = 0;

    // Processamento sequencial do vetor
    std::vector<float> out = processar_dados(v, N, soma, num_picos, num_vales);

    // Mais uma cópia desnecessária de dados para calcular média :'(
    double media_final = calcular_media(out, soma, N);

    auto t1 = std::chrono::high_resolution_clock::now();
    double tempo_ms = std::chrono::duration<double, std::milli>(t1 - t0).count();

    std::cout << "Soma final: "           << soma        << "\n";
    std::cout << "Média final: "          << media_final << "\n";
    std::cout << "Quantidade de picos: "  << num_picos   << "\n";
    std::cout << "Quantidade de vales: "  << num_vales   << "\n";
    std::cout << "Tempo CPU sequencial: " << tempo_ms    << " ms\n";

    return 0;
}


```

**Exercício 1 - Otimizações em CPU - 2 pontos**
Implemente as seguintes otimizções:

- Melhore o acesso aos dados, usando passagem por referência ou por ponteiro +0.2 pontos
- Aplicar Tilling para melhorar a localidade espacial e temporal e aproveitar melhor a memória cache +0.5 pontos
- Aplicar paralelismo em CPU usando OpenMP +0.8 pontos
- Criar um arquivo 'run.slurm' que solicita adequadamete os recursos de hardware para o Cluster Franky executar a versão com paralelismo em **CPU**. +0.5 pontos

**Exercício 2 - Otimizações em GPU - 3 pontos**

Implemente as seguintes otimizções:

* Alocação adequada dos dados da CPU para a GPU +0.2 pontos
* Implementação dos kernels para realizar as operações em GPU de forma síncrona +2 pontos
* O código compila com 'nvcc' e gera um executável +0.3 pontos
* Criar um arquivo 'run.slurm' que solicita adequadamete os recursos de hardware para o Cluster Franky executar a versão com paralelismo em **GPU**. +0.5 pontos

**Exercício 3 - Otimizações em GPU - 4 pontos**

Implemente as seguintes otimizções:

- Gerenciamento adequado dos dados para implementação em **GPU assíncrona** +1 ponto
- Implementação dos kernels para realizar as operações em **GPU de forma assíncrona** +2 pontos
- O código compila com 'nvcc' e gera um executável que apresenta corretamente a quantidade de picos, vales, média e a soma dos valores processados em GPU +0.5 pontos
- Criar um arquivo 'run.slurm' que solicita adequadamete os recursos de hardware para o Cluster Franky executar a versão com paralelismo em **GPU**. +0.5 pontos



Boa Prova!
    


