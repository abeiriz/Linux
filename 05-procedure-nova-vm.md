# 🚀 Procedure - Nova VM Linux (Do Zero)

Checklist completo para configurar uma VM do zero na ordem certa.

---

## ✅ PASSO 1 — Repositórios

```bash
sudo nano /etc/apt/sources.list
```

Verifique se os repositórios estão corretos para sua distro (Debian/Ubuntu).
Salve com `Ctrl+O` → `Enter` → `Ctrl+X`.

---

## ✅ PASSO 2 — Atualizar o Sistema

```bash
sudo apt update && sudo apt upgrade -y
```

> Sempre faça isso antes de qualquer instalação.

---

## ✅ PASSO 3 — IP Estático

```bash
sudo nano /etc/network/interfaces
```

Edite o arquivo com as informações da sua rede:

```
auto eth0
iface eth0 inet static
    address 192.168.1.100       # <- seu IP
    netmask 255.255.255.0       # <- sua máscara
    gateway 192.168.1.1         # <- seu gateway (passerelle)
    dns-nameservers 8.8.8.8 8.8.4.4
```

Salve e reinicie a placa de rede:

```bash
sudo ifdown eth0 && sudo ifup eth0
```

Verifique se o IP foi aplicado:

```bash
ip a
```

---

## ✅ PASSO 4 — Instalar SSH

```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
```

Verificar status e sessões:

```bash
sudo systemctl status ssh     # status do serviço SSH
w                             # sessões ativas no momento
```

---

## ✅ PASSO 5 — Cor no Terminal (.bashrc)

```bash
nano ~/.bashrc
```

Busque a linha (use `Ctrl+W` para procurar):

```
#force_color_prompt=yes
```

Retire o `#` para ficar assim:

```
force_color_prompt=yes
```

Salve e aplique:

```bash
source ~/.bashrc
```

> Feche e reabra o terminal se a cor não aparecer imediatamente.

---

## ✅ Checklist Final

| Etapa | Comando de verificação |
|-------|----------------------|
| IP estático aplicado | `ip a` |
| Internet funcionando | `ping -c 3 8.8.8.8` |
| SSH rodando | `sudo systemctl status ssh` |
| Sessões ativas | `w` |
| Cor no terminal | Abrir novo terminal |
