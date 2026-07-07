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

![](C:/Users/rafah/AppData/Roaming/marktext/images/2026-07-02-18-41-10-image.png)

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

![Captura de tela 2026-07-07 161728.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20161728.png)

##### Linux Cliente 2

![Captura de tela 2026-07-07 161743.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20161743.png)

##### Linux Firewall

![Captura de tela 2026-07-07 161759.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20161759.png)

### Depois de fazer o comando "sudo sysctl -w net.ipv4.icmp_echo_ignore_all=1" adicionado no Linux Firewall

##### Linux Firewall

![Captura de tela 2026-07-07 162620.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20162620.png)

### Teste de conectividade entre os Linux Clientes

##### Linux Cliente 1

![Captura de tela 2026-07-07 162657.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20162657.png)

##### Linux Cliente 2

![Captura de tela 2026-07-07 163015.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20163015.png)

### Linux Cliente 2 subindo um serviço simples na porta 80 e o teste de acesso pelo Linux Cliente 1

##### Linux Cliente 1

![Captura de tela 2026-07-07 163135.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20163135.png)

##### Linux Cliente 2

![Captura de tela 2026-07-07 163148.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20163148.png)

### Teste de Telnet

##### Linux Cliente 1

![Captura de tela 2026-07-07 163711.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20163711.png)

##### Linux Cliente 2

![Captura de tela 2026-07-07 164115.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20164115.png)

### Lista de Regras do Linux Firewall

![Captura de tela 2026-07-07 164152.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20164152.png)

![Captura de tela 2026-07-07 164211.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20164211.png)

![Captura de tela 2026-07-07 164228.png](C:\Users\rafah\OneDrive\Pictures\Screenshots\Captura%20de%20tela%202026-07-07%20164228.png)

## Análise técnica

1. O que caracteriza um **firewall de pacotes**?

2. Quais campos do pacote foram usados nas regras deste laboratório?

3. Por que foi necessário ativar o **IP forwarding** no Linux?

4. Qual é a função da cadeia `FORWARD` no `iptables`?

5. Por que o tráfego não permitido foi bloqueado mesmo sem regras específicas para todos os protocolos?

6. Qual a diferença entre permitir **HTTP** e permitir **ICMP**?

7. O que muda quando a política padrão da cadeia `FORWARD` é `DROP`?

8. Por que esse laboratório ainda não é considerado um firewall stateful?

9. Qual a importância da ordem das regras no `iptables`?

10. Quais vantagens práticas surgem ao usar hosts Linux básicos no lugar de VPCs neste laboratório?

11. Qual a diferença entre bloquear o ping com `sysctl` e bloquear ICMP com `iptables`?

## Conclusão
