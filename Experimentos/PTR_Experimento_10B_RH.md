Disciplina: **ENE0011 – Laboratório de Redes**  
Curso: **Engenharia de Redes de Comunicação**  
Instituição: **Universidade de Brasília (UnB)**  
Departamento: **Engenharia Elétrica**

Professor Responsável: **Prof. Dr. Laerte Peotta de Melo**

# Relatório do Experimento 10B - Firewall Stateful com `iptables`

## Identificação

- Nome: Rafael Hossoe Dantas Pinto
- Matrícula: 231036130
- Turma: 1

## Objetivo

Implementar um **firewall stateful** em uma máquina Linux no PNetLab , reutilizando a topologia do **Laboratório 10**, com regras baseadas em **estado de conexão** para permitir automaticamente o tráfego de retorno de sessões já autorizadas.

## Ambiente experimental

- 2 Linux Clientes - linux-tinycore-6.4

- 1 Linux Firewall - linux-ubuntu-24.04-server

## Topologia Lógica

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-02%20184107.png)

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

sudo iptables -F
sudo iptables -X<img width="630" height="278" alt="Captura de tela 2026-07-11 171110" src="https://github.com/user-attachments/assets/916affa1-7e43-4f10-8928-ec7a682b2a4d" />

sudo iptables -Z
sudo iptables -P FORWARD DROP
sudo iptables -A FORWARD -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A FORWARD -s 192.168.10.10 -d 192.168.20.10 -p icmp -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -A FORWARD -s 192.168.10.10 -d 192.168.20.10 -p tcp --dport 80 -m conntrack --ctstate NEW -j ACCEPT
```

## Resultados e evidências

### "ip addr show" dos dispositivos

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171110.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171123.png)

##### Linux Firewall

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171221.png)

### Lista de Regras do Linux Firewall

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171303.png)

### Teste de conectividade entre os Linux Clientes

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171328.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171349.png)

### Linux Cliente 2 subindo um serviço simples na porta 80 e o teste de acesso pelo Linux Cliente 1

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171431.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171447.png)

### Teste de Telnet

##### Linux Cliente 1

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171526.png)

##### Linux Cliente 2

![](https://github.com/Hossoe/PTR---Rafael-Hossoe/blob/a9e338698ba7776e5b36409644120dfa0e004266/Imagens%20Lab%2011/Captura%20de%20tela%202026-07-11%20171606.png)

## Análise técnica

| Teste                                | Laboratório 10 - Filtro de pacotes | Laboratório 10B - Stateful |
| ------------------------------------ | ---------------------------------- | -------------------------- |
| Ping iniciado pelo Cliente 1         | Permitido (exigia regra explícita de ida). | **Permitido** (liberado pela regra de estado `NEW`). |
| Retorno da comunicação               | **Bloqueado**, a menos que houvesse uma regra explícita de volta. | **Permitido automaticamente** pela regra `ESTABLISHED,RELATED`. |
| HTTP Cliente 1 → Cliente 2           | Permitido se houvesse regra na ida e na volta. | **Permitido** (Nova conexão TCP `NEW` na ida, retorno automático). |
| Nova conexão iniciada pelo Cliente 2 | Dependia de regras de ida/volta específicas para ele. | **Bloqueado** (Não há regra permitindo o estado `NEW` vindo do Cliente 2). |
| Quantidade de regras                 | **Elevada** (necessita do dobro de regras: ida e volta). | **Reduzida** (apenas uma regra genérica de retorno e as de ida). |
| Facilidade de administração          | **Baixa** (complexo de manter e propenso a falhas de segurança). | **Alta** (regras focadas apenas na intenção de abrir o tráfego). |

## Questões para análise

###### O que diferencia um firewall stateful de um firewall de pacotes simples?
Um firewall de pacotes simples (stateless) analisa cada pacote isoladamente, verificando apenas informações estáticas como IPs e portas de origem/destino. Já o firewall stateful possui uma tabela de estados (módulo conntrack), o que o permite lembrar e acompanhar o contexto das conexões ativas. Ele sabe se um pacote faz parte de uma sessão legítima já estabelecida ou se é um pacote isolado/malicioso.
###### Qual a função de `-m conntrack --ctstate ESTABLISHED,RELATED`?
Essa regra instrui o firewall a inspecionar o estado da conexão utilizando o módulo de rastreamento.

ESTABLISHED: Permite a passagem de pacotes que pertencem a uma conexão bidirecional já iniciada e autorizada.

RELATED: Permite pacotes que estão associados indiretamente a uma conexão existente, como transferências de dados FTP ou mensagens de erro ICMP.
###### Por que o retorno da conexão HTTP não precisou de regra explícita no sentido inverso?
Porque a regra genérica de estado (ESTABLISHED,RELATED) inserida no início da cadeia FORWARD intercepta e aceita automaticamente qualquer tráfego que faça parte de uma conexão iniciada com sucesso. Quando o Cliente 1 iniciou a requisição HTTP (estado NEW), o firewall memorizou a sessão; o tráfego de resposta do Cliente 2 foi reconhecido como ESTABLISHED e liberado.
###### O que caracteriza um pacote no estado `NEW`?
Um pacote é classificado como NEW quando ele tenta iniciar uma nova conexão que ainda não existe na tabela de rastreamento do firewall. No caso do TCP, isso é exemplificado pelo primeiro pacote de handshake (com a flag SYN ativada). No ICMP (ping), trata-se do primeiro pacote Echo Request.
###### Qual a principal vantagem de usar regras stateful em relação ao Laboratório 10?
A principal vantagem é a segurança combinada à simplicidade de gerenciamento. Não é necessário abrir portas dinâmicas de retorno para o tráfego de entrada de forma permanente, o que fecharia brechas onde atacantes poderiam forjar pacotes simulando respostas. O firewall só abre dinamicamente a porta de volta enquanto a sessão legítima durar.
###### Por que o Cliente 2 não conseguiu iniciar novas conexões para o Cliente 1?
Porque a política padrão da cadeia FORWARD foi definida como DROP (sudo iptables -P FORWARD DROP). As únicas regras que permitem o estado inicial NEW foram amarradas especificamente com a origem no Cliente 1 (-s 192.168.10.10). Como não há regra permitindo pacotes NEW vindos do Cliente 2 (192.168.20.10), suas tentativas de conexão foram descartadas pelo firewall.
###### O que mudou na quantidade e na lógica das regras entre Laboratório 10 e Laboratório 10B?
Na lógica: No Lab 10, a lógica era baseada em fluxo estático bidirecional (regras de ida e regras de volta idênticas invertendo os IPs/portas). No Lab 10B, a lógica passou a ser baseada em intencionalidade e direção de início.

Na quantidade: A quantidade de regras diminuiu drasticamente. Independentemente de quantos serviços o Cliente 1 precise acessar no Cliente 2 (HTTP, SSH, DNS, etc.), basta uma única regra ESTABLISHED,RELATED para gerenciar o retorno de todos eles, em vez de criar uma regra de retorno para cada porta.
###### Em que tipo de ambiente um firewall stateful tende a ser mais adequado?
Ele é adequado para praticamente qualquer ambiente moderno de rede, especialmente em bordas corporativas, DMZs e firewalls residenciais. É ideal onde clientes internos precisam acessar a internet ou redes externas com segurança, garantindo que servidores externos respondam às requisições, mas fiquem terminantemente proibidos de iniciar conexões arbitrárias para dentro da rede interna.
###### O firewall stateful elimina a necessidade de política de bloqueio padrão? Explique.
Não. Ele depende fundamentalmente de uma política de bloqueio padrão (como DROP). O firewall stateful apenas facilita a liberação do tráfego legítimo de retorno. Se a política padrão fosse ACCEPT, todas as conexões não mapeadas (incluindo ataques externos no estado NEW) seriam permitidas de qualquer forma, anulando o propósito do firewall.
###### O que a atividade comparativa mostrou de forma mais clara sobre a diferença entre os dois modelos?
A atividade demonstrou de forma prática que o modelo stateful torna o firewall muito mais inteligente e robusto. Enquanto o filtro de pacotes simples exige um esforço administrativo exaustivo e perigoso (abrindo portas de retorno de forma estática), o firewall stateful compreende dinamicamente o comportamento natural do tráfego de rede (pergunta e resposta), permitindo criar políticas restritivas fortes com pouquíssimas linhas de configuração.
## Conclusão
A realização do Experimento 10B permitiu consolidar, de forma prática, o entendimento sobre a arquitetura e o funcionamento de um **firewall stateful** utilizando a ferramenta `iptables` no ambiente Linux. A transição do modelo estático de filtragem de pacotes (*stateless*) do Laboratório 10 para o modelo baseado em estados demonstrou ser um marco fundamental no que tange à otimização e à segurança da infraestrutura de redes.

Ficou evidente que a utilização do módulo `conntrack` reduz drasticamente a complexidade da tabela de regras do firewall, uma vez que o tráfego de retorno legítimo passa a ser gerenciado de forma dinâmica e automatizada pela regra de estado `ESTABLISHED,RELATED`. Isso elimina a necessidade perigosa e ineficiente de abrir portas reversas permanentes. 

Por fim, os testes de conectividade — como o tráfego HTTP e ICMP iniciados estritamente pelo Cliente 1, e o bloqueio bem-sucedido de novas conexões vindas do Cliente 2 — comprovaram a robustez do firewall *stateful*. O modelo garantiu o princípio do privilégio mínimo, provando ser a abordagem ideal para ambientes modernos onde o controle de fluxo deve ser inteligente, escalável e centrado na intencionalidade da conexão.
