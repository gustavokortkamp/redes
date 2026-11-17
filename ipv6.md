# IPv6

## Por que o IPv6 existe

O IPv4 tem endereços de **32 bits** (cerca de 4,3 bilhões), e eles
**se esgotaram**. O NAT foi uma solução **paliativa**; o IPv6 é a solução
**definitiva**, com endereços de **128 bits** — o suficiente para que
todo dispositivo tenha um endereço público próprio.

## Formato do endereço

- **128 bits**, divididos em **8 grupos de 16 bits**
- Escritos em **hexadecimal** (0–9, A–F), separados por **`:`**

```
2001:0DB8:00AD:000F:3456:AF42:CDCC:0001
```

### Regras de abreviação

1. **Zeros à esquerda** de cada grupo podem ser omitidos: `00AD` → `AD`
2. Uma sequência de **grupos zerados** pode virar **`::`**
   — **apenas uma vez** por endereço

```
2001:0DB8:04FF:0000:0000:0000:0000:00CC
2001:DB8:4FF::CC
```

> Por que só uma vez? Em `2001::1::2` seria impossível saber quantos
> grupos de zero existem em cada `::`.

### Prefixo

Usa a mesma notação CIDR do IPv4 (`/n`). Em redes locais o padrão é
**/64**: 64 bits identificam a **rede** e 64 bits identificam a
**interface**. A autoconfiguração (SLAAC) depende do /64.

## Tipos de endereço

| Prefixo         | Tipo                                       | Equivalente no IPv4      |
|-----------------|--------------------------------------------|--------------------------|
| `::/128`        | Não especificado                           | 0.0.0.0                  |
| `::1/128`       | Loopback                                   | 127.0.0.1                |
| `2000::/3`      | Global Unicast (público)                   | IP público               |
| `FC00::/7`      | ULA — Unique Local (na prática `FD00::/8`) | IPs privados (RFC 1918)  |
| `FE80::/10`     | Link-local                                 | 169.254.0.0/16 (APIPA)   |
| `FF00::/8`      | Multicast                                  | 224.0.0.0/4              |
| `::FFFF:0:0/96` | IPv4 mapeado em IPv6                       | —                        |
| `2001:DB8::/32` | Documentação (exemplos)                    | 192.0.2.0/24             |
| `2002::/16`     | Túnel 6to4                                 | —                        |
| `2001::/32`     | Túnel Teredo                               | —                        |

**Destaques:**
- **Não especificado (`::`)**: indica ausência de endereço. Usado como
  origem por um host que ainda não tem IP. Nunca é atribuído nem usado
  como destino.
- **Loopback (`::1`)**: a própria máquina. Não sai para a rede; roteador
  descarta pacote com esse destino.
- **Link-local (`FE80::`)**: **toda interface IPv6 tem um**, criado
  automaticamente. Só funciona no enlace local — **roteadores não
  encaminham**. O identificador da interface pode vir do MAC (**EUI-64**)
  ou ser **aleatório** (padrão nos sistemas atuais, por privacidade).

### Endereços multicast conhecidos

O 4º dígito indica o **escopo**: `FF01` = interface, `FF02` = enlace,
`FF05` = site, `FF0E` = global.

| Endereço            | Escopo    | Grupo                                  |
|---------------------|-----------|----------------------------------------|
| `FF01::1`           | Interface | Todos os nós                           |
| `FF01::2`           | Interface | Todos os roteadores                    |
| `FF02::1`           | Enlace    | Todos os nós (substitui o broadcast)   |
| `FF02::2`           | Enlace    | Todos os roteadores                    |
| `FF02::5`           | Enlace    | Roteadores OSPF                        |
| `FF02::6`           | Enlace    | Roteadores OSPF designados (DR/BDR)    |
| `FF02::9`           | Enlace    | Roteadores RIP                         |
| `FF02::D`           | Enlace    | Roteadores PIM                         |
| `FF02::1:2`         | Enlace    | Agentes DHCP (servidores e relays)     |
| `FF02::1:FFxx:xxxx` | Enlace    | Solicited-node — usado pelo NDP        |
| `FF05::2`           | Site      | Todos os roteadores                    |
| `FF05::1:3`         | Site      | Servidores DHCP                        |
| `FF0X::101`         | Variável  | Servidores NTP (X = qualquer escopo)   |

> **Solicited-node:** formado por `FF02::1:FF` + os **últimos 24 bits**
> do endereço IPv6. É para ele que vai o Neighbor Solicitation, em vez de
> um broadcast para a rede toda.

## Unicast, Multicast e Anycast — não existe broadcast

| Tipo          | Entrega                                                                 |
|---------------|-------------------------------------------------------------------------|
| **Unicast**   | Para **um** destino                                                     |
| **Multicast** | Para **todos os membros** de um grupo                                   |
| **Anycast**   | Para **um só** membro do grupo: o **mais próximo** segundo o roteamento |

> **Atenção:** o IPv6 **não tem broadcast**. A função passou para o
> multicast (`FF02::1`). O anycast é novidade (ex.: servidores DNS raiz).

## Cabeçalho

| Característica       | IPv4                     | IPv6                       |
|----------------------|--------------------------|----------------------------|
| Tamanho              | 20 a 60 bytes (variável) | **40 bytes (fixo)**        |
| Quantidade de campos | 12 + opções              | **8**                      |
| Opções               | No próprio cabeçalho     | **Cabeçalhos de extensão** |

Mais **simples** (menos campos, tamanho fixo → processamento mais rápido
nos roteadores) e **modular** (recursos extras vão em cabeçalhos de
extensão, encadeados pelo campo *Next Header*).

| Campo                               | Bits | Função                                                                            |
|-------------------------------------|------|-----------------------------------------------------------------------------------|
| Versão                              | 4    | Valor 6                                                                           |
| Classe de Tráfego                   | 8    | Prioridade / QoS (como o ToS do IPv4)                                             |
| Identificador de Fluxo (Flow Label) | 20   | Marca pacotes de um mesmo fluxo (QoS)                                             |
| Tamanho dos Dados (Payload Length)  | 16   | Tamanho **só dos dados** — inclui os cabeçalhos de extensão, não o cabeçalho base |
| Próximo Cabeçalho (Next Header)     | 8    | Próximo cabeçalho de extensão ou protocolo superior (TCP, UDP…)                   |
| Limite de Saltos (Hop Limit)        | 8    | Igual ao TTL do IPv4                                                              |
| Endereço de Origem                  | 128  |                                                                                   |
| Endereço de Destino                 | 128  |                                                                                   |

### O que mudou do IPv4 para o IPv6, campo a campo

| Situação          | Campos                                                                 |
|-------------------|------------------------------------------------------------------------|
| ✅ **Mantidos**   | Versão · Endereço de origem · Endereço de destino (agora com 128 bits) |
| 🔄 **Renomeados** | ToS → **Classe de Tráfego** · Tamanho Total → **Tamanho dos Dados** · Protocolo → **Próximo Cabeçalho** · TTL → **Limite de Saltos** |
| 🆕 **Novo**       | **Identificador de Fluxo** (Flow Label)                                |
| ❌ **Removidos**  | IHL · Identificação · Flags · Deslocamento do Fragmento · Checksum do Cabeçalho · Opções |

Por que foram removidos:
- **IHL:** o cabeçalho tem tamanho fixo, então não precisa informar o tamanho
- **Identificação, Flags e Deslocamento:** a fragmentação saiu do cabeçalho
  base e foi para um **cabeçalho de extensão**, usado só pela origem
- **Checksum:** as camadas de enlace e de transporte já verificam erros;
  sem ele, o roteador não precisa recalcular nada a cada salto
- **Opções:** viraram **cabeçalhos de extensão**

> **Questão CESPE (gabarito ERRADO):** *"Em comparação ao IPv4, o IPv6 é
> maior no que se refere à parte obrigatória do cabeçalho e ao número de
> campos."*
> O cabeçalho é **maior em bytes** (40 contra 20), mas tem **menos
> campos**. Por isso a afirmação está errada.

### Jumbogramas

O *Payload Length* tem 16 bits, limitando os dados a **65.535 bytes**.
Pacotes maiores (**jumbogramas**) usam a opção *Jumbo Payload* no
cabeçalho de extensão **Hop-by-Hop**.

## Fragmentação

- **Só a origem fragmenta.** Roteadores intermediários **não fragmentam**.
- Se o pacote for maior que o MTU do próximo link, o roteador **descarta**
  e envia ao remetente um **ICMPv6 "Packet Too Big"**.
- A origem ajusta o tamanho usando o **Path MTU Discovery**.
- MTU mínimo no IPv6: **1280 bytes**.

> Por isso o **ICMPv6 não pode ser bloqueado** no firewall —
> sem ele, o IPv6 não funciona direito.

## IPSec

Na especificação original o suporte a IPSec era **obrigatório**. Desde a
**RFC 6434 (2011)** passou a ser **recomendado**. Em prova, muitas bancas
ainda consideram **"obrigatório no IPv6, opcional no IPv4"**.

## ICMPv6 e NDP

O ICMPv6 assumiu funções que no IPv4 eram de outros protocolos:

| IPv4 | IPv6 (via ICMPv6)                      |
|------|----------------------------------------|
| ARP  | **NDP** — Neighbor Discovery           |
| IGMP | **MLD** — Multicast Listener Discovery |

Funções principais: **NDP**, **MLD**, **Path MTU Discovery**,
**autoconfiguração stateless (SLAAC)** e suporte à **mobilidade (MIPv6)**.

### Mensagens do NDP

| Mensagem                    | Tipo | Quem envia / para quê                                                                         |
|-----------------------------|------|-----------------------------------------------------------------------------------------------|
| Router Solicitation (RS)    | 133  | **Host** pede aos roteadores que se anunciem                                                  |
| Router Advertisement (RA)   | 134  | **Roteador** anuncia prefixo, gateway, MTU, hop limit — periodicamente ou em resposta a um RS |
| Neighbor Solicitation (NS)  | 135  | Descobre o **MAC de um IPv6** (antigo ARP), testa se o vizinho está ativo e detecta **endereço duplicado (DAD)** |
| Neighbor Advertisement (NA) | 136  | Resposta ao NS, ou aviso de mudança                                                           |
| Redirect                    | 137  | Roteador informa que há um **caminho melhor**                                                 |

## Autoconfiguração e DHCPv6

- **SLAAC (stateless):** o host recebe o **prefixo** pelo RA do roteador e
  monta sozinho o **identificador da interface**. Antes de usar, confirma
  com **DAD** que ninguém tem o mesmo endereço.
- **DHCPv6 (stateful):** um servidor distribui e **registra** os endereços,
  como no IPv4.

> **Pegadinha de prova:** o DHCPv6 **não é necessário para o host se
> conectar** (o SLAAC resolve), mas **não é inútil**: dá controle
> centralizado e entrega outras informações, como servidores DNS.

## Transição IPv4 → IPv6

| Técnica                      | Como funciona                                                    |
|------------------------------|------------------------------------------------------------------|
| **Pilha dupla** (Dual Stack) | O equipamento roda IPv4 e IPv6 ao mesmo tempo. **A mais usada.** |
| **Túneis**                   | Pacote IPv6 vai **dentro** de um pacote IPv4 para atravessar redes só IPv4. Ex.: 6in4, **6to4** (`2002::/16`), **Teredo** (`2001::/32`, atravessa NAT usando UDP), ISATAP |
| **Tradução**                 | Converte IPv6 ↔ IPv4. Ex.: **NAT64 + DNS64** (prefixo `64:FF9B::/96`) |

### IPv4 mapeado em IPv6

Um IPv4 pode ser representado como IPv6 com o prefixo `::FFFF:`.
Exemplo com `192.168.1.2`:

```
192 = C0 · 168 = A8 · 1 = 01 · 2 = 02
→ ::FFFF:C0A8:102
→ ou, de forma mista: ::FFFF:192.168.1.2
```

## Resumo IPv4 × IPv6

| IPv4                              | IPv6                                                  |
|-----------------------------------|-------------------------------------------------------|
| 32 bits, decimal                  | 128 bits, hexadecimal                                 |
| Cabeçalho variável (20–60 bytes)  | Cabeçalho fixo (40 bytes), 8 campos                   |
| Opções no cabeçalho               | Cabeçalhos de extensão                                |
| Roteadores fragmentam             | Só a origem fragmenta                                 |
| IPSec opcional                    | IPSec obrigatório (original) / recomendado (RFC 6434) |
| Unicast, multicast, **broadcast** | Unicast, multicast, **anycast** — sem broadcast       |
| ARP (usa broadcast)               | NDP (usa multicast)                                   |
| IGMP                              | MLD                                                   |
| Configuração manual ou DHCP       | SLAAC, DHCPv6 ou manual                               |
| QoS pelo campo ToS                | Classe de Tráfego + Flow Label                        |

---
Conteúdo sob licença CC BY 4.0 — Luis Gustavo dos Reis Kort Kamp.
