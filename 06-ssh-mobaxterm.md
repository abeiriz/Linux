# 🔑 SSH com MobaXterm — Do Básico ao Acesso por Chave

## 1. Instalar o SSH no servidor

```bash
sudo apt install openssh-server -y
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh      # deve mostrar: active (running)
```

Liberar no firewall (se UFW ativo):

```bash
sudo ufw allow 22
sudo ufw status
```

---

## 2. Conectar pelo MobaXterm (senha)

1. Abra o **MobaXterm**
2. Clique em **Session** → **SSH**
3. Preencha:
   - **Remote host:** `192.168.1.100` (IP da VM)
   - **Username:** `seu-usuario`
   - **Port:** `22`
4. Clique **OK** → digite a senha quando solicitado

---

## 3. Gerar Chave SSH no MobaXterm

1. No MobaXterm, vá em **Tools** → **MobaKeyGen**
2. Selecione o tipo: **EdDSA** (recomendado) ou **RSA 4096**
3. Clique em **Generate** e mova o mouse para gerar entropia
4. Preencha o campo **Key comment** com algo como `moba-abeiriz`
5. (Opcional) Defina uma **passphrase** para mais segurança
6. Clique **Save private key** → salve o `.ppk` em local seguro
7. **Copie o texto** da caixa "Public key for pasting..." — você vai precisar dele

---

## 4. Colocar a Chave Pública no Servidor

Conecte no servidor (ainda com senha) e execute:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
```

Cole a chave pública copiada do MobaXterm (passo anterior), salve e saia.

Ajuste as permissões:

```bash
chmod 600 ~/.ssh/authorized_keys
```

---

## 5. Configurar Sessão no MobaXterm com a Chave

1. Clique em **Session** → **SSH**
2. Preencha host, usuário e porta
3. Clique em **Advanced SSH settings**
4. Marque **Use private key** e selecione o arquivo `.ppk` salvo
5. Clique **OK**

Na próxima conexão, o MobaXterm usará a chave automaticamente, sem pedir senha.

---

## 6. Comandos SSH Básicos no Servidor

```bash
# Ver usuários conectados no momento
w

# Ver histórico de logins
last

# Ver tentativas de login (sucesso e falha)
sudo journalctl -u ssh | tail -30

# Ou via arquivo de log
sudo tail -f /var/log/auth.log

# Ver conexões SSH ativas
ss -tnp | grep :22

# Desconectar todos de um usuário
sudo pkill -u nome-usuario

# Encerrar sua sessão
exit
```

---

## 7. Verificar Logs SSH

```bash
# Status geral do serviço
sudo systemctl status ssh

# Sessões abertas agora
w

# Logins recentes
last | head -20

# Tentativas falhas (ataques de força bruta)
sudo grep "Failed password" /var/log/auth.log | tail -20

# Logins bem-sucedidos
sudo grep "Accepted" /var/log/auth.log | tail -20

# Logs em tempo real
sudo tail -f /var/log/auth.log
```
