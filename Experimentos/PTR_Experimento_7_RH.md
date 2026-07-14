Disciplina: **ENE0025 – Protocolos de Transporte e Roteamento**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 7 - Configuração dos Provedores (BGP Externo)

## Identificação

- Nome: Rafael Hossoe Dantas Pinto
- Matrícula: 231036130
- Turma: 1

## Objetivo

Configurar os roteadores **ISP1**, **ISP2** e **ISP3** para permitir o funcionamento completo do cenário de **BGP externo**.

## Ambiente experimental

- 10 Cisco IOL - L3-ADVENTERPRISEK9-M-15.4-2T.bin
- 1 Cisco IOL - L2-ADVENTERPRISEK9-M-15.2-20150703.bin

## Topologia Lógica

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20170050.png)

## Procedimentos

Foi dado os comandos para cada dispositivo a seguir:

### R1

```
enable
configure terminal
no ip domain lookup

interface Loopback1
ip address 11.11.11.11 255.255.255.255
no shutdown
interface Ethernet 0/0
ip address 192.168.0.1 255.255.255.0
no shutdown
interface Ethernet 0/1
ip address 10.1.0.1 255.255.255.252
no shutdown
interface Ethernet 0/2
ip address 10.1.0.5 255.255.255.252
no shutdown
interface Ethernet 0/3
ip address 10.2.0.1 255.255.255.252
no shutdown
exit

ip route 10.10.10.10 255.255.255.255 Ethernet0/1 10.1.0.2
ip route 10.10.10.10 255.255.255.255 Ethernet0/2 10.1.0.6
ip route 200.18.245.64 255.255.255.224 Null0

router bgp 1000
bgp log-neighbor-changes
neighbor 10.10.10.10 remote-as 100
neighbor 10.10.10.10 password SENHA
neighbor 10.10.10.10 update-source Loopback1
neighbor 10.10.10.10 ebgp-multihop 2
neighbor 10.2.0.2 remote-as 200
neighbor 10.2.0.2 password SENHA
network 200.18.245.64 mask 255.255.255.224

end
write
```

### ISP1

```
enable
configure terminal
no ip domain lookup

interface Loopback0
ip address 10.10.10.10 255.255.255.255
no shutdown
interface Ethernet 0/0
ip address 10.1.0.2 255.255.255.252
no shutdown
interface Ethernet 0/1
ip address 10.1.0.6 255.255.255.252
no shutdown
interface Ethernet 0/2
ip address 191.1.0.1 255.255.255.252
no shutdown
exit

ip route 11.11.11.11 255.255.255.255 Ethernet0/0 10.1.0.1
ip route 11.11.11.11 255.255.255.255 Ethernet0/1 10.1.0.5

router bgp 100
bgp log-neighbor-changes
neighbor 11.11.11.11 remote-as 1000
neighbor 11.11.11.11 password SENHA
neighbor 11.11.11.11 update-source Loopback0
neighbor 11.11.11.11 ebgp-multihop 2
neighbor 191.1.0.2 remote-as 300
neighbor 191.1.0.2 password SENHA
network 10.10.10.10 mask 255.255.255.255

end
write
```

### ISP2

```
enable
configure terminal
no ip domain lookup

interface Ethernet 0/0
ip address 10.2.0.2 255.255.255.252
no shutdown
interface Ethernet 0/1
ip address 191.2.0.1 255.255.255.252
no shutdown

router bgp 200
bgp log-neighbor-changes
neighbor 10.2.0.1 remote-as 1000
neighbor 10.2.0.1 password SENHA
neighbor 191.2.0.2 remote-as 300
neighbor 191.2.0.2 password SENHA

end
write
```

### ISP3

```
enable
configure terminal
no ip domain lookup

interface Ethernet 0/0
ip address 191.2.0.2 255.255.255.252
no shutdown
interface Ethernet 0/1
ip address 191.1.0.2 255.255.255.252
no shutdown
interface Loopback1
ip address 181.0.0.1 255.0.0.0
interface Loopback2
ip address 182.0.0.1 255.0.0.0
interface Loopback3
ip address 183.0.0.1 255.0.0.0
interface Loopback4
ip address 184.0.0.1 255.0.0.0
interface Loopback5
ip address 185.0.0.1 255.0.0.0
exit

router bgp 300
bgp log-neighbor-changes
neighbor 191.1.0.1 remote-as 100
neighbor 191.1.0.1 password SENHA
neighbor 191.2.0.1 remote-as 200
neighbor 191.2.0.1 password SENHA
network 181.0.0.0 mask 255.0.0.0
network 182.0.0.0 mask 255.0.0.0
network 183.0.0.0 mask 255.0.0.0
network 184.0.0.0 mask 255.0.0.0
network 185.0.0.0 mask 255.0.0.0

end
write
```

### R_LAN

```
enable
configure terminal

interface Ethernet 0/0
ip address 192.168.0.2 255.255.255.0
no shutdown
exit

ip route 0.0.0.0 0.0.0.0 192.168.0.1

end
write
```

### SW1

```
enable
configure terminal
no ip routing

interface range Ethernet 0/0 - 1
switchport mode access
switchport access vlan 1
no shutdown

end
write
```

## Resultados e evidências

##### R1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165410.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165426.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165534.png)

##### ISP1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165605.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165613.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165629.png)

##### ISP2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165722.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165736.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165757.png)

##### ISP3

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165807.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165818.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165837.png)

##### R_LAN

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165854.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/3cb3435c03077afce1d10263eae65c70eff6426f/Imagens%20Lab%207/Captura%20de%20tela%202026-07-14%20165910.png)

## Análise técnica

A análise do experimento permite compreender a operação prática do protocolo **BGP (Border Gateway Protocol)** em cenários de borda multihomed e a interação entre diferentes Sistemas Autônomos (AS). Abaixo, são discutidos os principais aspectos de engenharia de tráfego e estabilização de sessões aplicados na topologia:

### 1. Estabelecimento de Sessão eBGP Multihop via Loopback (R1 ↔ ISP1)
Uma das técnicas mais importantes aplicadas no laboratório foi a vizinhança eBGP indireta estabelecida entre a `Loopback1` do R1 (`11.11.11.11`) e a `Loopback0` do ISP1 (`10.10.10.10`). 

* **Finalidade e Redundância:** R1 e ISP1 são interconectados por dois links físicos distintos (`10.1.0.0/30` e `10.1.0.4/30`). Caso a sessão BGP fosse fechada utilizando os IPs das interfaces físicas, a queda de um dos cabos físicos derrubaria imediatamente a adjacência. O uso de interfaces de Loopback (que são virtuais e permanecem ativas enquanto o roteador estiver ligado) garante a resiliência do pareamento.
* **Comandos de Suporte:** 
  * O comando `ebgp-multihop 2` foi indispensável porque, por padrão, pacotes eBGP são gerados com TTL (*Time-to-Live*) igual a 1, assumindo conexão física direta. Como as interfaces de Loopback estão logicamente a dois saltos de distância (passando pela interface física de saída e alcançando a interface virtual interna do par), o incremento do TTL para 2 viabilizou a entrega dos pacotes de controle TCP.
  * O comando `update-source` instruiu cada roteador a usar o IP de sua respectiva Loopback como endereço de origem nas mensagens de controle. Sem isso, a sessão TCP falharia, pois o roteador remoto rejeitaria conexões vindas de um IP diferente daquele configurado no comando `neighbor`.
  * As rotas estáticas criadas apontando para os endereços das Loopbacks através dos dois enlaces físicos atuaram como base para que o tráfego de controle pudesse ser roteado e redundante.

### 2. Pareamento Direto e Simplicidade (R1 ↔ ISP2)
Em contrapartida à complexidade da sessão com o ISP1, a vizinhança entre R1 e ISP2 (`AS 200`) foi estabelecida de maneira direta por meio do enlace físico na sub-rede `10.2.0.0/30`. Essa configuração dispensa o uso de comandos como `ebgp-multihop` ou `update-source`, pois a conectividade é de salto único. Contudo, ela introduz um ponto único de falha: se a interface física ou o cabo falharem, a adjacência eBGP cairá imediatamente, ao contrário do comportamento resiliente observado no ISP1.

### 3. Anúncio e a Rota para Null0
O BGP possui uma regra estrita de validação: ele só anuncia um prefixo inserido pelo comando `network` se esse bloco IP exato constar ativamente na tabela de roteamento do dispositivo. 
Como o prefixo corporativo alocado para o R1 é o bloco sumarizado `200.18.245.64/27`, e as sub-redes reais do ambiente interno podem estar divididas de outra forma ou temporariamente indisponíveis (como em falhas locais), o roteador não o anunciaria de forma estável. A criação da rota estática `ip route 200.18.245.64 255.255.255.224 Null0` serviu como uma âncora estática permanente. Ela garante a presença do prefixo corporativo `/27` na tabela de roteamento local do R1, permitindo o anúncio contínuo aos provedores (ISP1 e ISP2) de forma imune a flutuações das interfaces internas.

### 4. Distribuição das Rotas Externas da Internet (AS 300)
O ISP3 representou o *core* da internet global, originando e propagando as redes classe A (`181.0.0.0/8` a `185.0.0.0/8`) criadas em suas interfaces de loopback. Pelo fluxo de propagação estabelecido, as rotas foram enviadas via eBGP para o ISP1 e ISP2 e, posteriormente, repassadas para o R1. Como resultado das configurações aplicadas, foi possível observar o R1 recebendo com sucesso essas rotas externas pelos seus dois caminhos (através do AS 100 e AS 200).

## Conclusão

O Experimento 7 permitiu consolidar de forma prática os principais conceitos teóricos relativos ao funcionamento do protocolo **BGP externo (eBGP)** e à arquitetura de conexões *multihomed* corporativas. A atividade proporcionou uma visão nítida de como grandes sistemas autônomos se interconectam e trocam informações de roteamento global de maneira dinâmica.

Através do laboratório, tornou-se evidente a importância de técnicas de engenharia de tráfego para a resiliência corporativa. A implementação de sessões eBGP por interfaces de Loopback com múltiplos caminhos físicos — viabilizada pelos ajustes de TTL (`ebgp-multihop`) e de origem de pacotes (`update-source`) — demonstrou ser um mecanismo robusto para evitar que instabilidades físicas em enlaces locais afetem a conectividade de borda da rede. Além disso, a utilização estratégica de rotas estáticas direcionadas à interface virtual de descarte (`Null0`) evidenciou-se crucial para garantir que o bloco público corporativo fosse sumarizado e divulgado de maneira consistente para os provedores ISP1 e ISP2.

Com a conclusão das configurações, a infraestrutura convergiu com sucesso: todas as sessões eBGP atingiram o estado estável de *Established*, permitindo que o host local na rede interna corporativa (`R_LAN`) recuperasse a capacidade de visibilidade e comunicação com os prefixos de simulação da internet (`AS 300`) de forma totalmente automatizada.
