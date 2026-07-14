Disciplina: **ENE0025 – Protocolos de Transporte e Roteamento**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 06 - Roteamento Externo via BGP

## Identificação

- Nome: Rafael Hossoe Dantas Pinto
- Matrícula: 231036130
- Turma: 1

## Objetivo

Configurar o protocolo **BGP** no roteador da empresa para que ela possa **anunciar seu prefixo público à Internet** por meio de seus provedores.

## Ambiente experimental

- 10 Cisco IOL - L3-ADVENTERPRISEK9-M-15.4-2T.bin
- 1 Cisco IOL - L2-ADVENTERPRISEK9-M-15.2-20150703.bin

## Topologia Lógica

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-13%20121947.png)

## Procedimentos

Foi dado os comandos para cada dispositivo a seguir:

### R1

```
enable
configure terminal

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

interface Loopback1
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

ip route 11.11.11.11 255.255.255.255 Ethernet0/0 10.1.0.1
ip route 11.11.11.11 255.255.255.255 Ethernet0/1 10.1.0.5

router bgp 100
bgp log-neighbor-changes
neighbor 11.11.11.11 remote-as 1000
neighbor 11.11.11.11 password SENHA
neighbor 11.11.11.11 update-source Loopback1
neighbor 11.11.11.11 ebgp-multihop 2
neighbor 191.1.0.2 remote-as 300

end
write
```

### ISP2

```
enable
configure terminal

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

end
write
```

### ISP3

```
enable
configure terminal

interface Ethernet 0/0
ip address 191.2.0.2 255.255.255.252
no shutdown

interface Ethernet 0/1
ip address 191.1.0.2 255.255.255.252
no shutdown

interface Loopback181
ip address 181.0.0.1 255.0.0.0
interface Loopback182
ip address 182.0.0.1 255.0.0.0
interface Loopback183
ip address 183.0.0.1 255.0.0.0
interface Loopback184
ip address 184.0.0.1 255.0.0.0
interface Loopback185
ip address 185.0.0.1 255.0.0.0

router bgp 300
bgp log-neighbor-changes
neighbor 191.1.0.1 remote-as 100
neighbor 191.2.0.1 remote-as 200
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

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20121339.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20121351.png)

##### ISP1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122328.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122342.png)

##### ISP2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122406.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122419.png)

##### ISP3

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122440.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122456.png)

##### R_LAN

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/e257c03ce056434e0b8ece7fdc0286a4d8ed0d0d/Imagens%20Lab%206/Captura%20de%20tela%202026-07-14%20122523.png)

## Análise técnica

###### Qual é a função do BGP nesse cenário?
Neste cenário, o BGP (Border Gateway Protocol) atua como o protocolo de roteamento dinâmico externo (eBGP) responsável pela troca de informações de roteamento entre diferentes Sistemas Autônomos (AS 1000, AS 100, AS 200 e AS 300). A sua principal função é permitir que a empresa (AS 1000) anuncie o seu prefixo público designado (200.18.245.64/27) para a internet global através de seus provedores de trânsito (ISP1 e ISP2), ao mesmo tempo em que aprende as rotas dos prefixos públicos externos (181.0.0.0/8 a 185.0.0.0/8) originados no AS 300.
###### Por que a sessão com o ISP1 usa endereço de loopback?
Neste cenário, o BGP (Border Gateway Protocol) atua como o protocolo de roteamento dinâmico externo (eBGP) responsável pela troca de informações de roteamento entre diferentes Sistemas Autônomos (AS 1000, AS 100, AS 200 e AS 300). A sua principal função é permitir que a empresa (AS 1000) anuncie o seu prefixo público designado (200.18.245.64/27) para a internet global através de seus provedores de trânsito (ISP1 e ISP2), ao mesmo tempo em que aprende as rotas dos prefixos públicos externos (181.0.0.0/8 a 185.0.0.0/8) originados no AS 300.
###### Por que foi necessário configurar `ebgp-multihop 2`?
Neste cenário, o BGP (Border Gateway Protocol) atua como o protocolo de roteamento dinâmico externo (eBGP) responsável pela troca de informações de roteamento entre diferentes Sistemas Autônomos (AS 1000, AS 100, AS 200 e AS 300). A sua principal função é permitir que a empresa (AS 1000) anuncie o seu prefixo público designado (200.18.245.64/27) para a internet global através de seus provedores de trânsito (ISP1 e ISP2), ao mesmo tempo em que aprende as rotas dos prefixos públicos externos (181.0.0.0/8 a 185.0.0.0/8) originados no AS 300.
###### Qual a função do `update-source Loopback1`?
Neste cenário, o BGP (Border Gateway Protocol) atua como o protocolo de roteamento dinâmico externo (eBGP) responsável pela troca de informações de roteamento entre diferentes Sistemas Autônomos (AS 1000, AS 100, AS 200 e AS 300). A sua principal função é permitir que a empresa (AS 1000) anuncie o seu prefixo público designado (200.18.245.64/27) para a internet global através de seus provedores de trânsito (ISP1 e ISP2), ao mesmo tempo em que aprende as rotas dos prefixos públicos externos (181.0.0.0/8 a 185.0.0.0/8) originados no AS 300.
###### Por que foi criada a rota `ip route 200.18.245.64 255.255.255.224 Null0`?
Neste cenário, o BGP (Border Gateway Protocol) atua como o protocolo de roteamento dinâmico externo (eBGP) responsável pela troca de informações de roteamento entre diferentes Sistemas Autônomos (AS 1000, AS 100, AS 200 e AS 300). A sua principal função é permitir que a empresa (AS 1000) anuncie o seu prefixo público designado (200.18.245.64/27) para a internet global através de seus provedores de trânsito (ISP1 e ISP2), ao mesmo tempo em que aprende as rotas dos prefixos públicos externos (181.0.0.0/8 a 185.0.0.0/8) originados no AS 300.
###### Qual a diferença entre o pareamento com o ISP1 e com o ISP2?
Neste cenário, o BGP (Border Gateway Protocol) atua como o protocolo de roteamento dinâmico externo (eBGP) responsável pela troca de informações de roteamento entre diferentes Sistemas Autônomos (AS 1000, AS 100, AS 200 e AS 300). A sua principal função é permitir que a empresa (AS 1000) anuncie o seu prefixo público designado (200.18.245.64/27) para a internet global através de seus provedores de trânsito (ISP1 e ISP2), ao mesmo tempo em que aprende as rotas dos prefixos públicos externos (181.0.0.0/8 a 185.0.0.0/8) originados no AS 300.
## Conclusão
A realização do Experimento 06 proporcionou o entendimento prático do funcionamento do protocolo Border Gateway Protocol (BGP) e de sua aplicação como pilar de interconexão entre diferentes Sistemas Autônomos (AS). A implementação permitiu analisar de forma nítida os mecanismos que asseguram a estabilidade operacional das bordas de rede e a propagação de rotas públicas.
A configuração das sessões eBGP com o ISP1 ilustrou a importância de estratégias de resiliência e alta disponibilidade por meio do uso de interfaces de Loopback, evidenciando como comandos auxiliares como update-source e ebgp-multihop são essenciais para contornar limitações de TTL e garantir o estabelecimento de sessões indiretas de forma estável. Adicionalmente, compreendeu-se na prática o comportamento restritivo do comando network e como a utilização de rotas direcionadas ao Null0 age como elemento facilitador para viabilizar o anúncio seguro de blocos IP corporativos sumarizados para a infraestrutura de trânsito dos provedores.
