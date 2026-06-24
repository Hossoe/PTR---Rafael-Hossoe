Disciplina: **ENE0011 – Laboratório de Redes**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 10 : BGP

## Identificação

- Nome: Rafael Hossoe Dantas Pinto
- Matrícula: 231036130
- Turma: 1

## Objetivo

Esse experimento tem como objetivo aprender mais sobre o BGP, no caso, aprendendo sobre o iBGP. Utilizando a estrutura do laboratório anterior, foi alterado para um melhor entendimento sobre como funciona o iBGP em prática.

## Ambiente experimental

### Equipamentos

- 8 Cisco IOL Routers - L3-ADVENTERPRISEK9-M-15.4-2T.bin

- 3 Cisco IOL Switches - L2-ADVENTERPRISEK9-M-15.2-20150703.bin

- 3 VPCs

### Topologia Lógica

![Captura de tela 2026-06-23 181843.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-23%20181843.png)

## Procedimentos

Foi dado os comandos para cada dispositovo a seguir:

### R1

```
enable
configure terminal

interface Ethernet0/0
ip address 172.16.10.1 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 172.16.20.1 255.255.255.0
no shutdown

router ospf 1
network 172.16.0.0 0.0.255.255 area 0

router bgp 3200
neighbor 172.16.30.1 remote-as 3200

end
write
copy running-config startup-config
```

### R2

```
enable
configure terminal

interface Ethernet0/0
ip address 172.16.20.2 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 172.16.30.2 255.255.255.0
no shutdown
 
router ospf 1
network 172.16.0.0 0.0.255.255 area 0

router bgp 3200
neighbor 172.16.30.1 remote-as 3200

end
write
copy running-config startup-config
```

### R3

```
enable
configure terminal

interface Loopback0
ip address 172.16.0.1 255.255.255.0

interface Ethernet0/0
ip address 172.16.30.1 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 172.31.20.1 255.255.255.0
no shutdown

interface Ethernet0/2
ip address 172.31.10.1 255.255.255.0
no shutdown

router bgp 3200
bgp router-id 172.16.0.1
network 172.16.0.0
neighbor INTRA-3200 peer-group
neighbor INTRA-3200 remote-as 3200
neighbor INTRA-3200 default-originate
neighbor 172.16.20.1 peer-group INTRA-3200
neighbor 172.16.30.2 peer-group INTRA-3200
neighbor 172.31.10.2 remote-as 4100
neighbor 172.31.20.2 remote-as 1900

ip route 172.16.0.0 255.255.0.0 Null0

end
write
copy running-config startup-config
```

### R4

```
enable
configure terminal

interface Loopback0
ip address 192.168.0.1 255.255.255.0

interface Ethernet0/0
ip address 172.31.20.2 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 172.31.30.2 255.255.255.0
no shutdown

interface Ethernet0/2
ip address 192.168.30.1 255.255.255.0
no shutdown

router ospf 1
network 192.168.0.0 0.0.255.255 area 0

router bgp 1900
bgp router-id 192.168.0.1
network 192.168.0.0 mask 255.255.0.0
neighbor INTRA-1900 peer-group
neighbor INTRA-1900 remote-as 1900
neighbor INTRA-1900 default-originate
neighbor 172.31.20.1 remote-as 3200
neighbor 172.31.30.1 remote-as 4100
neighbor 192.168.20.1 peer-group INTRA-1900
neighbor 192.168.30.2 peer-group INTRA-1900

ip route 192.168.0.0 255.255.0.0 Null0

end
write
copy running-config startup-config
```

### R5

```
enable
configure terminal

interface Ethernet0/0
ip address 192.168.30.2 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 192.168.20.2 255.255.255.0
no shutdown

router ospf 1
network 192.168.0.0 0.0.255.255 area 0

router bgp 1900
neighbor 192.168.30.1 remote-as 1900
 
end
write
copy running-config startup-config
```

### R6

```
enable
configure terminal

interface Ethernet0/0
ip address 192.168.20.1 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 192.168.10.1 255.255.255.0
no shutdown

router ospf 1
network 192.168.0.0 0.0.255.255 area 0

router bgp 1900
neighbor 192.168.30.1 remote-as 1900

end
write
copy running-config startup-config
```

### R7

```
enable
configure terminal

interface Loopback0
ip address 10.10.0.1 255.255.255.0

interface Ethernet0/0
ip address 172.31.10.2 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 172.31.30.1 255.255.255.0
no shutdown

interface Ethernet0/2
ip address 10.10.10.1 255.255.255.0
no shutdown

router ospf 1
network 10.10.0.0 0.0.255.255 area 0

router bgp 4100
bgp router-id 10.10.0.1
network 10.10.0.0 mask 255.255.0.0
neighbor INTRA-4100 peer-group
neighbor INTRA-4100 remote-as 4100
neighbor INTRA-4100 default-originate
neighbor 10.10.10.2 peer-group INTRA-4100
neighbor 172.31.10.1 remote-as 3200
neighbor 172.31.30.2 remote-as 1900

ip route 10.10.0.0 255.255.0.0 Null0

end
write
copy running-config startup-config
```

### R8

```
enable
configure terminal

interface Ethernet0/0
ip address 10.10.10.2 255.255.255.0
no shutdown

interface Ethernet0/1
ip address 10.10.20.1 255.255.255.0
no shutdown

router ospf 1
network 10.10.0.0 0.0.255.255 area 0

router bgp 4100
neighbor 10.10.10.1 remote-as 4100

end
write
copy running-config startup-config
```

### VPC1

```
ip 172.16.10.2/24 172.16.10.1
```

### VPC2

```
ip 192.168.10.2/24 192.168.10.1
```

### VPC3

```
ip 10.10.20.2/24 10.10.20.1
```

## Resultados e evidências

Depois de configurar a rede foi observado as tabelas de roteamento dos roteadores mostradas a seguir:

### R1

![Captura de tela 2026-06-24 154412.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154412.png)

![Captura de tela 2026-06-24 154426.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154426.png)

![Captura de tela 2026-06-24 154703.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154703.png)

### R2

![Captura de tela 2026-06-24 154731.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154731.png)

![Captura de tela 2026-06-24 154743.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154743.png)

![Captura de tela 2026-06-24 154804.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154804.png)

### R3

![Captura de tela 2026-06-24 154841.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154841.png)

![Captura de tela 2026-06-24 154853.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154853.png)

![Captura de tela 2026-06-24 154911.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154911.png)

### R4

![Captura de tela 2026-06-24 154935.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154935.png)

![Captura de tela 2026-06-24 154953.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20154953.png)

![Captura de tela 2026-06-24 155007.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155007.png)

### R5

![Captura de tela 2026-06-24 155028.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155028.png)

![Captura de tela 2026-06-24 155044.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155044.png)

![Captura de tela 2026-06-24 155101.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155101.png)

### R6

![Captura de tela 2026-06-24 155118.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155118.png)

![Captura de tela 2026-06-24 155131.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155131.png)

![Captura de tela 2026-06-24 155145.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155145.png)

### R7

![Captura de tela 2026-06-24 155206.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155206.png)

![Captura de tela 2026-06-24 155219.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155219.png)

![Captura de tela 2026-06-24 155235.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155235.png)

### R8

![Captura de tela 2026-06-24 155252.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155252.png)

![Captura de tela 2026-06-24 155304.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155304.png)

![Captura de tela 2026-06-24 155317.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155317.png)

Também foi testado a conectividade dos hosts de cada AS entre si:

### VPC1

![Captura de tela 2026-06-24 155440.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155440.png)

### VPC2

![Captura de tela 2026-06-24 155446.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155446.png)

### VPC3

![Captura de tela 2026-06-24 155452.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-06-24%20155452.png)

## Análise técnica

Com base nos comandos feitos para cada dispositivo e pelas analíses da rede, é possível perceber que ela funciona da maneira esperada, dita no laboratório. Os VPCs de cada AS conseguem se comunicar entre si tendo em vista que o comando ping funcionou. Também é possivel perceber que cada roteador possui seus devidos vizinhos, seja os roteadores de bordas se comunicando com o eBGP e os da mesma AS com o iBGP.

## Conclusão

Com isso podemos entender na prática o funcionamento do iBGP, tendo em vista que as rotas dentro da própria AS foi feita com sucesso e os seus roteadores de borda se comunicam entre si, não tendo um trafégo descontrolado em cada rede. Ou seja, cada ponta das AS possui um controlador de tráfego da parte externa para a interna (eBGP para o iBGP). 
