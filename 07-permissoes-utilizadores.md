# 👥 Permissões, Diretórios e Utilizadores

---

## Utilizadores (Usuários)

### Criar e gerenciar utilizadores

```bash
# Criar utilizador com pasta home e shell
sudo adduser nome                          # interativo, recomendado
sudo useradd -m -s /bin/bash nome         # manual

# Definir ou alterar senha
sudo passwd nome

# Ver informações do utilizador
id nome                                    # UID, GID e grupos
groups nome                               # apenas grupos
cat /etc/passwd | grep nome               # linha completa no sistema

# Remover utilizador
sudo deluser nome                          # remove só o user
sudo deluser --remove-home nome           # remove user + pasta home
```

### Grupos

```bash
# Adicionar utilizador a um grupo
sudo usermod -aG sudo nome                 # dá permissão sudo
sudo usermod -aG www-data nome            # adiciona ao grupo do Apache
sudo usermod -aG docker nome              # adiciona ao grupo docker

# Remover utilizador de um grupo
sudo gpasswd -d nome grupo

# Criar novo grupo
sudo groupadd nome-grupo

# Listar todos os grupos do sistema
cat /etc/group

# Ver a quais grupos o utilizador atual pertence
groups
```

> ⚠️ Após `usermod -aG`, o utilizador precisa **sair e entrar novamente** para o grupo ser reconhecido.

---

## Diretórios — Estrutura Importante

```
/                   # raiz do sistema
├── home/           # pastas dos utilizadores (/home/abeiriz)
├── root/           # home do root
├── var/
│   ├── www/        # sites web (Apache)
│   └── log/        # logs do sistema
├── etc/            # arquivos de configuração
├── srv/            # dados de serviços (FTP, sites)
├── tmp/            # arquivos temporários
└── opt/            # software de terceiros
```

### Criar estrutura de diretórios

```bash
mkdir pasta                              # cria pasta simples
mkdir -p /var/www/meusite/logs          # cria toda a estrutura de uma vez
mkdir -p projeto/{src,config,logs}      # cria subpastas de uma vez
```

---

## Permissões

### Entender as permissões

```
-rwxr-xr-x  1  abeiriz  www-data  1234  Jan 01  arquivo.sh
 ^^^  ^^^  ^^^
  |    |    |
  |    |    └── outros (other)
  |    └─────── grupo (group)
  └──────────── dono (user/owner)

r = ler (read)     = 4
w = escrever (write) = 2
x = executar (execute) = 1
```

### chmod — Alterar permissões

```bash
# Notação numérica
chmod 755 arquivo       # dono: rwx | grupo: r-x | outros: r-x
chmod 644 arquivo       # dono: rw- | grupo: r-- | outros: r--
chmod 600 arquivo       # dono: rw- | grupo: --- | outros: --- (privado)
chmod 777 arquivo       # todos: rwx (EVITAR em produção)

# Notação simbólica
chmod +x script.sh      # adiciona execução para todos
chmod u+x script.sh     # adiciona execução só para o dono
chmod g-w arquivo       # remove escrita do grupo
chmod o-rwx arquivo     # remove tudo dos outros

# Recursivo (aplica em pasta e tudo dentro)
chmod -R 755 /var/www/meusite/
```

### Tabela rápida de permissões comuns

| chmod | Quem pode o quê | Uso típico |
|-------|----------------|------------|
| `600` | Só o dono lê/escreve | Chaves SSH, arquivos privados |
| `644` | Dono escreve, todos leem | Arquivos de site, configs |
| `700` | Só o dono acessa | Pasta `.ssh` |
| `750` | Dono tudo, grupo lê | Scripts privados |
| `755` | Dono tudo, outros leem/executam | Pastas de sites, scripts |
| `775` | Dono e grupo escrevem | Pastas compartilhadas |

### chown — Alterar dono e grupo

```bash
# Mudar dono
sudo chown abeiriz arquivo

# Mudar dono e grupo
sudo chown abeiriz:www-data arquivo

# Só o grupo
sudo chown :www-data arquivo

# Recursivo (toda a pasta)
sudo chown -R abeiriz:www-data /var/www/meusite/

# Ver dono atual
ls -la /var/www/meusite/
```

---

## Casos Práticos

### Site Apache — permissões corretas

```bash
# Dono = seu usuário, grupo = www-data (Apache)
sudo chown -R abeiriz:www-data /var/www/meusite/

# Pastas: 755 | Arquivos: 644
find /var/www/meusite -type d -exec chmod 755 {} \;
find /var/www/meusite -type f -exec chmod 644 {} \;
```

### Pasta compartilhada entre utilizadores

```bash
# Criar grupo compartilhado
sudo groupadd equipa

# Adicionar utilizadores ao grupo
sudo usermod -aG equipa abeiriz
sudo usermod -aG equipa deploy

# Criar pasta com o grupo
sudo mkdir /srv/compartilhado
sudo chown -R root:equipa /srv/compartilhado
sudo chmod -R 775 /srv/compartilhado

# Novos arquivos herdam o grupo automaticamente (setgid)
sudo chmod g+s /srv/compartilhado
```

### Script executável pelo dono apenas

```bash
sudo chown abeiriz:abeiriz /usr/local/bin/meu-script.sh
sudo chmod 700 /usr/local/bin/meu-script.sh
```

### Chave SSH — permissões obrigatórias

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519          # chave privada
chmod 644 ~/.ssh/id_ed25519.pub      # chave pública
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/config
```

> ⚠️ O SSH **recusa conexão** se as permissões da pasta `.ssh` ou das chaves estiverem erradas.

---

## Sudo — Acesso Privilegiado

```bash
# Ver se o utilizador tem sudo
sudo -l

# Editar quem tem sudo (nunca edite /etc/sudoers diretamente)
sudo visudo

# Dar sudo completo a um utilizador (dentro do visudo)
abeiriz ALL=(ALL:ALL) ALL

# Dar sudo sem senha (dentro do visudo)
abeiriz ALL=(ALL) NOPASSWD: ALL

# Executar um único comando como outro utilizador
sudo -u www-data comando
```

---

## Comandos de Diagnóstico

```bash
# Ver quem é o dono de um arquivo/pasta
ls -la /var/www/
stat arquivo.txt                     # informação completa (dono, permissões, datas)

# Buscar arquivos com permissão específica
find /var/www -perm 777              # perigoso: tudo liberado
find /home -name "*.sh" -perm /u+x  # scripts executáveis

# Verificar se utilizador existe
id nome 2>/dev/null && echo "existe" || echo "não existe"
```
