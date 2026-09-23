# Guia Prático de Hardening em Servidores Linux
## Manual Completo de Segurança, Defesa em Profundidade e Configuração de Produção

> **Conteúdo baseado no Seminário de Segurança em Hosts Linux — Prof. Uirá Ribeiro**  
> *Manual prático para Administradores de Sistemas, Engenheiros DevSecOps e Analistas de Infraestrutura.*

---

## 📋 Sumário
1. [Filosofia do Hardening e Minimização da Superfície de Ataque](#1-filosofia-do-hardening-e-minimização-da-superfície-de-ataque)
2. [Gestão Estrita de Serviços Básicos e Controle de Acesso (SSH, PAM e SMTP)](#2-gestão-estrita-de-serviços-básicos-e-controle-de-acesso-ssh-pam-e-smtp)
3. [Enrijecimento do Sistema Operacional e Proteções Locais](#3-enrijecimento-do-sistema-operacional-e-proteções-locais)
4. [Auditoria de Integridade, Detecção de Invasões e Gestão de Logs](#4-auditoria-de-integridade-detecção-de-invasões-e-gestão-de-logs)
5. [Hardening de Servidores Web (Apache, Nginx e PHP)](#5-hardening-de-servidores-web-apache-nginx-e-php)
6. [Ajustes de Segurança no Kernel (`sysctl`) e Acesso Remoto Seguro com OpenVPN](#6-ajustes-de-segurança-no-kernel-sysctl-e-acesso-remoto-seguro-com-openvpn)
7. [Script Completo de Firewall IPTables Pronto para Produção](#7-script-completo-de-firewall-iptables-pronto-para-produção)
8. [Auditoria de Vulnerabilidades (Nessus) e Checklist Diário](#8-auditoria-de-vulnerabilidades-nessus-e-checklist-diário)

---

## 1. Filosofia do Hardening e Minimização da Superfície de Ataque

### 1.1 A Ausência de "Bala de Prata"
Em segurança de computadores, não existe uma solução única, definitiva ou "100% segura" — a chamada **bala de prata**. Um computador totalmente seguro é aquele que está desligado e trancado dentro de um cofre. 

O *hardening* é um **trabalho contínuo, diário e incansável**. A segurança de um host baseia-se no conceito de **defesa em profundidade**: quanto mais camadas de proteção, trancas, filtros e restrições forem sobrepostas, menor será a probabilidade de um atacante explorar com sucesso uma vulnerabilidade (*exploit*, *buffer overflow*, etc.).

### 1.2 Removendo Software e Compiladores Desnecessários
A regra fundamental para servidores em produção é a **minimização**: quanto menos software instalado, menor é a superfície de ataque e menos pontos de entrada existem.

Em um ambiente comprometido, o atacante frequentemente tenta compilar *rootkits* ou utilitários maliciosos localmente. Portanto, quando o servidor estiver pronto e operacional em produção, deve-se remover todos os pacotes de compilação e linguagens não essenciais para a execução do serviço:

```bash
# Exemplo em distribuições RedHat/CentOS:
yum remove gcc gcc-c++ glibc-devel ruby perl python

# Exemplo em distribuições Debian/Ubuntu:
apt-get purge gcc g++ make perl python
```
*Nota: Caso alguma linguagem como Python ou Perl seja estritamente necessária para a aplicação principal, mantenha apenas o interpretador runtime e remova os cabeçalhos de desenvolvimento (`-dev`/`-devel`) e compiladores C/C++.*

### 1.3 Mapeamento e Diagnóstico de Portas e Processos
Antes de aplicar restrições, é indispensável listar exatamente quais serviços estão escutando nas interfaces de rede.

#### Identificação com `netstat`:
```bash
netstat -lntup
```
* Parâmetros:
  * `-l`: Lista apenas portas em estado de escuta (*listening*).
  * `-n`: Exibe endereços e números de portas em formato numérico (evita resolução DNS lenta).
  * `-t`: Inclui conexões TCP.
  * `-u`: Inclui conexões UDP.
  * `-p`: Exibe o PID (ID do processo) e o nome do programa responsável.

#### Análise Aprofundada com `lsof`:
Enquanto atacantes experientes costumam alterar ou substituir os binários do `netstat` e `ps` para ocultar portas abertas por *rootkits*, o `lsof` (*List Open Files*) fornece uma visão profunda dos arquivos, bibliotecas e conexões abertas por cada processo:

```bash
# Listar todos os processos com conexões de rede ativas:
lsof -i

# Filtrar serviços escutando em portas específicas ou IPs:
lsof -iTCP -sTCP:LISTEN
```
O `lsof` detalha não apenas a porta aberta, mas também os arquivos executáveis associados, bibliotecas compartilhadas carregadas e as conexões atualmente estabelecidas com máquinas remotas.

---

## 2. Gestão Estrita de Serviços Básicos e Controle de Acesso (SSH, PAM e SMTP)

### 2.1 Isolamento do Serviço SMTP (Postfix)
Se o servidor web precisa enviar e-mails locais (como notificações do sistema ou formulários web), o servidor SMTP não deve ficar aberto para a rede externa ou para a rede interna, evitando que seja utilizado como um *open relay* ou vetor de *spam*.

No Postfix, edite o arquivo `/etc/postfix/main.cf` e restrinja a interface de escuta estritamente para o *loopback*:

```ini
# /etc/postfix/main.cf
inet_interfaces = localhost
```
Após editar, reinicie o serviço:
```bash
systemctl restart postfix
```

### 2.2 Configuração Extrema de Segurança do SSH (`sshd_config`)
O acesso SSH é um dos alvos mais visados por ataques de força bruta. Edite o arquivo `/etc/ssh/sshd_config` (ou `/etc/sshd_config`) aplicando as seguintes diretivas:

```ini
# Forçar uso exclusivo do protocolo SSH v2 (mais seguro que v1)
Protocol 2

# Tamanho da chave do servidor em bits
ServerKeyBits 1024

# Tempo máximo para concluir a autenticação (2 minutos = 120s; o padrão de 600s é excessivo)
LoginGraceTime 2m

# Intervalo para regeneração da chave efêmera do servidor (em segundos)
KeyRegenerationInterval 3600

# Proibir login direto do usuário root (força login com usuário comum e subida via su/sudo)
PermitRootLogin no

# Bloquear autenticação em contas que possuam senha em branco
PermitEmptyPasswords no

# Desativar arquivos .rhosts e conexões baseadas em Rhosts sem senha
IgnoreRhosts yes

# Verificar permissões do diretório home do usuário antes de aceitar o login
StrictModes yes

# Limitar o número máximo de tentativas de senha por conexão
MaxAuthTries 3

# Limitar sessões simultâneas
MaxSessions 2

# Alterar a porta padrão (evita varreduras automáticas na porta 22)
Port 9072

# Restringir a interface na qual o SSH escutará (ex: IP da rede interna)
ListenAddress 192.168.1.10
```

### 2.3 Restrição de Logins SSH via PAM (`pam_listfile.so`)
Para impedir que qualquer usuário cadastrado no sistema acesse o *shell* via SSH, pode-se criar uma lista explícita de usuários autorizados utilizando o Pluggable Authentication Modules (PAM).

1. Edite `/etc/pam.d/sshd` e adicione a seguinte linha de checagem:
```ini
auth required pam_listfile.so item=user sense=allow file=/etc/login.allow onerr=fail
```

2. Crie o arquivo `/etc/login.allow` contendo apenas os logins autorizados (um por linha):
```text
uira
adminlocal
```

3. Reinicie o SSH. Qualquer outro usuário que tentar autenticar terá o acesso negado antes da validação da senha.

### 2.4 Política Rígida de Senhas com `pam_cracklib.so`
Para exigir que os usuários criem senhas complexas contendo letras maiúsculas, minúsculas, números e caracteres especiais, edite o arquivo de configuração PAM de senhas (`/etc/pam.d/common-password` ou `/etc/pam.d/system-auth`):

```ini
password required pam_cracklib.so retry=3 minlen=8 minclass=3
```
* Onde:
  * `retry=3`: Permite no máximo 3 tentativas para digitar uma senha válida.
  * `minlen=8`: Exige tamanho mínimo de 8 caracteres.
  * `minclass=3`: Exige a combinação de no mínimo 3 classes de caracteres (maiúsculas, minúsculas, números ou símbolos).

### 2.5 Varredura e Trancamento Automático de Contas sem Senha
Diversos serviços do sistema criam contas padrão (ex: `games`, `lp`, `news`) que podem estar ativas no `/etc/passwd` e com senhas não definidas ou em branco no `/etc/shadow`.

#### Comando para listar contas sem senha:
```bash
awk -F: '($2 == "") { print $1 }' /etc/shadow
```

#### Loop Shell em lote para trancar todas as contas sem senha:
```bash
for user in $(awk -F: '($2 == "" || $2 == "*") { print $1 }' /etc/shadow); do
    passwd -l "$user"
done
```
*Onde `passwd -l` (lock) trava a conta inserindo um caractere de bloqueio no campo de hash do `/etc/shadow`.*

### 2.6 Bloqueio Temporário por Tentativas Incorretas (`pam_tally` / `pam_faillock`)
Para prevenir ataques de força bruta locais e remotos, configure o módulo de contagem de falhas editando `/etc/pam.d/common-auth`:

```ini
auth required pam_tally2.so deny=5 unlock_time=900 magic_root
```
* Onde:
  * `deny=5`: Bloqueia o usuário após 5 tentativas incorretas.
  * `unlock_time=900`: Mantém a conta trancada por 900 segundos (15 minutos).
  * `magic_root`: Evita que o bloqueio se aplique ao usuário `root`.

#### Comandos para gestão de desbloqueio pelo administrador:
```bash
# Verificar falhas de um usuário:
pam_tally2 --user uira

# Destravar manualmente uma conta bloqueada:
pam_tally2 --user uira --reset
```

---

## 3. Enrijecimento do Sistema Operacional e Proteções Locais

### 3.1 Desativação de Serviços de Boot e Níveis de Execução
Servidores de produção devem ser executados no **Runlevel 3** (modo texto/multi-usuário sem interface gráfica). A interface gráfica (Runlevel 5) consome recursos desnecessários e aumenta drasticamente o vetor de falhas.

1. Inspecione o diretório do runlevel ativo (`/etc/init.d/rc3.d/` ou `/etc/rc3.d/`):
```bash
ls -l /etc/init.d/rc3.d/S*
```
2. Utilize o utilitário `chkconfig` (ou `systemctl disable`) para desativar serviços dispensáveis:
```bash
chkconfig --list
chkconfig service_name off
```

3. **Remoção do `xinetd`:** O super-servidor `xinetd` gerencia serviços legados e inseguros (como `telnet`, `rsh`, `finger`, `tftp`). Salvo raras exceções controladas por TCP Wrappers, o pacote `xinetd` deve ser desinstalado.

### 3.2 Gestão de Patches de Segurança
Atualizações de segurança devem ser testadas previamente em ambientes de desenvolvimento ou homologação espelhados antes da aplicação em produção.

* **RedHat / CentOS / Rocky Linux:** `yum check-update` e `yum update --security`
* **Debian / Ubuntu:** `apt-get update && apt-get upgrade`
* **SUSE / openSUSE:** `zypper check-update` e `zypper patch`

### 3.3 Segurança Física e Proteção na Inicialização (Boot)
1. **Proteção de BIOS e Mídias:** Defina senha administrativa na BIOS/UEFI e desative a inicialização por USB, CD/DVD e placa de rede (PXE).
2. **Senha no GRUB:** Configure uma senha no gerenciador de inicialização GRUB para impedir que usuários com acesso físico editem os parâmetros de boot e entrem no modo *single-user* (`init=/bin/sh` ou `single`) sem autenticação.

### 3.4 Varredura de Binários com Bits SUID e SGID
Arquivos executáveis que possuem a permissão SUID (*Set User ID*) executam com os privilégios do proprietário do arquivo. Se o proprietário for o `root`, qualquer falha no binário pode conceder um *shell* privilegiado a um usuário comum.

#### Localizar todos os arquivos SUID no sistema:
```bash
find / -perm -4000 -type f -ls
```

#### Localizar arquivos SGID:
```bash
find / -perm -2000 -type f -ls
```
*Análise Rotineira:* Audite a lista periodicamente e remova o bit SUID de utilitários que não precisam ser executados por usuários comuns (ex: `chmod -u-s /path/to/binary`).

### 3.5 Restrição dos Agendadores de Tarefas (`cron` e `at`)
Ataques de movimentação lateral frequentemente tentam agendar rotinas maliciosas no `cron` ou `at`. Para evitar que usuários comuns utilizem estes agendadores:

1. Elimine os arquivos de negação:
```bash
rm -f /etc/cron.deny /etc/at.deny
```

2. Crie os arquivos de permissão restritos exclusivamente ao `root`:
```bash
echo "root" > /etc/cron.allow
echo "root" > /etc/at.allow
```

3. Ajuste as permissões para leitura exclusiva do root:
```bash
chmod 400 /etc/cron.allow /etc/at.allow
```

### 3.6 Shell Inválido para Contas de Serviço
Contas do sistema que não correspondem a usuários humanos (ex: `www-data`, `apache`, `mysql`, `nobody`) jamais devem ter permissão de acesso ao *shell*. Edite o `/etc/passwd` e altere o *shell* padrão para `/bin/false` ou `/sbin/nologin`:

```text
apache:x:48:48:Apache:/var/www:/bin/false
```

### 3.7 Atributo de Imutabilidade em Arquivos Críticos (`chattr +i`)
Para evitar que mesmo o usuário `root` ou um processo comprometido altere arquivos cruciais de configuração, aplique o atributo de imutabilidade do sistema de arquivos ext3/ext4/xfs:

```bash
# Tornar arquivos imutáveis:
chattr +i /etc/php.ini
chattr +i /etc/my.cnf
chattr +i /etc/httpd/conf/httpd.conf
chattr +i /etc/passwd /etc/shadow

# Para verificar atributos ativos:
lsattr /etc/php.ini

# Dica avançada de hardening:
# Após definir a imutabilidade, pode-se temporariamente renomear o binário '/usr/bin/chattr'
# para um nome secreto conhecendo apenas pelo administrador.
```

---

## 4. Auditoria de Integridade, Detecção de Invasões e Gestão de Logs

### 4.1 Verificação da Integridade do Sistema com Tripwire
O **Tripwire** monitora alterações não autorizadas no sistema gerando um banco de dados criptográfico com *hashes* MD5/SHA de arquivos e diretórios críticos.

#### Compilação e Instalação Básica:
```bash
tar -jxvf tripwire-*.tar.bz2
cd tripwire-*
./configure
make
make install
```

#### Arquivos Principais de Configuração (`/usr/local/tripwire/etc/`):
* `twcfg.txt`: Configurações do programa (caminhos de relatórios, chave de criptografia, envio de e-mails).
* `twpol.txt`: Política de monitoramento especificando quais diretórios (`/bin`, `/sbin`, `/lib`, `/etc`, `/boot`) serão auditados.

#### Inicialização e Execução da Auditoria:
```bash
# 1. Inicializar o banco de dados de referência (baseline):
tripwire --init

# 2. Executar a checagem interativa de integridade:
tripwire --check --interactive
```
*Boas Práticas:* O banco de dados gerado no `--init` deve ser copiado e mantido em uma mídia externa ou servidor de backup somente-leitura.

### 4.2 Alternativa Leve: AIDE (Advanced Intrusion Detection Environment)
O **AIDE** é uma alternativa moderna e simplificada ao Tripwire.

#### Instalação e Uso:
```bash
# Instalação via gerenciador de pacotes:
apt-get install aide   # Debian/Ubuntu
yum install aide       # RedHat/CentOS

# Inicializar o banco de dados inicial:
aide --init

# Mover o banco criado para a posição de referência:
mv /var/lib/aide/aide.db.new.gz /var/lib/aide/aide.db.gz

# Executar a verificação periódica:
aide --check
```

### 4.3 Detecção de Rootkits com `chkrootkit`
O `chkrootkit` executa uma série de testes locais em busca de assinaturas de *rootkits*, assinaturas de *trojans* em binários do sistema e interfaces em modo promíscuo.

```bash
# Baixar e descompactar:
tar -zxvf chkrootkit.tar.gz
cd chkrootkit-*

# Executar a varredura completa:
./chkrootkit
```

### 4.4 Validação de Pacotes via Gerenciador RPM (`rpm -Va`)
Em distribuições baseadas em RPM, é possível comparar todos os arquivos instalados no sistema contra as somas de verificação originais gravadas no banco de dados do gerenciador de pacotes:

```bash
rpm -Va
```
Se um arquivo de sistema (como `/bin/login` ou `/bin/ps`) foi modificado por um ataque, o `rpm -Va` exibirá letras indicando a discrepância (ex: `S` = tamanho alterado, `5` = hash MD5 alterado, `M` = permissões alteradas).

### 4.5 Centralização Remota de Logs com Syslog / RSyslog
Uma das primeiras ações de um invasor é apagar os arquivos em `/var/log/` para eliminar os rastros de auditoria. A solução é exportar os logs em tempo real para um servidor centralizado.

No arquivo `/etc/rsyslog.conf` do servidor a ser protegido, adicione a instrução para encaminhar logs via UDP/TCP:

```ini
# Encaminhar todos os logs para o servidor central (IP 192.168.1.50)
*.* @192.168.1.50:514
```

---

## 5. Hardening de Servidores Web (Apache, Nginx e PHP)

### 5.1 Ocultação de Assinaturas no Apache
Erros HTTP (404, 500, 403) não devem expor a versão exata do servidor web nem do sistema operacional. Edite o `httpd.conf`:

```ini
ServerSignature Off
ServerTokens Prod
```

### 5.2 WAF na Camada Web: ModSecurity 2
O **ModSecurity** atua como um Firewall de Aplicação Web (WAF) integrado ao Apache, inspecionando requisições GET/POST, cabeçalhos e *payloads* antes do processamento.

#### Exemplo de Diretivas Básicas (`/etc/httpd/conf.d/mod_security.conf`):
```ini
<IfModule mod_security2.c>
    SecRuleEngine On
    SecRequestBodyAccess On
    SecResponseBodyAccess Off
    SecResponseBodyMimeType text/plain text/html text/xml
    SecDataDir /tmp/
    SecUploadDir /tmp/
    SecAuditEngine FocusedOff
    SecAuditLog /var/log/httpd/modsec_audit.log
</IfModule>
```

### 5.3 Nginx como Proxy Reverso e Filtro de Segurança
O Nginx destaca-se pela alta eficiência e baixo consumo de memória, sendo ideal para atuar como *Proxy Reverso* na porta 80/443, repassando para o Apache apenas o processamento estritamente dinâmico.

#### Parâmetros de Estabilidade e Proteção contra Buffer Overflow (`nginx.conf`):
```nginx
http {
    # Ignorar cabeçalhos inválidos enviados no request
    ignore_invalid_headers on;

    # Timeouts stritamente definidos para evitar conexões presas (Slowloris)
    client_header_timeout 3m;
    client_body_timeout   3m;
    send_timeout          3m;

    # Limites rigorosos de tamanhos de buffers e payload
    client_header_buffer_size    128k;
    large_client_header_buffers  4 256k;
    client_max_body_size         10m;
}
```

#### Bloqueio de Scrapers, User-Agents Maliciosos e Ferramentas de Download:
```nginx
server {
    listen 80;
    server_name example.com;

    # Bloquear utilitários de download automático e crawlers não autorizados
    if ($http_user_agent ~* (wget|curl|libwww-perl|BBBike|LWP::Simple|clshttp) ) {
        return 403;
    }

    # Restringir Métodos HTTP permitidos (apenas GET, HEAD, POST)
    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return 444;
    }

    # Bloquear Robôs de varredura agressivos
    if ($http_user_agent ~* (msnbot|scrapbot) ) {
        return 403;
    }
}
```

#### Regras Nativas no Nginx contra SQL Injection (SQLi):
```nginx
# Detectar UNION, SELECT, CONCAT ou DROP na Query String
if ($args ~* "(concat|eval\*|union.\*select|select.\*from|drop.\*table)") {
    set $block_sql_injection 1;
}
if ($block_sql_injection = 1) {
    return 403;
}
```

#### Regras Nativas no Nginx contra File Injection (LFI/RFI):
```nginx
# Bloquear inclusão remota de URLs ou acesso a diretórios sensíveis do sistema (/proc/self/environ)
if ($args ~* "(boot\.ini|etc/passwd|self/environ|base64_encode|base64_decode)") {
    set $block_file_injection 1;
}
if ($block_file_injection = 1) {
    return 403;
}
```

### 5.4 Hardening no PHP (`php.ini`)
A linguagem PHP possui diversas funções integradas para execução de comandos do sistema operacional que devem ser desativadas em ambientes de produção.

Edite o arquivo `/etc/php.ini`:

```ini
; Ocultar a presença e versão do PHP nos cabeçalhos HTTP (X-Powered-By)
expose_php = Off

; Desativar funções perigosas de execução de código local
disable_functions = exec,system,shell_exec,passthru,eval,popen,proc_open,dl,show_source

; Desativar inclusão de arquivos remotos via allow_url_fopen/include
allow_url_fopen = Off
allow_url_include = Off

; Restringir a execução de scripts PHP apenas aos diretórios autorizados
open_basedir = "/var/www/html/:/tmp/"
```

### 5.5 Prevenção contra Cross-Site Scripting (XSS) no PHP
Na camada de aplicação PHP, qualquer entrada proveniente de formulários (`$_POST`, `$_GET`, `$_COOKIE`) deve passar por funções de sanitização como `mysql_real_escape_string()` ou tratamento customizado para remoção de tags `<script>` e caracteres especiais de controle antes de ser processada ou armazenada.

---

## 6. Ajustes de Segurança no Kernel (`sysctl`) e Acesso Remoto Seguro com OpenVPN

### 6.1 Parâmetros de Hardening do Kernel (`/etc/sysctl.conf`)
As diretivas do Kernel Linux no arquivo `/etc/sysctl.conf` controlam o comportamento da pilha TCP/IP e protegem a máquina contra ataques de rede na camada de transporte e rede.

Adicione ou edite as seguintes linhas:

```ini
# Desativar roteamento de pacotes (caso o servidor não seja um roteador)
net.ipv4.ip_forward = 0

# Ativar proteção contra ataques TCP SYN Flood (SYN Cookies)
net.ipv4.tcp_syncookies = 1

# Logar pacotes com endereços impossíveis ou suspeitos (Martians)
net.ipv4.conf.all.log_martians = 1
net.ipv4.conf.default.log_martians = 1

# Ignorar respostas ICMP para pacotes de broadcast (Mitiga ataques ICMP Smurf)
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Ignorar mensagens de erro ICMP falsas/maliciosas
net.ipv4.icmp_ignore_bogus_error_responses = 1

# Não aceitar pacotes ICMP Redirect (Evita alteração maliciosa da tabela de roteamento)
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.send_redirects = 0

# Não aceitar pacotes com Source Routing (Roteamento na origem)
net.ipv4.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
```

Para aplicar as novas configurações sem reiniciar o servidor:
```bash
sysctl -p
```

### 6.2 Administração Remota Segura via OpenVPN
Abrir a porta SSH diretamente para a internet expõe o servidor a tentativas ininterruptas de varredura. A prática recomendada para data centers ou conexões remotas de administradores é utilizar uma rede privada virtual (VPN) sobre a interface de túnel (`tun`).

#### Sequência de Configuração da Autoridade Certificadora Local (Easy-RSA):
```bash
# 1. Copiar estrutura do Easy-RSA para o diretório de configuração do OpenVPN:
cp -r /usr/share/openvpn/easy-rsa/2.0 /etc/openvpn/easy-rsa
cd /etc/openvpn/easy-rsa

# 2. Configurar variáveis locais no arquivo 'vars' e inicializar o ambiente:
source ./vars
./clean-all

# 3. Gerar Certificado da CA e Chave do Servidor:
./build-ca
./build-key-server server

# 4. Gerar Chave do Cliente e Parâmetros Diffie-Hellman (DH):
./build-key client_uira
./build-dh

# 5. Copiar certificados gerados para o diretório SSL do OpenVPN:
cp keys/ca.crt keys/server.crt keys/server.key keys/dh1024.pem /etc/openvpn/ssl/
```

#### Modelo Prático de Configuração do Servidor (`/etc/openvpn/server.conf`):
```ini
port 1194
proto udp
dev tun

ca /etc/openvpn/ssl/ca.crt
cert /etc/openvpn/ssl/server.crt
key /etc/openvpn/ssl/server.key
dh /etc/openvpn/ssl/dh1024.pem

# Rede interna virtual atribuída aos clientes VPN
server 10.8.0.0 255.255.255.0

keepalive 10 120
comp-lzo
persist-key
persist-tun

status /var/log/openvpn/openvpn-status.log
log-append /var/log/openvpn/openvpn.log
verb 3
```

#### Modelo de Configuração no Cliente (`client.ovpn`):
```ini
client
dev tun
proto udp
remote 200.xxx.xxx.xxx 1194
resolv-retry infinite
nobind
persist-key
persist-tun

ca ca.crt
cert client_uira.crt
key client_uira.key

comp-lzo
verb 3
```

---

## 7. Script Completo de Firewall IPTables Pronto para Produção

Abaixo é apresentado um script em *Shell Bash* completo e modularizado. O script adota a postura de segurança **Default DROP** (rejeição implícita), filtra ataques de rastreamento (*port scans*), bloqueia *floods*, rejeita pacotes malformados e integra o download dinâmico da lista negra **Spamhaus DROP**.

```bash
#!/bin/bash
# ==============================================================================
# SCRIPT DE FIREWALL IPTABLES PARA SERVIDOR WEB EM PRODUÇÃO
# Autor: Baseado na arquitetura apresentada pelo Prof. Uirá Ribeiro
# ==============================================================================

IPT="/sbin/iptables"
INTERFACE="eth0"

# Portas TCP autorizadas a receber tráfego externo (ex: 80=HTTP, 9072=SSH Custom)
ALLOW_TCP="80 9072"
ALLOW_UDP=""

echo "[+] Inicializando regras de firewall IPTables..."

# 1. LIMPEZA TOTAL DAS REGRAS E CADEIAS EXISTENTES
$IPT -F
$IPT -X
$IPT -t nat -F
$IPT -t nat -X
$IPT -t mangle -F
$IPT -t mangle -X

# 2. POLÍTICA PADRÃO: BLOQUEIO TOTAL (DROP)
$IPT -P INPUT DROP
$IPT -P FORWARD DROP
$IPT -P OUTPUT ACCEPT

# 3. PERMITIR TRÁFEGO TOTAL NA INTERFACE DE LOOPBACK (LOCALHOST)
$IPT -A INPUT -i lo -j ACCEPT
$IPT -A OUTPUT -o lo -j ACCEPT

# 4. MANTER CONEXÕES JÁ ESTABELECIDAS E RELACIONADAS
$IPT -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# 5. PROTEÇÃO CONTRA PORT SCANS E LIMITAÇÃO DE NOVAS CONEXÕES (NMAP RATE-LIMITING)
# Se um único IP tentar abrir mais de 10 conexões 'NEW' em 30 segundos, será descartado.
$IPT -A INPUT -p tcp --dport 80 -m state --state NEW -m recent --set --name HTTP_SCAN
$IPT -A INPUT -p tcp --dport 80 -m state --state NEW -m recent --update --seconds 30 --hitcount 10 --name HTTP_SCAN -j DROP

# 6. MITIGAÇÃO DE TCP SYN FLOOD
$IPT -N SYN_FLOOD
$IPT -A INPUT -p tcp --syn -j SYN_FLOOD
$IPT -A SYN_FLOOD -m limit --limit 5/s --limit-burst 7 -j RETURN
$IPT -A SYN_FLOOD -m limit --limit 5/s -j LOG --log-prefix "IPTables: SYN-Flood: "
$IPT -A SYN_FLOOD -j DROP

# 7. BLOQUEIO DE PACOTES FRAGMENTADOS
$IPT -A INPUT -f -m limit --limit 5/m -j LOG --log-prefix "IPTables: Fragmento: "
$IPT -A INPUT -f -j DROP

# 8. ELIMINAÇÃO DE PACOTES MALFORMADOS E FLAGS INVÁLIDAS (XMAS, NULL, FIN-ACK, SYN-RST)
# Ataque NULL Scan (Sem nenhuma flag ativa)
$IPT -A INPUT -p tcp --tcp-flags ALL NONE -m limit --limit 5/m -j LOG --log-prefix "IPTables: NULL-Scan: "
$IPT -A INPUT -p tcp --tcp-flags ALL NONE -j DROP

# Ataque Xmas Scan (Flags FIN, URG, PSH ativas)
$IPT -A INPUT -p tcp --tcp-flags ALL FIN,URG,PSH -j DROP
$IPT -A INPUT -p tcp --tcp-flags ALL ALL -j DROP

# Ataque FIN Scan
$IPT -A INPUT -p tcp --tcp-flags ALL FIN,ACK FIN -j DROP
$IPT -A INPUT -p tcp --tcp-flags SYN,RST SYN,RST -j DROP
$IPT -A INPUT -p tcp --tcp-flags SYN,FIN SYN,FIN -j DROP

# 9. BLOQUEIO DE PACOTES COM ESTADO INVÁLIDO
$IPT -A INPUT -m state --state INVALID -j DROP

# 10. PROTEÇÃO CONTRA ATAQUES DE ICMP SMURF E PING DA MORTE
$IPT -A INPUT -p icmp --icmp-type echo-request -m limit --limit 1/s --limit-burst 4 -j ACCEPT
$IPT -A INPUT -p icmp -j DROP

# 11. BLOQUEIO DE ENDEREÇOS BOGON / IPS INVÁLIDOS NA INTERFACE EXTERNA
$IPT -A INPUT -i $INTERFACE -s 127.0.0.0/8 -j DROP
$IPT -A INPUT -i $INTERFACE -s 169.254.0.0/16 -j DROP
$IPT -A INPUT -i $INTERFACE -s 224.0.0.0/4 -j DROP
$IPT -A INPUT -i $INTERFACE -d 224.0.0.0/4 -j DROP
$IPT -A INPUT -i $INTERFACE -s 240.0.0.0/5 -j DROP

# 12. INTEGRAÇÃO DINÂMICA COM A BLACKLIST SPAMHAUS DROP
echo "[+] Baixando lista atualizada de redes maliciosas (Spamhaus DROP)..."
DROP_LIST="/tmp/drop.lasso"
wget -q -O - https://www.spamhaus.org/drop/drop.lasso | grep -E -v "^;" | awk '{ print $1 }' > $DROP_LIST

if [ -s $DROP_LIST ]; then
    for ip_range in $(cat $DROP_LIST); do
        $IPT -A INPUT -i $INTERFACE -s $ip_range -j DROP
    done
    echo "[+] Spamhaus DROP aplicada com sucesso."
fi

# 13. LIBERAÇÃO EXPLÍCITA DAS PORTAS TCP AUTORIZADAS
for port in $ALLOW_TCP; do
    $IPT -A INPUT -p tcp --dport $port -j ACCEPT
done

# 14. LIBERAÇÃO EXPLÍCITA DAS PORTAS UDP AUTORIZADAS
for port in $ALLOW_UDP; do
    $IPT -A INPUT -p udp --dport $port -j ACCEPT
done

# 15. LOG E REJEIÇÃO DO TRÁFEGO RESTANTE (DEFAULT LOG & DROP)
$IPT -A INPUT -m limit --limit 5/m -j LOG --log-prefix "IPTables: Default-Drop: "
$IPT -A INPUT -j DROP

echo "[+] Firewall ativado e operacional!"
```

### 7.1 Diagnóstico e Inspeção de Regras em Tempo Real
Para monitorar o tráfego atingindo cada regra do firewall e verificar o fluxo de pacotes e bytes consumidos, utilize o utilitário `watch` combinado com a exibição detalhada do IPTables:

```bash
watch -n 1 "iptables -L -n -v --line-numbers"
```
*Acompanhe em tempo real os contadores da primeira coluna incrementando quando conexões ativas ou tentativas de bloqueio ocorrerem.*

---

## 8. Auditoria de Vulnerabilidades (Nessus) e Checklist Diário

### 8.1 Varredura Proativa com o Nessus
O **Nessus** (desenvolvido pela Tenable) é uma das ferramentas de análise de vulnerabilidades mais consolidadas na indústria. Diferente de um simples *port scanner*, o Nessus executa *plugins* de testes ativos (baseados na linguagem NASL — *Nessus Attack Scripting Language*) para identificar softwares desatualizados, configurações incorretas e falhas de segurança conhecidas.

* **Arquitetura:**
  * `nessusd`: O serviço daemon de varredura que executa os testes e varreduras de rede.
  * `Interface Web`: Cliente para criação de escopos de varredura (*Internal/External Scans*) e geração de relatórios detalhados com classificação de severidade (Informativo, Baixo, Médio, Alto e Crítico).

### 8.2 Topologia de Rede e DMZ
Todos os servidores acessíveis publicamente (Web, DNS, SMTP) devem ser posicionados em uma **Zona Desmilitarizada (DMZ)**, isolados da rede corporativa interna por um firewall de borda. Esta arquitetura garante que, caso o servidor web na DMZ seja comprometido, a rede interna não fique imediatamente exposta a ataques de movimentação lateral.

---

### 📋 Checklist Executivo de Manutenção e Hardening

| Frequência | Ação de Segurança | Comando / Procedimento |
| :--- | :--- | :--- |
| **Diário** | Análise de logs de autenticação e firewall | `tail -f /var/log/messages` / `/var/log/secure` |
| **Diário** | Monitoramento de tentativas de bloqueio no IPTables | `watch -n 2 "iptables -nvL"` |
| **Semanal** | Aplicação de patches e atualizações do SO | `yum update --security` / `apt-get upgrade` |
| **Semanal** | Auditoria de integridade de arquivos | `aide --check` / `tripwire --check` |
| **Quinzenal** | Varredura de Rootkits e integridade RPM | `./chkrootkit` e `rpm -Va` |
| **Mensal** | Identificação de arquivos com bits SUID/SGID | `find / -perm -4000 -type f` |
| **Mensal** | Varredura proativa de vulnerabilidades | Execução de relatórios via **Nessus** |
| **Trimestral** | Atualização do script de firewall e regras Spamhaus | Reexecução do script `iptables.sh` |

---
*Fim do Guia Prático.*
