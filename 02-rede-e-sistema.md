# 🌐 Rede e Configuração do Sistema

## Informações de Rede

```bash
ip a                        # lista interfaces de rede e IPs
ip addr show eth0           # info da interface eth0
ip route                    # tabela de roteamento
hostname                    # exibe nome da máquina
hostname -I                 # exibe apenas os IPs da máquina
cat /etc/hostname           # arquivo com o hostname
```

## Testes de Rede

```bash
ping google.com             # testa conectividade
ping -c 4 8.8.8.8           # envia apenas 4 pacotes
traceroute google.com       # rastreia rota até destino
curl -I https://site.com    # verifica cabeçalhos HTTP
wget https://site.com/arq   # baixa arquivo da internet
nslookup dominio.com        # consulta DNS
dig dominio.com             # consulta DNS detalhada
```

## Portas e Conexões

```bash
ss -tulnp                   # portas abertas e processos
netstat -tulnp              # igual ao ss (mais antigo)
lsof -i :80                 # processo usando a porta 80
lsof -i :443                # processo usando a porta 443
```

## Configuração de IP (temporário)

```bash
ip addr add 192.168.1.10/24 dev eth0    # adiciona IP à interface
ip addr del 192.168.1.10/24 dev eth0    # remove IP
ip link set eth0 up                      # ativa interface
ip link set eth0 down                    # desativa interface
```

## Configuração de IP (permanente - Ubuntu/Netplan)

Editar `/etc/netplan/00-installer-config.yaml`:

```yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.10/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

```bash
netplan apply               # aplica as configurações
```

## DNS

```bash
cat /etc/resolv.conf        # servidores DNS configurados
nano /etc/resolv.conf       # editar DNS manualmente

# Adicionar no arquivo:
# nameserver 8.8.8.8
# nameserver 8.8.4.4

# Hosts locais
nano /etc/hosts             # mapeamento IP → hostname local
# Exemplo: 192.168.1.100   servidor-web
```

## Firewall (UFW - Ubuntu)

```bash
ufw status                          # status do firewall
ufw enable                          # ativa o firewall
ufw disable                         # desativa o firewall

ufw allow 22                        # libera porta 22 (SSH)
ufw allow 80                        # libera porta 80 (HTTP)
ufw allow 443                       # libera porta 443 (HTTPS)
ufw allow 3306                      # libera porta 3306 (MySQL)

ufw deny 22                         # bloqueia porta 22
ufw delete allow 80                 # remove regra da porta 80

ufw allow from 192.168.1.0/24       # libera faixa de IPs
ufw allow from 192.168.1.10 to any port 22  # SSH só de IP específico

ufw reload                          # recarrega regras
ufw reset                           # reseta todas as regras
```

## Serviços (systemd)

```bash
systemctl status nginx              # status do serviço
systemctl start nginx               # inicia
systemctl stop nginx                # para
systemctl restart nginx             # reinicia
systemctl reload nginx              # recarrega config sem derrubar
systemctl enable nginx              # ativa na inicialização
systemctl disable nginx             # desativa na inicialização

systemctl list-units --type=service # lista todos os serviços
journalctl -u nginx                 # logs do serviço
journalctl -u nginx -f              # logs em tempo real
journalctl -u nginx --since "1 hour ago"
```

## Informações do Sistema

```bash
uname -a                    # info do kernel e sistema
lsb_release -a              # versão da distribuição
cat /etc/os-release         # info detalhada do SO
uptime                      # tempo ligado e carga do sistema
who                         # usuários logados
last                        # histórico de logins
date                        # data e hora atual
timedatectl                 # fuso horário e hora do sistema
timedatectl set-timezone America/Sao_Paulo  # define fuso horário
```

## Usuários e Grupos

```bash
adduser nome                # cria novo usuário (interativo)
useradd -m -s /bin/bash nome  # cria usuário (manual)
passwd nome                 # define/altera senha
usermod -aG sudo nome       # adiciona usuário ao grupo sudo
usermod -aG www-data nome   # adiciona ao grupo do Apache
deluser nome                # remove usuário
id nome                     # info do usuário (UID, grupos)
groups nome                 # grupos do usuário
cat /etc/passwd             # lista todos os usuários
cat /etc/group              # lista todos os grupos
```

## Cron (Agendamento de Tarefas)

```bash
crontab -e                  # edita crontab do usuário atual
crontab -l                  # lista tarefas agendadas
crontab -r                  # remove todas as tarefas

# Formato: minuto hora dia mês dia-semana comando
# Exemplos:
# 0 2 * * *   /script/backup.sh        # todo dia às 2h
# */5 * * * * /script/monitor.sh       # a cada 5 minutos
# 0 0 * * 0   /script/semanal.sh       # todo domingo à meia-noite
```
