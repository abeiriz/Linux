# 📂 Comandos Básicos Linux

## Navegação

```bash
pwd                         # mostra o diretório atual
ls                          # lista arquivos
ls -la                      # lista com detalhes e arquivos ocultos
cd /caminho/pasta           # entra na pasta
cd ..                       # volta um nível
cd ~                        # vai para o home do usuário
```

## Arquivos e Diretórios

```bash
mkdir nome-pasta            # cria pasta
mkdir -p a/b/c              # cria pastas aninhadas
touch arquivo.txt           # cria arquivo vazio
cp origem destino           # copia arquivo
cp -r origem/ destino/      # copia diretório inteiro
mv origem destino           # move ou renomeia
rm arquivo.txt              # remove arquivo
rm -rf pasta/               # remove pasta e tudo dentro (CUIDADO!)
cat arquivo.txt             # exibe conteúdo do arquivo
less arquivo.txt            # exibe com scroll (q para sair)
nano arquivo.txt            # edita arquivo no terminal
```

## Busca

```bash
find /caminho -name "*.log"          # busca arquivos por nome
grep "texto" arquivo.txt             # busca texto dentro de arquivo
grep -r "texto" /caminho/            # busca recursiva em diretório
grep -i "texto" arquivo.txt          # busca sem diferenciar maiúsculas
```

## Permissões

```bash
ls -l arquivo.txt           # ver permissões do arquivo
chmod 755 arquivo.sh        # rwxr-xr-x (dono: tudo | outros: ler+executar)
chmod 644 arquivo.txt       # rw-r--r-- (dono: ler+escrever | outros: ler)
chmod +x script.sh          # adiciona permissão de execução
chown usuario:grupo arq     # muda dono e grupo do arquivo
chown -R usuario:grupo dir/ # aplica recursivamente em pasta
sudo comando                # executa como root
su - usuario                # troca para outro usuário
```

### Tabela chmod numérico

| Número | Permissão |
|--------|-----------|
| 7 | rwx (ler + escrever + executar) |
| 6 | rw- (ler + escrever) |
| 5 | r-x (ler + executar) |
| 4 | r-- (somente ler) |
| 0 | --- (sem permissão) |

## Processos

```bash
ps aux                      # lista todos os processos
top                         # monitor em tempo real
htop                        # monitor avançado (instalar: apt install htop)
kill PID                    # encerra processo pelo ID
kill -9 PID                 # força encerramento
pkill nome-processo         # encerra pelo nome
jobs                        # lista processos em background
nohup comando &             # roda comando mesmo após fechar terminal
```

## Compactação

```bash
tar -czf arquivo.tar.gz pasta/      # compacta pasta em .tar.gz
tar -xzf arquivo.tar.gz             # descompacta .tar.gz
tar -czf backup.tar.gz pasta/ --exclude="*.log"  # excluindo arquivos
zip -r arquivo.zip pasta/           # compacta em .zip
unzip arquivo.zip                   # descompacta .zip
```

## Disco e Memória

```bash
df -h                       # espaço em disco por partição
du -sh pasta/               # tamanho de uma pasta
du -sh /*                   # tamanho de cada pasta raiz
free -h                     # uso de memória RAM e swap
lsblk                       # lista dispositivos de bloco (HDs, SSDs)
```

## Pacotes (Debian/Ubuntu)

```bash
apt update                          # atualiza lista de pacotes
apt upgrade                         # atualiza pacotes instalados
apt install nome-pacote             # instala pacote
apt remove nome-pacote              # remove pacote
apt purge nome-pacote               # remove pacote + configurações
apt autoremove                      # remove dependências não usadas
apt search nome                     # busca pacote disponível
dpkg -l | grep nome                 # verifica se pacote está instalado
```

## Variáveis e Ambiente

```bash
echo $VARIAVEL              # exibe valor de variável
export VAR=valor            # define variável de ambiente
env                         # lista todas as variáveis de ambiente
which comando               # mostra caminho do executável
history                     # histórico de comandos
history | grep ssh          # filtra histórico por palavra
!!                          # repete último comando
!ssh                        # repete último comando que começa com "ssh"
```
