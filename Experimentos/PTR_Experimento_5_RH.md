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

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20181304.png)

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

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184420.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184551.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184658.png)

##### R - SP

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184455.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184558.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184727.png)

##### R - BH

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184514.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184604.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20184758.png)

## Verificação do OSPF

##### R - RJ

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185131.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185139.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185210.png)

##### R - SP

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185238.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185254.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185318.png)

##### R - BH

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185340.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185356.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20185422.png)

### Teste de alcance entre unidades

##### VPC - RJ - 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20191955.png)

### Situação 1 – Remover uma rede do anúncio OSPF em São Paulo

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20192259.png)

### Situação 2 – Interromper o enlace SP-BH

##### R - SP

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20192441.png)

##### R - RJ

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20192448.png)

### Situação 3 – Remover o RIP de um roteador

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a8cf0c51831abbcb3da3d90df63f37294aa0fc55/Imagens%20Lab%205/Captura%20de%20tela%202026-07-12%20192633.png)

## Análise técnica

### Comparação orientada entre RIP e OSPF

###### Qual protocolo foi mais simples de configurar?
O RIP foi visivelmente mais simples de configurar. A sua ativação exigiu apenas o comando global router rip acompanhado de um único comando declarativo de rede classful network 172.16.0.0 em todos os roteadores. O OSPF exigiu a especificação de máscaras curinga (wildcard masks) para cada sub-rede individualizada, IDs de processos diferentes e a associação manual à área de roteamento (area 0).
###### Qual protocolo apresentou maior riqueza de informações operacionais?
O OSPF. Por meio de comandos de diagnóstico como show ip ospf neighbor, show ip ospf database e o próprio mapa gerado pelo algoritmo, o OSPF provê detalhes minuciosos sobre o estado do link, prioridades dos roteadores vizinhos (DR/BDR), temporizadores e estruturas topológicas complexas que o RIP simplesmente não processa em sua tabela básica de roteamento.
###### Qual a principal métrica do RIP?
A métrica fundamental do RIP é o Vetor de Distância baseado em Contagem de Saltos (Hop Count). Cada roteador que o pacote atravessa conta como 1 salto, possuindo um limite máximo de 15 saltos (sendo 16 considerado rede inalcançável). Essa métrica ignora completamente a velocidade ou largura de banda real dos enlaces.
###### Qual algoritmo é usado pelo OSPF?
O OSPF utiliza o algoritmo SPF (Shortest Path First), também amplamente conhecido como Algoritmo de Dijkstra. Esse algoritmo constrói um mapa topológico completo (grafo) da rede e calcula a árvore de caminhos mais curtos com base no custo dos enlaces (atrelados à largura de banda), em vez de contar puramente os nós intermediários.
###### Qual protocolo tende a escalar melhor?
O OSPF escala incomparavelmente melhor do que o RIP. Devido ao limite rígido de 15 saltos do RIP, redes de médio e grande porte tornam-se inviáveis para ele. O OSPF supera essa barreira e suporta o particionamento em múltiplas áreas (Multi-area OSPF), o que reduz o processamento e confina a propagação de atualizações de rotas a regiões específicas.
###### Qual protocolo converge melhor em cenários maiores?
O OSPF converge muito mais rápido. Sendo um protocolo Link-State, ele reage imediatamente a alterações na topologia disparando atualizações instantâneas via Multicast (LSU - Link State Updates). O RIP, por outro lado, sofre com uma convergência lenta decorrente do envio periódico e estático de tabelas inteiras a cada 30 segundos, necessitando de temporizadores altos para declarar a invalidez de uma rota.
### Explicação da Situação 1
Ao aplicar o comando no network para remover o anúncio de uma das redes locais do roteador R - SP, os roteadores vizinhos (R - RJ e R - BH) receberam pacotes LSA informando sobre a indisponibilidade dessa rede. Como consequência, o algoritmo de Dijkstra recalculou instantaneamente a árvore de rotas nos dispositivos adjacentes, removendo a respectiva sub-rede de suas tabelas de roteamento, impedindo que as VPCs das outras filiais alcançassem os hosts que residiam naquela sub-rede específica, embora o restante da vizinhança OSPF tenha permanecido estável
### Explicação da Situação 2
Ao derrubar a interface física ou lógica que interliga R - SP e R - BH, a adjacência direta entre eles foi desfeita.
*No cenário com OSPF: Graças ao mapa topológico mantido pelo protocolo, o tráfego destinado a BH foi redirecionado de forma automatizada e dinâmica através do roteador centralizador R - RJ (fazendo o desvio alternativo SP -> RJ -> BH), restabelecendo a comunicação em pouquíssimos segundos após o recálculo do SPF.*
*No cenário com RIP: A perda do enlace também forçaria um desvio, porém a detecção e a convergência demoram substancialmente mais tempo (podendo levar minutos), devido à dependência do vencimento dos timers de holddown e invalid para que o RIP finalmente remova a rota morta e aprenda o caminho alternativo por meio dos anúncios periódicos de R - RJ.*
### Explicação da Situação 3
Ao executar no router rip em um dos três roteadores, aquele dispositivo específico cessou o envio e o processamento de atualizações do protocolo RIP. O efeito em cadeia foi a quebra total da conectividade em partes da topologia: o roteador afetado esqueceu as redes remotas de seus vizinhos e, simultaneamente, os vizinhos pararam de receber as atualizações sobre as redes locais daquele roteador, fazendo com que as tabelas de roteamento ficassem severamente incompletas devido à característica isolada do protocolo em depender que todos os nós falem a mesma língua para formar os vetores de distância.
## Conclusão

A realização do Experimento 5 proporcionou uma oportunidade prática valiosa para analisar, configurar e comparar o comportamento de dois dos principais protocolos de roteamento dinâmico do mercado: o RIP (Vetor de Distância) e o OSPF (Estado de Link), utilizando roteadores Cisco IOL. 

As atividades práticas permitiram constatar que, embora o RIP ofereça uma simplicidade de configuração notável para topologias pequenas e estáticas, ele carece de flexibilidade, escalabilidade e visibilidade técnica[cite: 2]. Por outro lado, o OSPF mostrou-se muito mais robusto, disponibilizando ferramentas de monitoramento complexas e operando com base na largura de banda dos enlaces, o que otimiza de forma inteligente o envio de dados através do algoritmo de Dijkstra.

As simulações de falhas e alterações estruturais (Situações 1, 2 e 3) comprovaram de maneira inequívoca a velocidade superior de convergência do OSPF em comparação ao RIP diante de quedas de enlaces ou mudanças nos anúncios de rede. Conclui-se que o domínio e a escolha correta do protocolo de roteamento dinâmico são pilares fundamentais para engenheiros de redes, assegurando que infraestruturas de comunicação corporativas de qualquer porte permaneçam tolerantes a falhas, escaláveis e altamente eficientes.
