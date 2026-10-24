<div align="center">
# 🦔💨 zg-switch
 
**Sonic Riders: Zero Gravity (Wii) em homebrew para Nintendo Switch**
*Experimental · tradução antecipada (ahead-of-time) do código do jogo para ARM64 · núcleo de hardware baseado no Dolphin*

<img width="4080" height="1884" alt="20261024_004412" src="https://github.com/user-attachments/assets/aa0d8e9c-bf52-4027-8886-800f5a1b4564" />
 
![Status](https://img.shields.io/badge/status-beta-orange)
![Plataforma](https://img.shields.io/badge/plataforma-Nintendo%20Switch-e60012)
![Velocidade](https://img.shields.io/badge/velocidade-~41%25%20do%20Wii-yellow)
![Licença](https://img.shields.io/badge/licen%C3%A7a-GPL--3.0-blue)
 
[English](README.md) · **Português (BR)**
 
</div>
---
 
> [!WARNING]
> **Beta.** A velocidade atual é de **~41–44% da velocidade original do Wii em corridas (~25 FPS)** e **~39–41% em cutscenes**. **Ainda não é jogável em velocidade total.** Meta atual: **30 FPS estáveis (50%)**, depois chegar mais perto de 60 FPS.
 
## 📌 O que é isto?
 
Um projeto experimental que roda **Sonic Riders: Zero Gravity (Wii)** em um **Nintendo Switch com homebrew**.
 
O código PowerPC do jogo é **traduzido estaticamente de forma antecipada** para ARM64 nativo (as partes em C são compiladas com clang) e linkado em um `.nro` de homebrew. O hardware do Wii (GPU/FIFO, áudio DSP, temporização) ainda é tratado por um **núcleo adaptado, baseado no Dolphin**. Ou seja, é um **híbrido**: código de jogo nativo sobre um console emulado.
 
 
## ✨ Destaques
 
- 🎯 **Tradução antecipada** do código do jogo para ARM64 nativo
- 🐬 **Núcleo baseado no Dolphin** para o hardware do Wii
- ⚙️ **Opções em tempo de execução** que você pode ativar/desativar e medir (veja abaixo)
- 📊 **Medições honestas**, com a variação informada
## 📈 Desempenho
 
```
Velocidade do Wii (100% = 60 FPS)
Agora  ████████████████░░░░░░░░░░░░░░░░░░░░░░░░  ~41%   (~25 FPS)
Meta   ████████████████████░░░░░░░░░░░░░░░░░░░░   50%   (30 FPS)  ← próximo
Total  ████████████████████████████████████████  100%   (60 FPS)
```
 
| Rodada | Corrida (replay de input gravado) | Cutscene |
|---|---:|---:|
| 28 | 35% | 40% |
| 29 | 41–44% | 39–41% |
 
**Progresso na mesma largada automática de corrida:** R24 ~15% → R25 ~24% → R27 ~21% → **R29 ~40%**
 
> [!NOTE]
> Os resultados **variam entre execuções**: já medimos de 32% a 44% na mesma build. Compare os números apenas dentro da mesma cena e das mesmas condições (dock/portátil, carregador, temperatura).
 
### Onde o tempo é gasto (cutscene, rodada 29)
 
| Categoria | Parcela |
|---|---:|
| Código do jogo traduzido | 46,5% |
| Núcleo baseado no Dolphin | 45,4% |
| Caminho lento de memória / suporte a módulos | 6,9% |
| Ponto flutuante | 0,7% |
| Hardware (FIFO/MMIO) | 0,3% |
 
## 🎛️ Opções em tempo de execução (medidas em uma cena de corrida)
 
Linha de base: **39,5%**.
 
| Opção | Valor testado | Resultado |
|---|---|---|
| `RIDERSZG_DIRECT_CALLS` | `0` | **28%** (−11 pts: importa muito) |
| | `16` / `96` | 39% / 39% (igual) |
| `RIDERSZG_SYNC_IDLE` | `1` | 33,5% (−6 pts) |
| `RIDERSZG_CACHE_DATA` | `0` | 35,5% (−4 pts) |
| `RIDERSZG_FAST_FP` | `0` | 37% (−2,5 pts) |
| `RIDERSZG_AUTO_IDLE` | `0` | 39% (igual) |
| `RIDERSZG_PIN_THREADS` | `0` | 39,5% (igual) |
| Parâmetro de build `K` | 4 / 5 / 6 / 7 | **41,5%** / 39,5% / 39,5% / 38,5% |
| Flags do compilador | `-Os` / `-fno-slp-vectorize` / `-O3` | 38% / 38,5% / 38,5% |
 
`K=4` é o padrão atual (+2 pts, próximo do ruído entre execuções). As opções fáceis se esgotaram; ganhos adicionais exigem novas otimizações.
 
 
## 🗺️ Roteiro de desenvolvimento
 
- [x] Rodada 29: versão atual
- [ ] **Rodada 30** (em validação):
  - [ ] Funções do jogo sem overhead de entrada/saída de chamada (`preserve_none`)
  - [ ] Paired-single (`psq`) e acesso à MEM2 mais enxutos
  - [ ] Flush de cache apenas onde existe código
- [ ] Acesso direto à MEM2 (cutscenes: ~7 milhões de acessos lentos por segundo medidos)
- [ ] Reduzir o overhead no loop de despacho do núcleo
- [ ] **30 FPS estáveis** (média ≥50%, mínimo ≥45% em janelas de 5 s)
- [ ] Rumo aos 60 FPS
## 🔧 Requisitos
 
- Nintendo Switch com homebrew (por exemplo, Atmosphère) e um cartão SD
- Espaço livre em disco: **3,54 GB**

## 🚀 Build e instalação

O repositório contém os arquivos pequenos da "homebrew". Os **4 arquivos grandes** (cerca de 3,5 GB) ficam na [página de Releases](https://github.com/MegaTail/zg-switch/releases/latest), porque passam do limite de 100 MB por arquivo do GitHub.

1. Baixe este repositório (**Code → Download ZIP**) e extraia.
2. Baixe os arquivos grandes na [última release](https://github.com/MegaTail/zg-switch/releases/latest):
   - `riderszg.nro`
   - `Image1.cvm`, `Image2.cvm`, `Image3.cvm`
3. Organize os arquivos nesta estrutura:

```
riderszg/
├── riderszg.nro            ← da release
├── game/
│   └── files/
│       ├── Image1.cvm      ← da release
│       ├── Image2.cvm      ← da release
│       └── Image3.cvm      ← da release
└── ... (todos os outros arquivos deste repositório)
```

4. Copie a pasta `riderszg` inteira para a pasta `switch` do seu cartão SD.
5. Abra-o pelo Homebrew Menu segurando o R.

ou

use o Sphaira e crie um Forwarder para abrir pelo menu principal.
 
## 📄 Licença
 
GPL-3.0. Veja [LICENSE](LICENSE). Inclui código derivado do Dolphin Emulator.
