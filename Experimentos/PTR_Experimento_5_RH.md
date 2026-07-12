Disciplina: **ENE0025 – Protocolos de Transporte e Roteamento**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 5 - Roteamento Dinâmico com RIP e OSPF

## Identificação

- Nome: Rafael Hossoe Dantas Pinto
- Matrícula: 231036130
- Turma: 1

## Objetivo

Configurar e validar o roteamento dinâmico em uma topologia com três roteadores, comparando o funcionamento dos protocolos **RIP** e **OSPF** em um mesmo cenário.

## Ambiente experimental

- 3 Cisco IOL - L3-ADVENTERPRISEK9-M-15.4-2T.bin
- 6 Cisco IOL - L2-ADVENTERPRISEK9-M-15.2-20150703.bin
- 12 VPCs

### Topologia Lógica

![Captura de tela 2026-07-12 181304.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20181304.png)

## Procedimentos

Foi dado os comandos para cada dispositivo a seguir:

### R - RJ

```
en
conf t

int e0/0
ip add 172.16.100.1 255.255.255.0
no shut
int e0/1
ip add 172.16.10.254 255.255.255.0
no shut
int e0/2
ip add 172.16.20.254 255.255.255.0
no shut

Situação com RIP
router rip
network 172.16.0.0

Situação com OSPF
no router rip
router ospf 64
network 172.16.10.0 0.0.0.255 area 0
network 172.16.20.0 0.0.0.255 area 0
network 172.16.100.0 0.0.0.255 area 0

end
wr
```

### R - SP

```
en
conf t

int e0/0
ip add 172.16.100.2 255.255.255.0
no shut
int e0/1
ip add 172.16.200.1 255.255.255.0
no shut
int e0/2
ip add 172.16.30.254 255.255.255.0
no shut
int e0/3
ip add 172.16.40.254 255.255.255.0
no shut

Situação com RIP
router rip
network 172.16.0.0

Situação com OSPF
no router rip
router ospf 65
network 172.16.30.0 0.0.0.255 area 0
network 172.16.40.0 0.0.0.255 area 0
network 172.16.100.0 0.0.0.255 area 0
network 172.16.200.0 0.0.0.255 area 0

end
wr
```

### R - BH

```
en
conf t

int e0/0
ip add 172.16.200.2 255.255.255.0
no shut
int e0/1
ip add 172.16.50.254 255.255.255.0
no shut
int e0/2
ip add 172.16.60.254 255.255.255.0
no shut

Situação com RIP
router rip
network 172.16.0.0

Situação com OSPF
no router rip
router ospf 66
network 172.16.50.0 0.0.0.255 area 0
network 172.16.60.0 0.0.0.255 area 0
network 172.16.200.0 0.0.0.255 area 0

end
wr
```

### VPC - RJ - 1

```
ip 172.16.10.1/24 172.16.10.254
save
```

### VPC - RJ - 2

```
ip 172.16.10.2/24 172.16.10.254
save
```

### VPC - RJ - 3

```
ip 172.16.20.1/24 172.16.20.254
save
```

### VPC - RJ - 4

```
ip 172.16.20.2/24 172.16.20.254
save
```

### VPC - SP - 1

```
ip 172.16.30.1/24 172.16.30.254
save
```

### VPC - SP - 2

```
ip 172.16.30.2/24 172.16.30.254
save
```

### VPC - SP - 3

```
ip 172.16.40.1/24 172.16.40.254
save
```

### VPC - SP - 4

```
ip 172.16.40.2/24 172.16.40.254
save
```

### VPC - BH - 1

```
ip 172.16.50.1/24 172.16.50.254
save
```

### VPC - BH - 2

```
ip 172.16.50.2/24 172.16.50.254
save
```

### VPC - BH - 3

```
ip 172.16.60.1/24 172.16.50.254
save
```

### VPC - BH - 4

```
ip 172.16.60.2/24 172.16.50.254
save
```

## Resultados e evidências

## Verificação do RIP

##### R - RJ

![Captura de tela 2026-07-12 184420.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184420.png)

![Captura de tela 2026-07-12 184551.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184551.png)

![Captura de tela 2026-07-12 184658.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184658.png)

##### R - SP

![Captura de tela 2026-07-12 184455.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184455.png)

![Captura de tela 2026-07-12 184558.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184558.png)

![Captura de tela 2026-07-12 184727.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184727.png)

##### R - BH

![Captura de tela 2026-07-12 184514.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184514.png)

![Captura de tela 2026-07-12 184604.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184604.png)

![Captura de tela 2026-07-12 184758.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20184758.png)

## Verificação do OSPF

##### R - RJ

![Captura de tela 2026-07-12 185131.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185131.png)

![Captura de tela 2026-07-12 185139.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185139.png)

![Captura de tela 2026-07-12 185210.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185210.png)

##### R - SP

![Captura de tela 2026-07-12 185238.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185238.png)

![Captura de tela 2026-07-12 185254.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185254.png)

![Captura de tela 2026-07-12 185318.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185318.png)

##### R - BH

![Captura de tela 2026-07-12 185340.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185340.png)

![Captura de tela 2026-07-12 185356.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185356.png)

![Captura de tela 2026-07-12 185422.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20185422.png)

### Teste de alcance entre unidades

##### VPC - RJ - 1

![Captura de tela 2026-07-12 191955.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20191955.png)

### Situação 1 – Remover uma rede do anúncio OSPF em São Paulo

![Captura de tela 2026-07-12 192259.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20192259.png)

### Situação 2 – Interromper o enlace SP-BH

##### R - SP

![Captura de tela 2026-07-12 192441.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20192441.png)

##### R - RJ

![Captura de tela 2026-07-12 192448.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20192448.png)

### Situação 3 – Remover o RIP de um roteador

![Captura de tela 2026-07-12 192633.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-12%20192633.png)

## Análise técnica

## Comparação orientada entre RIP e OSPF

###### Qual protocolo foi mais simples de configurar?

###### Qual protocolo apresentou maior riqueza de informações operacionais?

###### Qual a principal métrica do RIP?

###### Qual algoritmo é usado pelo OSPF?

###### Qual protocolo tende a escalar melhor?

###### Qual protocolo converge melhor em cenários maiores?

### Explicação da Situação 1

### Explicação da Situação 2

### Explicação da Situação 3

## Conclusão
