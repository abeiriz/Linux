# Site Web com Debian + Windows Server AD
### Apache2 + DNS Active Directory

---

## 1. Configurar IP Fixo no Debian

```bash
sudo nano /etc/network/interfaces
```

```
auto eth0
iface eth0 inet static
    address 192.168.1.123
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 192.168.1.199
```

```bash
sudo systemctl restart networking
```

---

## 2. Instalar o Apache

```bash
sudo apt update
sudo apt install apache2 -y
```

---

## 3. Criar a página do site

```bash
sudo nano /var/www/html/index.html
```

```html
<html>
  <body>
    <h1>Bem-vinda ao engineer.ana!</h1>
  </body>
</html>
```

---

## 4. Configurar o VirtualHost

```bash
sudo nano /etc/apache2/sites-available/engineer.ana.conf
```

```apache
<VirtualHost *:80>
    ServerName engineer.ana
    ServerAlias www.engineer.ana
    DocumentRoot /var/www/html
</VirtualHost>
```

```bash
sudo a2ensite engineer.ana.conf
sudo a2dissite 000-default.conf
sudo systemctl restart apache2
```

---

## 5. DNS no Windows Server

No **Gestionnaire DNS → Zones de recherche directes → ENGINEER.ANA → Nouvel hôte (A)**

| Nom | IP |
|-----|----|
| (vazio) | 192.168.1.123 |
| www | 192.168.1.123 |

---

## 6. Testar

No navegador de qualquer PC da rede:

```
http://engineer.ana
```
