# 🌍 Apache - Servidor Web

## Instalação

```bash
apt update
apt install apache2 -y

systemctl start apache2         # inicia
systemctl enable apache2        # ativa no boot
systemctl status apache2        # verifica status
```

Teste: abra `http://SEU-IP` no navegador — deve aparecer a página padrão do Apache.

## Comandos Essenciais

```bash
systemctl start apache2         # inicia o Apache
systemctl stop apache2          # para o Apache
systemctl restart apache2       # reinicia (derruba e sobe)
systemctl reload apache2        # recarrega config sem derrubar
systemctl status apache2        # status do serviço

apache2ctl configtest           # testa se a config está correta (SEMPRE antes de reload)
apache2ctl -v                   # versão do Apache
apache2ctl -M                   # lista módulos carregados
```

## Estrutura de Diretórios

```
/etc/apache2/
├── apache2.conf            # config principal
├── ports.conf              # portas que o Apache escuta
├── sites-available/        # todos os VirtualHosts configurados
├── sites-enabled/          # VirtualHosts ativos (links simbólicos)
├── conf-available/         # configurações extras disponíveis
├── conf-enabled/           # configurações extras ativas
└── mods-available/         # módulos disponíveis
    mods-enabled/           # módulos ativos

/var/www/html/              # diretório padrão dos sites
/var/log/apache2/           # logs
├── access.log              # acessos
└── error.log               # erros
```

## VirtualHost - HTTP (porta 80)

Criar arquivo `/etc/apache2/sites-available/meusite.conf`:

```apache
<VirtualHost *:80>
    ServerName meusite.com
    ServerAlias www.meusite.com
    DocumentRoot /var/www/meusite

    <Directory /var/www/meusite>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/meusite_error.log
    CustomLog ${APACHE_LOG_DIR}/meusite_access.log combined
</VirtualHost>
```

```bash
# Criar a pasta do site
mkdir -p /var/www/meusite
echo "<h1>Funcionando!</h1>" > /var/www/meusite/index.html
chown -R www-data:www-data /var/www/meusite

# Ativar o site
a2ensite meusite.conf
apache2ctl configtest
systemctl reload apache2
```

## VirtualHost - HTTPS com SSL manual

```apache
<VirtualHost *:443>
    ServerName meusite.com
    DocumentRoot /var/www/meusite

    SSLEngine on
    SSLCertificateFile      /etc/ssl/certs/meusite.crt
    SSLCertificateKeyFile   /etc/ssl/private/meusite.key

    <Directory /var/www/meusite>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/meusite_error.log
    CustomLog ${APACHE_LOG_DIR}/meusite_access.log combined
</VirtualHost>

# Redirecionar HTTP → HTTPS
<VirtualHost *:80>
    ServerName meusite.com
    Redirect permanent / https://meusite.com/
</VirtualHost>
```

## SSL com Let's Encrypt (Certbot) — Gratuito

```bash
# Instalar Certbot
apt install certbot python3-certbot-apache -y

# Gerar certificado (substitui automaticamente no VirtualHost)
certbot --apache -d meusite.com -d www.meusite.com

# Só gerar o certificado (sem modificar config)
certbot certonly --apache -d meusite.com

# Renovar certificados manualmente
certbot renew

# Testar renovação (dry run)
certbot renew --dry-run

# Os certificados ficam em:
# /etc/letsencrypt/live/meusite.com/fullchain.pem
# /etc/letsencrypt/live/meusite.com/privkey.pem
```

## Gerenciar Sites e Módulos

```bash
# Sites
a2ensite meusite.conf       # ativa site
a2dissite meusite.conf      # desativa site
a2dissite 000-default.conf  # desativa site padrão

# Módulos úteis
a2enmod rewrite             # mod_rewrite (necessário para .htaccess)
a2enmod ssl                 # módulo SSL/HTTPS
a2enmod headers             # cabeçalhos HTTP personalizados
a2enmod deflate             # compressão gzip
a2enmod expires             # controle de cache
a2dismod nome               # desativa módulo

# Sempre recarregar após mudanças
apache2ctl configtest && systemctl reload apache2
```

## .htaccess

Arquivo criado dentro do `DocumentRoot` do site. Requer `AllowOverride All` no VirtualHost.

```apache
# Redirecionar www → sem www
RewriteEngine On
RewriteCond %{HTTP_HOST} ^www\.(.+)$ [NC]
RewriteRule ^ https://%1%{REQUEST_URI} [R=301,L]

# Forçar HTTPS
RewriteCond %{HTTPS} off
RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [R=301,L]

# Bloquear acesso a arquivos .env e .git
<FilesMatch "\.(env|git|htaccess)$">
    Require all denied
</FilesMatch>

# Página de erro personalizada
ErrorDocument 404 /404.html
ErrorDocument 500 /500.html

# Desabilitar listagem de diretório
Options -Indexes
```

## Logs

```bash
# Acompanhar logs em tempo real
tail -f /var/log/apache2/access.log
tail -f /var/log/apache2/error.log

# Filtrar erros críticos
grep "error" /var/log/apache2/error.log
grep "crit\|alert\|emerg" /var/log/apache2/error.log

# Ver acessos de um IP específico
grep "192.168.1.100" /var/log/apache2/access.log

# Logs de um site específico (se configurado no VirtualHost)
tail -f /var/log/apache2/meusite_error.log
```

## Permissões Corretas

```bash
# Dono dos arquivos deve ser www-data (usuário do Apache)
chown -R www-data:www-data /var/www/meusite

# Permissões recomendadas
find /var/www/meusite -type d -exec chmod 755 {} \;   # pastas: 755
find /var/www/meusite -type f -exec chmod 644 {} \;   # arquivos: 644

# Adicionar seu usuário ao grupo www-data (para editar sem sudo)
usermod -aG www-data $USER
```

## Configurações de Segurança (apache2.conf ou VirtualHost)

```apache
# Ocultar versão do Apache nos cabeçalhos
ServerTokens Prod
ServerSignature Off

# Desabilitar TRACE
TraceEnable off

# Cabeçalhos de segurança (requer mod_headers)
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-XSS-Protection "1; mode=block"
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```
