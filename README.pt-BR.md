<div align="center">
# 🦔💨 zg-switch
 
**Sonic Riders: Zero Gravity (Wii) em homebrew para Nintendo Switch**
*Experimental · tradução antecipada (ahead-of-time) do código do jogo para ARM64 · núcleo de hardware baseado no Dolphin*
 
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
 
1. Baixe este repositório.
2. Coloque o "riderszg" na pasta "switch".
3. Abra-o pelo Homebrew Menu.
ou
 
use o Sphaira e crie um Forwarder para abrir pelo menu principal.
 
 
## ⚖️ Aviso legal
 
- Este repositório **não contém código, assets ou dados da Sega**, nenhuma imagem do jogo, nem chaves ou firmware do Switch.
- Projeto de fã, **sem afiliação ou endosso da Sega ou da Nintendo**. *Sonic Riders: Zero Gravity* é marca registrada de seus respectivos proprietários.
## ⚠️ Riscos
 
- Homebrew é **por sua conta e risco**. Mantenha seu Switch **fora dos serviços online da Nintendo** para evitar banimentos.
- Software experimental.
## ❓ FAQ
 
**Já dá para jogar em velocidade total?** Ainda não: ~41–44% em corridas.
**Isto é um emulador?** Em parte. O código do jogo é traduzido antecipadamente, mas o hardware do console roda em um núcleo baseado no Dolphin.
**Quais regiões funcionam?** **[EUA / PAL]**
 
## 🐞 Reportando problemas
 
Inclua: versão/rodada, cena, velocidade medida, dock ou portátil e logs, se disponíveis.
 
## 🙏 Créditos
 
- **Dolphin Emulator**: o núcleo de hardware no qual este projeto se baseia
- **devkitPro / libnx**: toolchain de homebrew para Switch
- **zig / LLVM clang**: compilador
## 🤖 Uso de IA no desenvolvimento
 
Partes deste projeto foram desenvolvidas com assistência de IA (Claude, da Anthropic) e validadas com testes e medições no console.
 
## 📄 Licença
 
GPL-3.0. Veja [LICENSE](LICENSE). Inclui código derivado do Dolphin Emulator.
