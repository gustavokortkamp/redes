# Fibras e Cabos Ópticos — Resumo (Redes FTTH)

Nov 17, 2026 · @Gustavo

Resumo das seções 3.1 (Fibras ópticas) e 3.2 (Cabos ópticos) do material *Projeto e Viabilidade de Redes FTTH* (Ronaldo Couto / Primori, rev. 30), com diagramas redesenhados. Onde o original tinha um pequeno erro de digitação ou uma explicação imprecisa, a correção está indicada.

## 1. O que é a fibra óptica

A fibra óptica é um filamento de vidro (sílica) capaz de conduzir luz de um **emissor** (laser/LED) até um **detector** (fotodiodo). É transparente, flexível, dielétrica (não conduz eletricidade) e tem espessura próxima à de um fio de cabelo.

## 2. Constituição da fibra

A fibra tem três camadas concêntricas:

- **Núcleo** — região central por onde a luz efetivamente viaja.
- **Casca** — envolve o núcleo e tem índice de refração um pouco **menor** que o dele. Essa diferença provoca a reflexão interna total, que mantém a luz confinada no núcleo. *(O slide original diz que a casca é "responsável pela transmissão da luz"; o mais preciso é dizer que ela confina a luz no núcleo.)*
- **Revestimento primário** — camada plástica (acrilato) que protege o vidro contra danos mecânicos e umidade. Com ele, a fibra chega a cerca de 250 µm.

&#91;embedded content: Estrutura da fibra óptica · corte transversal\]

Núcleo e casca são ambos de vidro; o que muda entre eles é o índice de refração.

## 3. Multimodo × Monomodo

| Característica | Multimodo (MM) | Monomodo (SM) |
| --- | --- | --- |
| Diâmetro do núcleo | 50 µm ou 62,5 µm | 9 µm |
| Diâmetro da casca | 125 µm | 125 µm |
| Comprimentos de onda | 850 nm e 1300 nm | 1310 nm a 1650 nm |
| Atenuação típica | 3,5 dB/km @ 850 nm · 1,0 dB/km @ 1300 nm | 0,35 dB/km @ 1310 nm · 0,20 dB/km @ 1550 nm |
| Uso hoje | LANs de curta distância | Redes externas, LANs e **FTTH** |
| Normas ITU-T | G.651.1 | G.652, G.653, G.655, G.656, G.657 |

Na multimodo, o núcleo largo permite que a luz siga vários caminhos (modos), refletindo nas paredes; cada modo chega num instante diferente, o que alarga o pulso (dispersão modal) e limita a distância. Na monomodo, o núcleo de 9 µm deixa passar praticamente um único modo, quase em linha reta — por isso alcança dezenas de quilômetros com baixa perda.

&#91;embedded content: Propagação da luz · multimodo × monomodo\]

As duas têm casca de 125 µm; é o diâmetro do núcleo que define quantos modos a fibra transporta.

## 4. Fibras usadas em FTTH

### G.652.D — rede externa

- Baixo pico d'água (atenuação reduzida por volta de 1383 nm) e PMD melhorada.
- Por não ter o pico d'água, permite usar toda a faixa de 1260 a 1625 nm, o que viabiliza sistemas **CWDM** — algo que a G.652.B não suporta bem. *(No original aparece "CDWM", erro de digitação.)*
- Atenuação máxima: 0,4 dB/km de 1310 a 1625 nm; 0,3 dB/km em 1550 nm.
- Valores típicos: 0,35 dB/km @ 1310 nm · 0,25 dB/km @ 1490 nm · 0,20 dB/km @ 1550 nm.
- Cuidado: é sensível a curvas acentuadas, que causam perdas altas — ruim para instalações internas.

&#91;embedded content: Bandas ITU-T e comprimentos de onda da GPON\]

Na GPON, a OLT transmite dados em 1490 nm e recebe em 1310 nm; o 1550 nm fica para o vídeo (RF overlay).

### G.657 (BLI) — rede interna e drop

Fibras de baixa sensibilidade à curvatura (*Bend Loss Insensitive*). Podem ser dobradas em raios bem menores sem perda significativa, por isso são indicadas para cabos internos, drops e PTOs. As categorias A (A1, A2) são totalmente compatíveis com a G.652.D; as B (B2, B3) aceitam raios ainda menores.

Perda máxima por macrocurvatura (fibra nua), conforme a tabela ITU-T reproduzida no material:

| Raio (mm) | Voltas | λ (nm) | G.652.D | G.657.A1 | G.657.A2 / B2 | G.657.B3 |
| --- | --- | --- | --- | --- | --- | --- |
| 30 | 100 | 1625 | 0,1 dB | — | — | — |
| 15 | 10 | 1550 | — | 0,25 dB | 0,03 dB | — |
| 15 | 10 | 1625 | — | 1,0 dB | 0,1 dB | — |
| 10 | 1 | 1550 | — | 0,75 dB | 0,1 dB | 0,03 dB |
| 10 | 1 | 1625 | — | 1,5 dB | 0,2 dB | 0,1 dB |
| 7,5 | 1 | 1550 | — | — | 0,5 dB | 0,08 dB |
| 7,5 | 1 | 1625 | — | — | 1,0 dB | 0,25 dB |
| 5 | 1 | 1550 | — | — | — | 0,15 dB |
| 5 | 1 | 1625 | — | — | — | 0,45 dB |

Leitura prática: a G.652.D só é especificada para curvas largas (30 mm); para raios de 10 mm ou menos, use G.657.

## 5. Cabos com tubo loose

- As fibras (com revestimento de 250 µm) ficam **soltas** dentro de tubos plásticos, com um comprimento um pouco maior que o do tubo.
- Essa folga deixa a fibra se mover quando o cabo dilata ou contrai com a temperatura, e a protege durante o lançamento.
- O tubo costuma ser preenchido com gel repelente à água.
- Os cabos externos tipo **DD** (duto) e **AS** (autossustentado) são construídos com tubos loose.

&#91;embedded content: Cabo tubo loose · corte genérico, fora de escala\]

Os tubos ficam dispostos em volta de um elemento central, que dá rigidez ao cabo; a capa externa fecha o conjunto.

## 6. Nomenclatura dos cabos

### Cabo para duto — ex.: CFOA-SM-DD-G-36 FO

| Campo | Significado |
| --- | --- |
| CFOA | Cabo de fibra óptica revestida em acrilato |
| SM / MM | Monomodo / multimodo |
| DD / DDR / DE | Duto / duto com proteção contra roedores / diretamente enterrado (o DD também pode ser espinado em cordoalha) |
| G / S | Geleado / seco (seco só para rede aérea) |
| 36 FO | Número de fibras (até 144, em grupos de 2, 6 ou 12) |

### Cabo aéreo autossustentado — ex.: CFOA-SM-AS-80-G-12 FO-NR

| Campo | Significado |
| --- | --- |
| AS / ASU / AS RA | Autossustentado / autossustentado com tubo único |
| 80 / 120 / 200 | Vão máximo entre postes (m) |
| G / S | Geleado / seco |
| 12 FO | Número de fibras (até 144; tubo único até 12) |
| NR / RC | Capa não retardante / retardante à chama |

### Cabo drop — ex.: DROP-F8-FTTH-SM-G652D-02 FO-COG

| Campo | Significado |
| --- | --- |
| DROP | Cabo de atendimento ao cliente |
| F8-FTTH | Formato "figura 8", com mensageiro para ancoragem |
| SM-G652D | Tipo de fibra |
| 02 FO | Número de fibras (até 12, em um único grupo) |
| COG / LSZH | Retardante à chama / retardante com baixa emissão de fumaça tóxica |

### Drop compacto — ex.: CFOAC-BLI-A/B-CM-01-AR-LSZH

| Campo | Significado |
| --- | --- |
| CFOAC | Cabo de fibra óptica de acesso |
| BLI-A/B ou SM | Fibra G.657 (baixa sensibilidade à curvatura) ou monomodo padrão |
| CM / CD | Mensageiro compacto metálico / dielétrico |
| 01 | Número de fibras (em geral 1 a 8) |
| AR / CO | Capa de atrito reduzido / convencional |
| LSZH / COG | Tipo de capa |

## 7. Código de cores

| Nº | Fibra — ABNT | Fibra — EIA-598-A | Tubo — ABNT | Tubo — EIA-598-A |
| --- | --- | --- | --- | --- |
| 1 | Verde | Azul | Verde (piloto) | Azul |
| 2 | Amarelo | Laranja | Amarelo (direcional) | Laranja |
| 3 | Branco | Verde | Branco | Verde |
| 4 | Azul | Marrom | Branco | Marrom |
| 5 | Vermelho | Cinza | Branco | Cinza |
| 6 | Violeta | Branco | Branco | Branco |
| 7 | Marrom | Vermelho | Branco | Vermelho |
| 8 | Rosa | Preto | Branco | Preto |
| 9 | Preto | Amarelo | Branco | Amarelo |
| 10 | Cinza | Violeta | Branco | Violeta |
| 11 | Laranja | Rosa | Branco | Rosa |
| 12 | Aqua | Aqua | Branco | Aqua |

No padrão ABNT os tubos não têm cor própria a partir do 3º: todos são brancos. A contagem parte do tubo **verde (piloto)** e segue na direção do tubo **amarelo (direcional)**; os demais brancos são contados nesse sentido, que pode ser horário ou anti-horário conforme o lado do cabo que você está olhando.

&#91;embedded content: Sequência dos tubos no padrão ABNT · 6 tubos\]

Por isso, antes de abrir uma emenda, identifique piloto e direcional na ponta do cabo que está na sua mão.

## 8. Numeração das fibras no cabo

A fibra recebe um número sequencial: os 12 (ou 6) primeiros números são do tubo 1, os seguintes do tubo 2, e assim por diante.

- Cabos de 48 a 144 FO (12 fibras por tubo): **nº = (tubo − 1) × 12 + posição da cor**. Ex.: tubo 3, fibra azul (4ª cor ABNT) → 2 × 12 + 4 = **28**.
- Cabos de 18 a 36 FO (6 fibras por tubo): **nº = (tubo − 1) × 6 + posição da cor**. Ex.: tubo 4, fibra vermelha (5ª cor) → 3 × 6 + 5 = **23**.

## Fonte

COUTO, Ronaldo. *Projeto e Viabilidade de Redes FTTH*, rev. 30. Primori Desenvolvimento de Competências, 2018 — slides 20 a 37.
