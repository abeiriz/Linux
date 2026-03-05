# Configuração SSH com Chave RSA — Debian + MobaXterm

---

## 1. Instalar e verificar o SSH

```bash
sudo apt install openssh-server -y
systemctl status ssh
```

---

## 2. Alterar a porta padrão

```bash
sudo nano /etc/ssh/sshd_config
```

Encontrar a linha e alterar:
```
# Port 22  →  Port 22320
```

```bash
sudo systemctl restart sshd
```

---

## 3. Gerar as chaves no MobaXterm

```
Tools → MobaKeyGen → RSA → 4096 bits
→ Gerar movendo o mouse
→ Definir frase de acesso (passphrase)
→ Save public key  →  ana.pub
→ Save private key →  ana.ppk
```

---

## 4. Adicionar a chave pública no Debian

```bash
mkdir /home/ana/.ssh
nano /home/ana/.ssh/authorized_keys
```

Colar o conteúdo da chave pública gerada no MobaXterm.

```bash
sudo systemctl restart ssh
```

---

## 5. Conectar pelo MobaXterm

```
Session → SSH
→ Host: 192.168.1.123
→ Port: 22320
→ Advanced SSH settings
→ Use private key → selecionar ana.ppk
→ OK
```
