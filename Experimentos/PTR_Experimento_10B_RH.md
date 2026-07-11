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
| Ping iniciado pelo Cliente 1         |                                    |                            |
| Retorno da comunicação               |                                    |                            |
| HTTP Cliente 1 → Cliente 2           |                                    |                            |
| Nova conexão iniciada pelo Cliente 2 |                                    |                            |
| Quantidade de regras                 |                                    |                            |
| Facilidade de administração          |                                    |                            |

## Questões para análise

###### O que diferencia um firewall stateful de um firewall de pacotes simples?

###### Qual a função de `-m conntrack --ctstate ESTABLISHED,RELATED`?

###### Por que o retorno da conexão HTTP não precisou de regra explícita no sentido inverso?

###### O que caracteriza um pacote no estado `NEW`?

###### Qual a principal vantagem de usar regras stateful em relação ao Laboratório 10?

###### Por que o Cliente 2 não conseguiu iniciar novas conexões para o Cliente 1?

###### O que mudou na quantidade e na lógica das regras entre Laboratório 10 e Laboratório 10B?

###### Em que tipo de ambiente um firewall stateful tende a ser mais adequado?

###### O firewall stateful elimina a necessidade de política de bloqueio padrão? Explique.

###### O que a atividade comparativa mostrou de forma mais clara sobre a diferença entre os dois modelos?

## Conclusão
