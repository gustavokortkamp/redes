# VLAN — Virtual LAN

## O que é

VLAN (Virtual Local Area Network) é uma forma de dividir **um mesmo switch
físico em várias redes lógicas separadas**. Os equipamentos em VLANs
diferentes não se comunicam diretamente, como se estivessem em switches
diferentes, mesmo estando ligados no mesmo equipamento.

## Para que serve

- **Segurança:** separa setores (ex.: administrativo, alunos, câmeras, Wi-Fi visitantes)
- **Desempenho:** reduz o domínio de broadcast — cada VLAN tem o seu
- **Organização:** agrupa equipamentos por função, não por localização física
- **Economia:** um switch atende várias redes, sem precisar de um para cada

## Conceitos principais

### Padrão 802.1Q
É o padrão que define as VLANs em redes Ethernet. Ele adiciona ao quadro
uma **tag de 4 bytes** com o número da VLAN (VLAN ID), que vai de **1 a 4094**.

### Porta de acesso (access / untagged)
Pertence a **uma única VLAN**. Usada para ligar computadores, impressoras,
câmeras. O equipamento final nem sabe que existe VLAN — o quadro sai
**sem tag**.

### Porta tronco (trunk / tagged)
Transporta **várias VLANs** ao mesmo tempo, com tag 802.1Q. Usada entre
switches, ou entre switch e roteador/firewall/access point.

### VLAN nativa
Em uma porta tronco, é a VLAN cujo tráfego passa **sem tag**. Por padrão
costuma ser a VLAN 1. Boa prática: trocar para uma VLAN sem uso.

## Exemplo de cenário

| VLAN | Nome         | Rede            |
|------|--------------|-----------------|
| 10   | ADMIN        | 192.168.10.0/24 |
| 20   | LABORATORIO  | 192.168.20.0/24 |
| 30   | CAMERAS      | 192.168.30.0/24 |
| 99   | GERENCIA     | 192.168.99.0/24 |

## Configuração (exemplo em Cisco IOS)

### Criar as VLANs
```
configure terminal
vlan 10
 name ADMIN
vlan 20
 name LABORATORIO
vlan 30
 name CAMERAS
vlan 99
 name GERENCIA
```

### Porta de acesso (computador do setor administrativo)
```
interface GigabitEthernet0/1
 description PC-ADMIN-01
 switchport mode access
 switchport access vlan 10
```

### Porta tronco (ligação com outro switch)
```
interface GigabitEthernet0/24
 description UPLINK-SW-CORE
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
 switchport trunk native vlan 999
```

> Em alguns modelos é preciso definir antes o encapsulamento:
> `switchport trunk encapsulation dot1q`

## Verificação
```
show vlan brief
show interfaces trunk
show interfaces GigabitEthernet0/1 switchport
```

## Comunicação entre VLANs

Por padrão, VLANs diferentes **não conversam**. Para isso é preciso um
equipamento de **camada 3** fazendo o roteamento:

- um **switch L3** com interfaces VLAN (SVI), ou
- um **roteador/firewall** com subinterfaces ("router on a stick").

O firewall é o lugar ideal para controlar **quem pode falar com quem**
(ex.: câmeras só acessam o servidor de gravação).

## Boas práticas

- Não usar a **VLAN 1** para tráfego de usuários
- Criar uma **VLAN de gerência** exclusiva para os equipamentos de rede
- Liberar no tronco **apenas as VLANs necessárias** (`allowed vlan`)
- Trocar a **VLAN nativa** para uma VLAN sem uso
- **Documentar** cada VLAN: ID, nome, rede e finalidade
- Desativar portas sem uso ou colocá-las numa VLAN "morta"

## Erros comuns

- VLAN criada em um switch e esquecida no outro
- VLAN não liberada no tronco → equipamento sem rede
- VLAN nativa diferente nas duas pontas do tronco
- Máscara ou gateway errado no equipamento final

---
Conteúdo sob licença CC BY 4.0 — Luis Gustavo dos Reis Kort Kamp.
