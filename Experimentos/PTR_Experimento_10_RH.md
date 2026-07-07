Disciplina: **ENE0025 – Protocolos de Transporte e Roteamento**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 10 - Firewall de Pacotes com `iptables`

## Identificação

- Nome: Rafael Hossoe Dantas Pinto
- Matrícula: 231036130
- Turma: 1

## Objetivo

Implementar um **firewall de pacotes** em uma máquina Linux no PNetLab, posicionada entre **duas máquinas Linux básicas** - preferencialmente **Linux Tinycore-6.4** - aplicando regras com `iptables` para controlar o tráfego entre duas redes distintas com base em endereço IP, protocolo e porta.

## Ambiente experimental

- 2 Linux Clientes - linux-tinycore-6.4

- 1 Linux Firewall - linux-ubuntu-24.04-server

### Topologia Lógica

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/cc9b536c03da6f283d50c4dcd9ca082b7360d86a/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-02%20184107.png)

## Procedimentos

Foi dado os comandos para cada dispositivo a seguir:

### Linux Cliente 1

```
(Com o Linux conectado com a rede Cloud0, foi feito esse comando)
sudo udhcpc -i eth0
tce-load -wi busybox-httpd.tcz

sudo ip addr flush dev eth0
sudo ip addr add 192.168.10.10/24 dev eth0
sudo ip link set eth0 up
sudo ip route add default via 192.168.10.1
```

### Linux Cliente 2

```
(Com o Linux conectado com a rede Cloud0, foi feito esse comando)
sudo udhcpc -i eth0
tce-load -wi python3.tcz

sudo ip addr flush dev eth0
sudo ip addr add 192.168.20.10/24 dev eth0
sudo ip link set eth0 up
sudo ip route add default via 192.168.20.1
```

### Linux Firewall

```
Login: user
Password: Test123

sudo ip addr flush dev ens3
sudo ip addr flush dev ens4
sudo ip addr add 192.168.10.1/24 dev ens3
sudo ip addr add 192.168.20.1/24 dev ens4
sudo ip link set ens3 up
sudo ip link set ens4 up

sudo sysctl -w net.ipv4.ip_forward=1

sudo nano /etc/sysctl.conf
(Foi descomentado o net.ipv4.ip_forward=1)
Ctrl+O
Ctrl+X

- Bloqueio de ping com sysctl
sudo sysctl -w net.ipv4.icmp_echo_ignore_all=1
tcpdump -i any -n icmp
(Foi observado o comportamento dos pings para o firewall)
sudo sysctl -w net.ipv4.icmp_echo_ignore_all=0
tcpdump -i any -n icmp

sudo iptables -F
sudo iptables -X
sudo iptables -Z
sudo iptables -P FORWARD DROP
sudo iptables -A FORWARD -s 192.168.10.10 -d 192.168.20.10 -p icmp -j ACCEPT
sudo iptables -A FORWARD -s 192.168.20.10 -d 192.168.10.10 -p icmp -j ACCEPT
sudo iptables -A FORWARD -s 192.168.10.10 -d 192.168.20.10 -p tcp --dport 80 -j ACCEPT
sudo iptables -A FORWARD -s 192.168.20.10 -d 192.168.10.10 -p tcp --sport 80 -j ACCEPT
```

## Resultados e evidências

### "ip addr show" dos dispositivos

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/cc9b536c03da6f283d50c4dcd9ca082b7360d86a/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20161728.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/cc9b536c03da6f283d50c4dcd9ca082b7360d86a/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20161743.png)

##### Linux Firewall

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/cc9b536c03da6f283d50c4dcd9ca082b7360d86a/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20161759.png)

### Depois de fazer o comando "sudo sysctl -w net.ipv4.icmp_echo_ignore_all=1" adicionado no Linux Firewall

##### Linux Firewall

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/cc9b536c03da6f283d50c4dcd9ca082b7360d86a/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20162620.png)

### Teste de conectividade entre os Linux Clientes

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/cc9b536c03da6f283d50c4dcd9ca082b7360d86a/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20162657.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20163015.png)

### Linux Cliente 2 subindo um serviço simples na porta 80 e o teste de acesso pelo Linux Cliente 1

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20163135.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20163148.png)

### Teste de Telnet

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20163711.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20164115.png)

### Lista de Regras do Linux Firewall

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20164152.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20164211.png)

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/465b7eb5b128a296a706223772fb288aff556fdb/Imagens%20Lab%2010/Captura%20de%20tela%202026-07-07%20164228.png)

## Análise técnica

###### O que caracteriza um **firewall de pacotes**?
Um firewall de filtragem de pacotes (stateless packet filter) opera na camada de rede (Camada 3) e de transporte (Camada 4) do modelo OSI. Ele analisa individualmente cada pacote que trafega pela interface de rede, tomando decisões de bloqueio ou permissão (ACCEPT ou DROP) com base estritamente em critérios fixos presentes no cabeçalho do próprio pacote (como IPs, portas e protocolos), sem guardar o histórico ou o estado das conexões anteriores.
###### Quais campos do pacote foram usados nas regras deste laboratório?
Baseando-se nos comandos iptables executados no firewall, os seguintes campos dos cabeçalhos IP e de transporte foram inspecionados:

*Endereço IP de Origem (-s): 192.168.10.10 e 192.168.20.10*

*Endereço IP de Destino (-d): 192.168.20.10 e 192.168.10.10*

*Protocolo (-p): icmp e tcp*

*Porta de Destino (--dport): Porta 80 (usada pelo HTTP)*

*Porta de Origem (--sport): Porta 80 (usada no tráfego de retorno do servidor HTTP)*
###### Por que foi necessário ativar o **IP forwarding** no Linux?
Por padrão, por motivos de segurança, as distribuições Linux descartam pacotes cujo endereço IP de destino não seja o do próprio host. Como a máquina intermediária atua como um roteador/firewall entre duas sub-redes distintas (192.168.10.0/24 e 192.168.20.0/24), o comando net.ipv4.ip_forward=1 é obrigatório para instruir o kernel do Linux a encaminhar os pacotes de uma interface física (ens3) para a outra (ens4).
###### Qual é a função da cadeia `FORWARD` no `iptables`?
A tabela filter do iptables possui três cadeias principais (INPUT, OUTPUT e FORWARD). A cadeia FORWARD é responsável especificamente por processar todos os pacotes que atravessam a máquina, ou seja, pacotes que vêm de uma rede externa com destino a outra rede externa e apenas passam pelo firewall, sem serem direcionados para processos locais do próprio sistema operacional do firewall.
###### Por que o tráfego não permitido foi bloqueado mesmo sem regras específicas para todos os protocolos?
Isso ocorreu devido à configuração da política padrão (Default Policy) da cadeia como DROP através do comando "sudo iptables -P FORWARD DROP". Em firewalls, essa abordagem segue o princípio do Privilégio Mínimo (Lista Branca). Tudo o que não for explicitamente permitido por uma regra subsequente é sumariamente descartado pela política padrão da cadeia. Portanto, protocolos como o Telnet (porta 23) foram bloqueados de forma automática.
###### Qual a diferença entre permitir **HTTP** e permitir **ICMP**?
A diferença reside nas camadas em que operam e no comportamento do protocolo:

*ICMP (Internet Control Message Protocol): Opera na Camada 3 (Rede) e não utiliza o conceito de "portas". Ele é mapeado diretamente no cabeçalho IP e é usado para mensagens de controle e diagnóstico (como o Echo Request e Echo Reply do ping). Suas regras filtram apenas o tipo de mensagem.*

*HTTP (Hypertext Transfer Protocol): É um protocolo de aplicação (Camada 7) transportado por segmentos TCP na Camada 4. Ele requer o estabelecimento de uma conexão em três vias (3-way handshake) e o controle de portas específicas (porta de destino 80 na requisição e porta de origem 80 na resposta do servidor).*
###### O que muda quando a política padrão da cadeia `FORWARD` é `DROP`?
Muda o paradigma de segurança do firewall de Permissivo (onde tudo passa, exceto o que você explicitamente proibir) para Restritivo (onde nada passa, exceto o que você explicitamente autorizar). Isso garante maior segurança, pois mitiga o risco de deixar passar tráfego desconhecido ou malicioso por esquecimento do administrador.
###### Por que esse laboratório ainda não é considerado um firewall stateful?
Porque o tráfego de retorno do HTTP precisou ser liberado manualmente através de uma regra estática explícita "sudo iptables -A FORWARD -s 192.168.20.10 -d 192.168.10.10 -p tcp --sport 80 -j ACCEPT". Um firewall stateful (com controle de estado) mantém uma tabela interna de conexões ativas (conntrack). Ele seria capaz de reconhecer que o tráfego vindo da porta 80 do Cliente 2 é uma resposta legítima a uma conexão iniciada pelo Cliente 1, permitindo o retorno automaticamente usando apenas a diretiva -m state --state ESTABLISHED,RELATED. No modelo utilizado, o firewall avalia cada pacote de forma isolada, sem saber se ele faz parte de uma sessão já aberta.
###### Qual a importância da ordem das regras no `iptables`?
O iptables analisa as regras sequencialmente, de cima para baixo (top-down). No momento em que um pacote faz o casamento (match) perfeito com os critérios de uma regra, a ação correspondente (ACCEPT ou DROP) é executada imediatamente e a varredura da lista é interrompida para aquele pacote. Se regras muito genéricas forem colocadas antes de regras específicas, elas podem anular as restrições desejadas mais abaixo.
###### Quais vantagens práticas surgem ao usar hosts Linux básicos no lugar de VPCs neste laboratório?
O uso do Linux Tinycore emulados no PNetLab oferece vantagens significativas frente a simples VPCs (Virtual PC Simulator):

*Suporte Real a Serviços: Permite instalar e rodar daemons reais de rede (como o servidor web HTTP busybox-httpd e o interpretador de python3), simulando fielmente a interação real entre cliente e servidor.*

*Ferramental de Diagnóstico Completo: Diferente de uma VPC que possui apenas comandos básicos de ping e trace, uma distribuição Linux real oferece suporte a tcpdump, netstat, ss, manipulação fina da tabela de roteamento e da pilha do kernel.*
###### Qual a diferença entre bloquear o ping com `sysctl` e bloquear ICMP com `iptables`?
*Via sysctl (net.ipv4.icmp_echo_ignore_all=1): Essa alteração é feita diretamente nos parâmetros do Kernel do Linux. O pacote ICMP chega a ser processado pela pilha de rede, mas a função interna do sistema operacional que geraria a resposta de Echo Reply é instruída a ignorar e descartar a requisição silenciosamente.*

*Via iptables (-p icmp -j DROP/REJECT): O bloqueio ocorre na camada do subsistema de firewall (Netfilter). O pacote é interceptado e descartado em ganchos (hooks) de rede específicos antes mesmo de o Kernel avaliar o propósito do pacote, permitindo logs detalhados, controle por interface, por IP de origem e aplicação de políticas de rejeição personalizadas.*
## Conclusão
A realização deste experimento permitiu compreender, de forma prática e aprofundada, o funcionamento de um firewall de filtragem de pacotes (*stateless*) utilizando a ferramenta `iptables` em ambiente Linux. A arquitetura montada no PNetLab, interconectando duas sub-redes distintas por meio de um gateway intermediário com o encaminhamento de IP (`ip_forward`) ativado, simulou com fidelidade o cenário real de segmentação e proteção de perímetros de rede.

Os testes de conectividade evidenciaram a eficácia e o rigor de uma política padrão baseada em `DROP` na cadeia `FORWARD`. Sob essa premissa de segurança restritiva (Lista Branca), todo o tráfego não explicitamente autorizado foi mitigado com sucesso, o que pôde ser comprovado pelo bloqueio automático do serviço de Telnet. Em contrapartida, os serviços essenciais moldados nas regras estáticas — como o tráfego de diagnóstico ICMP (ping) e as requisições/respostas HTTP na porta 80 — operaram perfeitamente dentro dos parâmetros estabelecidos.

Por fim, o experimento também serviu para contrastar as limitações de um firewall puramente *stateless* frente aos modelos *stateful*. A necessidade de criar regras manuais e bidirecionais específicas para permitir o tráfego de retorno do servidor HTTP ilustrou o custo operacional e a complexidade de se manter filtros que avaliam pacotes de maneira isolada. Adicionalmente, a manipulação de parâmetros do kernel via `sysctl` e o monitoramento em tempo real com o `tcpdump` consolidaram a importância do domínio de ferramentas nativas do Linux para a auditoria, diagnóstico e endurecimento (*hardening*) da segurança em infraestruturas de redes de comunicação.
