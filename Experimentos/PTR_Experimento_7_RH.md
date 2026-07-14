Disciplina: **ENE0025 – Protocolos de Transporte e Roteamento**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 7 - # Configuração dos Provedores (BGP Externo)

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

![](C:/Users/rafah/AppData/Roaming/marktext/images/2026-07-14-17-00-54-image.png)

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

![Captura de tela 2026-07-14 165410.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165410.png)

![Captura de tela 2026-07-14 165426.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165426.png)

![Captura de tela 2026-07-14 165534.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165534.png)

##### ISP1

![Captura de tela 2026-07-14 165605.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165605.png)

![Captura de tela 2026-07-14 165613.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165613.png)

![Captura de tela 2026-07-14 165629.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165629.png)

##### ISP2

![Captura de tela 2026-07-14 165722.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165722.png)

![Captura de tela 2026-07-14 165736.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165736.png)

![Captura de tela 2026-07-14 165757.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165757.png)

##### ISP3

![Captura de tela 2026-07-14 165807.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165807.png)

![Captura de tela 2026-07-14 165818.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165818.png)

![Captura de tela 2026-07-14 165837.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165837.png)

##### R_LAN

![Captura de tela 2026-07-14 165854.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165854.png)

![Captura de tela 2026-07-14 165910.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-14%20165910.png)

## Análise técnica

## Conclusão
