# 🔐 SSH e Chaves de Acesso

## Conectar via SSH

```bash
ssh usuario@ip                          # conexão básica
ssh usuario@192.168.1.100               # com IP
ssh usuario@servidor.com                # com domínio
ssh -p 2222 usuario@ip                  # porta customizada
ssh -i ~/.ssh/minha-chave usuario@ip    # com chave específica
ssh -v usuario@ip                       # modo verbose (debug)
```

## Gerar Par de Chaves SSH

```bash
# Gerar chave ED25519 (recomendado - mais segura e moderna)
ssh-keygen -t ed25519 -C "seu@email.com"

# Gerar chave RSA 4096 bits (compatibilidade máxima)
ssh-keygen -t rsa -b 4096 -C "seu@email.com"

# Com nome personalizado (útil para múltiplos servidores)
ssh-keygen -t ed25519 -C "servidor-producao" -f ~/.ssh/id_servidor_prod
```

As chaves ficam salvas em `~/.ssh/`:
- `id_ed25519` → chave **privada** (NUNCA compartilhe!)
- `id_ed25519.pub` → chave **pública** (pode compartilhar)

## Copiar Chave Pública para o Servidor

```bash
# Método automático (recomendado)
ssh-copy-id usuario@ip

# Com porta customizada
ssh-copy-id -p 2222 usuario@ip

# Com chave específica
ssh-copy-id -i ~/.ssh/id_ed25519.pub usuario@ip

# Manual (caso ssh-copy-id não esteja disponível)
cat ~/.ssh/id_ed25519.pub | ssh usuario@ip "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

## Gerenciar Chaves

```bash
ls -la ~/.ssh/                          # lista todas as chaves
cat ~/.ssh/id_ed25519.pub               # exibe chave pública
chmod 700 ~/.ssh                        # permissão correta da pasta
chmod 600 ~/.ssh/id_ed25519             # permissão da chave privada
chmod 644 ~/.ssh/id_ed25519.pub         # permissão da chave pública
chmod 600 ~/.ssh/authorized_keys        # permissão do authorized_keys

# Adicionar chave ao agente SSH (não pede senha a cada uso)
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
ssh-add -l                              # lista chaves carregadas no agente
```

## Arquivo de Configuração SSH (~/.ssh/config)

Crie `~/.ssh/config` para facilitar conexões:

```
# Servidor de produção
Host prod
    HostName 192.168.1.100
    User ubuntu
    Port 22
    IdentityFile ~/.ssh/id_servidor_prod

# Servidor de desenvolvimento
Host dev
    HostName 10.0.0.5
    User root
    Port 2222
    IdentityFile ~/.ssh/id_ed25519

# GitHub
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
```

Depois é só usar: `ssh prod` ou `ssh dev`

## Configurar SSH no Servidor (/etc/ssh/sshd_config)

```bash
nano /etc/ssh/sshd_config
```

Configurações recomendadas de segurança:

```
Port 22                         # mude para outra porta (ex: 2222)
PermitRootLogin no              # desabilita login como root
PasswordAuthentication no       # desabilita senha (só chave)
PubkeyAuthentication yes        # habilita autenticação por chave
MaxAuthTries 3                  # tentativas máximas de login
AllowUsers ubuntu deploy        # só permite esses usuários
```

```bash
systemctl restart sshd          # aplica as mudanças
systemctl status sshd           # verifica se está rodando
```

## Transferir Arquivos com SCP

```bash
# Enviar arquivo para servidor
scp arquivo.txt usuario@ip:/destino/

# Baixar arquivo do servidor
scp usuario@ip:/caminho/arquivo.txt ./

# Enviar pasta inteira
scp -r pasta/ usuario@ip:/destino/

# Com porta customizada
scp -P 2222 arquivo.txt usuario@ip:/destino/
```

## Transferir Arquivos com RSYNC

```bash
# Sincronizar pasta local → servidor
rsync -avz pasta/ usuario@ip:/destino/

# Sincronizar servidor → local
rsync -avz usuario@ip:/pasta/ ./destino/

# Com delete (remove arquivos que não existem mais na origem)
rsync -avz --delete pasta/ usuario@ip:/destino/

# Excluindo arquivos/pastas
rsync -avz --exclude="*.log" --exclude=".git" pasta/ usuario@ip:/destino/

# Dry run (simula sem executar)
rsync -avz --dry-run pasta/ usuario@ip:/destino/
```

## Túnel SSH (Port Forwarding)

```bash
# Túnel local: acessa porta remota localmente
ssh -L 8080:localhost:80 usuario@ip
# Acesse: http://localhost:8080 → abre porta 80 do servidor

# Túnel reverso: expõe porta local no servidor
ssh -R 9090:localhost:3000 usuario@ip

# Túnel SOCKS (proxy)
ssh -D 1080 usuario@ip
```

## Chave SSH no GitHub

```bash
# 1. Gere a chave
ssh-keygen -t ed25519 -C "seu@email.com"

# 2. Copie a chave pública
cat ~/.ssh/id_ed25519.pub

# 3. Cole em: GitHub → Settings → SSH and GPG keys → New SSH key

# 4. Teste a conexão
ssh -T git@github.com
# Resposta esperada: "Hi username! You've successfully authenticated..."
```

## Logs SSH

```bash
tail -f /var/log/auth.log               # logs em tempo real (Debian/Ubuntu)
grep "Failed password" /var/log/auth.log  # tentativas falhas
grep "Accepted" /var/log/auth.log         # logins bem-sucedidos
journalctl -u sshd -f                   # logs via systemd
```
