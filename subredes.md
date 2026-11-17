# Cálculo de Sub-redes — Método do Número Mágico (a "Gaveta")

## Objetivo

Dado um endereço IP com prefixo (ex.: `172.16.5.140/28`), encontrar:

- Máscara
- Endereço de rede
- Broadcast
- Primeiro e último host
- Quantidade de hosts utilizáveis

Tudo **de cabeça**, sem converter o endereço inteiro para binário.

## Conceitos rápidos

- **Endereço IPv4:** 32 bits, divididos em 4 octetos (ex.: `192.168.1.10`)
- **Prefixo (/n):** quantos bits, da esquerda para a direita, pertencem à rede
- **Máscara:** o mesmo prefixo escrito em decimal (`/24` = `255.255.255.0`)
- **Endereço de rede:** o primeiro endereço da sub-rede — não vai em host
- **Broadcast:** o último endereço da sub-rede — não vai em host
- **Hosts utilizáveis:** tudo que está entre a rede e o broadcast

Valor de cada bit dentro de um octeto:

| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
|-----|----|----|----|---|---|---|---|

Somando os bits ligados da esquerda para a direita, saem os valores
possíveis de máscara: **128, 192, 224, 240, 248, 252, 254, 255**.

## A ideia da gaveta

Esse método é conhecido como **número mágico** ou **block size**
(tamanho do bloco), muito usado em cursos de redes e de certificação
CCNA.

Eu gosto de pensar em cada sub-rede como uma **gaveta**:

- Todas as gavetas têm o **mesmo tamanho**
- A **primeira posição** da gaveta é o **endereço de rede**
- A **última posição** é o **broadcast**
- O que está **no meio** são os **hosts**

O trabalho é só descobrir **o tamanho da gaveta** e **em qual gaveta o
endereço caiu**.

## As duas fórmulas

```
256 − máscara = tamanho da gaveta
256 − tamanho da gaveta = máscara
```

> **Atenção:** é **256**, não 255. O octeto tem 256 valores (de 0 a 255).

E para a quantidade de hosts:

```
hosts = total de endereços − 2   (tira a rede e o broadcast)
```

## Passo a passo

1. Pelo prefixo, descubra a **máscara**
2. Ache o **octeto interessante**: aquele em que a máscara não é 255 nem 0
3. Calcule o **tamanho da gaveta**: 256 − valor da máscara nesse octeto
4. Monte as gavetas de 0 em diante: 0, 1×gaveta, 2×gaveta…
5. Veja em qual gaveta o endereço **caiu**
6. **Rede** = início da gaveta · **Broadcast** = início da próxima − 1
7. **Hosts** = rede + 1 até broadcast − 1

## Tabela de referência

### Gaveta no 4º octeto

| Prefixo | Máscara          | Gaveta | Hosts |
|---------|------------------|--------|-------|
| /24     | 255.255.255.0    | 256    | 254   |
| /25     | 255.255.255.128  | 128    | 126   |
| /26     | 255.255.255.192  | 64     | 62    |
| /27     | 255.255.255.224  | 32     | 30    |
| /28     | 255.255.255.240  | 16     | 14    |
| /29     | 255.255.255.248  | 8      | 6     |
| /30     | 255.255.255.252  | 4      | 2     |

### Gaveta no 3º octeto

| Prefixo | Máscara        | Gaveta (3º octeto) | Hosts  |
|---------|----------------|--------------------|--------|
| /16     | 255.255.0.0    | 256                | 65.534 |
| /17     | 255.255.128.0  | 128                | 32.766 |
| /18     | 255.255.192.0  | 64                 | 16.382 |
| /19     | 255.255.224.0  | 32                 | 8.190  |
| /20     | 255.255.240.0  | 16                 | 4.094  |
| /21     | 255.255.248.0  | 8                  | 2.046  |
| /22     | 255.255.252.0  | 4                  | 1.022  |
| /23     | 255.255.254.0  | 2                  | 510    |

> Fórmula geral de hosts: **2^(32 − prefixo) − 2**.
> Ex.: /22 → 2^10 − 2 = 1024 − 2 = **1.022**

## Exemplos resolvidos

### Exemplo 1 — `10.20.30.70/27`

1. /27 → máscara **255.255.255.224**
2. Octeto interessante: o 4º
3. Gaveta: 256 − 224 = **32**
4. Gavetas: 0, 32, **64**, 96, 128…
5. O 70 está entre 64 e 95 → gaveta **64–95**

| Item          | Resultado        |
|---------------|------------------|
| Rede          | 10.20.30.64      |
| Broadcast     | 10.20.30.95      |
| Primeiro host | 10.20.30.65      |
| Último host   | 10.20.30.94      |
| Hosts         | 32 − 2 = **30**  |

### Exemplo 2 — `172.16.5.140/28`

1. /28 → máscara **255.255.255.240**
2. Gaveta: 256 − 240 = **16**
3. Gavetas: …, 112, **128**, 144…
4. O 140 está entre 128 e 143

| Item          | Resultado        |
|---------------|------------------|
| Rede          | 172.16.5.128     |
| Broadcast     | 172.16.5.143     |
| Primeiro host | 172.16.5.129     |
| Último host   | 172.16.5.142     |
| Hosts         | 16 − 2 = **14**  |

### Exemplo 3 — `200.10.10.250/30` (link ponto a ponto)

1. /30 → máscara **255.255.255.252**
2. Gaveta: 256 − 252 = **4**
3. Gavetas: …, 244, **248**, 252
4. O 250 está entre 248 e 251

| Item          | Resultado        |
|---------------|------------------|
| Rede          | 200.10.10.248    |
| Broadcast     | 200.10.10.251    |
| Primeiro host | 200.10.10.249    |
| Último host   | 200.10.10.250    |
| Hosts         | 4 − 2 = **2**    |

> O /30 é muito usado em links entre roteadores: só precisa de 2 IPs.

### Exemplo 4 — `10.1.13.50/22` (gaveta no 3º octeto)

1. /22 → máscara **255.255.252.0**
2. Octeto interessante: o **3º** (é nele que a máscara vale 252)
3. Gaveta: 256 − 252 = **4**, contando no **3º octeto**
4. Gavetas: 0–3, 4–7, 8–11, **12–15**, 16–19…
5. O **13** caiu na gaveta **12–15**

Agora o 4º octeto também entra: a rede termina em **.0** e o broadcast
em **.255**.

| Item          | Resultado            |
|---------------|----------------------|
| Rede          | 10.1.12.0            |
| Broadcast     | 10.1.15.255          |
| Primeiro host | 10.1.12.1            |
| Último host   | 10.1.15.254          |
| Hosts         | 2^10 − 2 = **1.022** |

## Erros comuns

- Fazer **255** − máscara em vez de **256** − máscara
- Esquecer de **tirar 2** (rede e broadcast) na quantidade de hosts
- Contar hosts com subtração simples: de .65 a .94 são **30**, não 29
  (94 − 65 + 1)
- Em prefixos menores que /24, esquecer que o **4º octeto** vai de .0
  (rede) a .255 (broadcast)
- Usar o endereço de rede ou de broadcast em um equipamento

## Exercícios

Para cada endereço, encontre: máscara, rede, broadcast, primeiro host,
último host e quantidade de hosts. Clique em **Resposta** para conferir.

**1.** `192.168.1.100/26`

<details>
<summary>Resposta</summary>

Máscara 255.255.255.192 · gaveta 64 · faixa 64–127
Rede 192.168.1.64 · Broadcast 192.168.1.127
Hosts de .65 a .126 · **62 hosts**
</details>

**2.** `10.0.0.200/29`

<details>
<summary>Resposta</summary>

Máscara 255.255.255.248 · gaveta 8 · faixa 200–207
Rede 10.0.0.200 · Broadcast 10.0.0.207
Hosts de .201 a .206 · **6 hosts**
</details>

**3.** `192.168.10.33/30`

<details>
<summary>Resposta</summary>

Máscara 255.255.255.252 · gaveta 4 · faixa 32–35
Rede 192.168.10.32 · Broadcast 192.168.10.35
Hosts .33 e .34 · **2 hosts**
</details>

**4.** `10.10.10.10/25`

<details>
<summary>Resposta</summary>

Máscara 255.255.255.128 · gaveta 128 · faixa 0–127
Rede 10.10.10.0 · Broadcast 10.10.10.127
Hosts de .1 a .126 · **126 hosts**
</details>

**5.** `172.20.45.10/20`

<details>
<summary>Resposta</summary>

Máscara 255.255.240.0 · gaveta 16 no 3º octeto · faixa 32–47
Rede 172.20.32.0 · Broadcast 172.20.47.255
Hosts de 172.20.32.1 a 172.20.47.254 · **4.094 hosts**
</details>

---
Conteúdo sob licença CC BY 4.0 — Luis Gustavo dos Reis Kort Kamp.
