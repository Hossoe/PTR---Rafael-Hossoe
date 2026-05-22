# Protocolos de Transporte e Roteamento -  Experimento 1: Configuração Inicial no PNetLab

Este repositório foi feito com o propósito de compreender o funcionamento do simulador PNetLab, também entendendo os conceitos básicos de como configurar uma rede simples.

---

# 1. Introdução

No ambiente de redes, a capacidade de gerenciar e explorar dispositivos localmente é um fundamento essencial. Este projeto documenta a inicialização de um roteador Cisco através de uma conexão de console em simulador, detalhando os comandos essenciais para auditoria de hardware, interfaces e descoberta de topologia via **CDP (Cisco Discovery Protocol)**.

# 2. Objetivos

* Compreender que redes distintas **não se comunicam automaticamente**

* Identificar o **papel do roteador** como elemento lógico de interconexão

* Diferenciar falha de comunicação por ausência de roteamento de falha física

* Relacionar teoria de roteamento com comportamento real da rede

# 3. Recursos no PNetLab

* Router: Cisco IOL - L3-ADVENTERPRISEK9-M-15.4-2T.bin
* 2 Virtual PCs

## Topologia Lógica

Com os dispositivos colocados no simulador dessa rede, foi conectado dessa maneira com cabos Ethernet:

![](<img width="465" height="419" alt="Captura de tela 2026-05-22 173931" src="https://github.com/user-attachments/assets/ab25001a-ff2e-452a-a85f-812be5c6911f" />
)

---

# 4. Configurações Iniciais

Para configurar a rede pedida pelo professor, foi colocado esses comandos nos dispositivos:

## Configurações do R1

```text
enable
configure terminal
interface e0/0
ip address 192.168.10.1 255.255.255.0
no shutdown
interface e0/1
ip address 192.168.20.1 255.255.255.0
no shutdown
end
write
```

## Configurações do VPC1

```text
ip 192.168.10.10/24 192.168.10.1
```

## Configurações do VPC2

```text
ip 192.168.20.10/24 192.168.20.1
```

## Tabela de Representação da Configuração da Rede

| Dispositivo | Interface Host | Interface Router | IP            | Máscara (/24) |
|:----------- |:--------------:|:----------------:|:------------- |:------------- |
| **Host A**  | Eth0           | Eth0             | 192.168.10.10 | 255.255.255.0 |
| **Router**  | —              | Eth0             | 192.168.10.1  | 255.255.255.0 |
| **Host B**  | Eth0           | Eth1             | 192.168.20.10 | 255.255.255.0 |
| **Router**  | —              | Eth1             | 192.168.20.1  | 255.255.255.0 |

# 5. Teste de Conectividade

![](C:\Users\rafah\AppData\Roaming\marktext\images\2026-05-22-17-50-15-image.png)

![](C:\Users\rafah\AppData\Roaming\marktext\images\2026-05-22-17-50-53-image.png)

De acordo com as imagens, os VPCs 1 e 2 conseguem se comunicar.

## Discussão Orientada

* **O enlace físico funciona?**
  Sim, o enlace físico e o de enlace estão operacionais. O sucesso nos testes de ping entre os VPCs e o gateway mostra que a infraestrutura de cabeamento e as portas lógicas estão no estado up, permitindo o tráfego bilateral de quadros Ethernet.

* **O IP está configurado corretamente?**
  Sim. As subnets definidas (`192.168.10.0/24` e `192.168.20.0/24`) são diferentes e as interfaces dos hosts receberam IPs dentro de suas subnets. Além disso, a configuração do Default Gateway nos VPCs aponta para os IPs das interfaces `e0/0` e `e0/1` do roteador R1.

* **Por que o pacote chega ao Host B?**
  O pacote chega ao Host B porque o Host A, ao perceber que o destino (`192.168.20.10`) pertence a uma rede lógica diferente da sua, encapsula o pacote IP em um quadro Ethernet direcionado ao endereço MAC do seu gateway (`192.168.10.1`). Como o roteador R1 possui as duas redes diretamente conectadas em suas interfaces físicas, ele desempacota o quadro, consulta sua tabela de roteamento e encaminha o pacote IP pela interface `e0/1` até o Host B.

# 6. Observação da tabela de rotas

![](C:\Users\rafah\AppData\Roaming\marktext\images\2026-05-22-17-59-02-image.png)

![](C:\Users\rafah\AppData\Roaming\marktext\images\2026-05-22-17-59-14-image.png)

![](C:\Users\rafah\AppData\Roaming\marktext\images\2026-05-22-18-00-56-image.png)

## Tabela de Indicação das Rotas

| Dispositivo     | Conectadas    | Default Gateway | Decisão de Salto |
|:--------------- |:------------- |:---------------:|:---------------- |
| **Host A**      | 192.168.10.10 | 192.168.10.1    | 192.168.10.1     |
| **Host B**      | 192.168.20.10 | 192.168.20.1    | 192.168.20.1     |
| **Router Eth0** | 192.168.10.1  | -               | -                |
| **Router Eth1** | 192.168.20.1  | -               | -                |

# 7. Amarração com a teoria

## Discussão final orientada

* **O roteador conhece o caminho completo?**
  Não, o roteador não precisa conhecer o caminho fim-a-fim de forma explícita em redes maiores. Neste cenário de next-hop, ele conhece apenas as redes que estão diretamente conectadas às suas próprias interfaces. O roteamento funciona com o hop-by-hop, onde cada dispositivo decide apenas o próximo destino correto para o pacote.

* **Onde ocorreu a “inteligência” da rede?**
  A inteligência inicial ocorre nos hosts ao tomarem a decisão de enviar o pacote para o gateway usando a máscara de sub-rede como referência. Depois, a inteligência é redirecionada no processo de busca e comutação interna do roteador, que analisa o cabeçalho de Camada 3 (IP) e decide, por meio de sua tabela de rotas, para qual interface de saída o tráfego deve ser direcionado.

* **O que aconteceria com mais roteadores?**
  Se isso acontecer, as redes deixariam de ser diretamente conectadas para o R1. Seria necessário implementar mecanismos de **roteamento estático** ou **protocolos de roteamento dinâmico** (como OSPF ou RIP) para que os roteadores possam acessar suas tabelas de rotas e aprender caminhos que não estão fisicamente ligados a eles.

## Relação Teórica Fundamental

* **Encaminhamento (*Forwarding*) × Roteamento (*Routing*):**
  O roteamento é o processo macro responsável por mapear a topologia e construir a tabela de rotas (a tomada de decisão estratégica). O encaminhamento é a ação local e rápida de Camada 3 de mover o pacote de uma interface de entrada para a interface de saída correta dentro do próprio dispositivo (a execução operacional). No experimento, o roteamento foi implícito pelas rotas diretas, e o encaminhamento ocorreu a cada ping bem-sucedido transmitido entre as interfaces `e0/0` e `e0/1`.

* **Plano de Dados × Plano de Controle:**
  O Plano de Controle envolve os processos que determinam como o tráfego será tratado, o que inclui a criação e atualização da tabela de rotas do roteador. O Plano de Dados manipula o fluxo real dos pacotes dos usuários. Quando o VPC1 envia uma requisição ICMP, os circuitos do plano de dados filtram o cabeçalho IP, decrementam o TTL, recalculam o checksum e comutam o pacote em tempo de execução com base no mapa fornecido previamente pelo plano de controle.
