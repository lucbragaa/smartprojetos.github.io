---
title: Operação, testes e manutenção da VPN
layout: default
---

[← Início da documentação](/)

# Operação, testes e manutenção da VPN

**SMART PROJETOS - PROCEDIMENTO VPN**
    OPENVPN + VPS + USR G806S

## 20. TESTES BÁSICOS DA VPN

**TESTE 1 - VPN SERVER**

**No Windows:**

    ping 10.8.0.1


**Resultado esperado:**

**Resposta.**


**TESTE 2 - G806S**

    ping 10.8.0.2


**Resultado esperado:**

**Resposta.**


**TESTE 3 - EQUIPAMENTO INDUSTRIAL**

**Exemplo:**

    ping 192.168.1.10


**Resultado esperado:**

**Resposta.**


**IMPORTANTE:**

**Esse teste deve ser feito com o PC FORA da LAN do G806S.**

**Ou seja:**

**NÃO deixar o notebook conectado fisicamente à LAN industrial.**

Usar uma rede externa, por exemplo:

**Wi-Fi de celular**
ou
**outra Internet.**


**Isso garante que o acesso realmente está passando pela VPN.**


## 21. TESTE DE PORTA - SIEMENS S7

**Para controladores Siemens S7:**

**TCP PORT 102**


**Comando:**

    Test-NetConnection 192.168.1.10 -Port 102


**Resultado esperado:**

**TcpTestSucceeded : True**


**Exemplo validado:**

**RemoteAddress:**
**192.168.1.10**

**RemotePort:**
102

**SourceAddress:**
**10.8.0.3**

**TcpTestSucceeded:**
**True**


**Esse teste confirma que a comunicação TCP/102 está passando**
**pela VPN até o CLP.**


## 22. TESTE DE PORTA - MODBUS TCP

**Para equipamentos Modbus TCP:**

**TCP PORT 502**


**Comando:**

    Test-NetConnection 192.168.1.10 -Port 502


**Resultado esperado:**

**TcpTestSucceeded : True**


**IMPORTANTE:**

**O IP deve ser substituído pelo IP real do equipamento.**


## 23. TIA PORTAL

O "Accessible devices" do TIA pode não encontrar o CLP através
**da VPN.**

**MOTIVO:**

O Accessible Devices utiliza mecanismos de descoberta PROFINET/DCP,
**que não funcionam da mesma forma que uma comunicação IP roteada.**

**Por isso:**

    PING funcionando
**não significa necessariamente**
**Accessible Devices funcionando.**


**No nosso teste:**

**PING:**
**192.168.1.10       OK**

**TCP/102:**
OK

**TIA Portal:**
**GO ONLINE            OK**


Portanto, para operação pela VPN, o importante é conseguir
**comunicação com o CLP pelo IP configurado.**


**TESTE VALIDADO:**

**TIA Portal conseguiu acessar ONLINE o CLP**
**192.168.1.10 através da VPN.**


## 24. COMANDOS DE DIAGNÓSTICO

**VER INTERFACES:**

    ip addr


**VER ROTAS:**

    ip route


**TESTAR VPN SERVER:**

    ping 10.8.0.1


**TESTAR G806S:**

    ping 10.8.0.2


**TESTAR EQUIPAMENTO:**

    ping 192.168.1.10


**TESTAR S7:**

    Test-NetConnection 192.168.1.10 -Port 102


**TESTAR MODBUS TCP:**

    Test-NetConnection 192.168.1.10 -Port 502


**VER STATUS OPENVPN:**

    systemctl status openvpn-server@server --no-pager


**VER LOG OPENVPN:**

    journalctl -u openvpn-server@server.service -n 50 --no-pager


**VER IPTABLES:**

    iptables -L -n


**VER NAT:**

    iptables -t nat -L -n


**VER REGRAS SALVAS:**

    cat /etc/iptables/rules.v4


**VER STATUS DOS CLIENTES:**

    cat /var/log/openvpn-status.log


## 25. ARQUIVOS IMPORTANTES NO VPS

    OPENVPN SERVER:

**/etc/openvpn/server/server.conf**


CA:

**/etc/openvpn/easy-rsa/pki/ca.crt**

**/etc/openvpn/easy-rsa/pki/private/ca.key**


**CERTIFICADO DO SERVIDOR:**

**/etc/openvpn/easy-rsa/pki/issued/server.crt**


**CHAVE PRIVADA DO SERVIDOR:**

**/etc/openvpn/easy-rsa/pki/private/server.key**


**TLS-CRYPT:**

**/etc/openvpn/ta.key**


**CLIENTES:**

**/etc/openvpn/easy-rsa/pki/issued/**

**/etc/openvpn/easy-rsa/pki/private/**


**CONFIGURAÇÕES CCD:**

**/etc/openvpn/ccd/**


**CONFIGURAÇÃO DO CLIENTE:**

/etc/openvpn/*.ovpn


**IPTABLES:**

**/etc/iptables/rules.v4**


**IP FORWARD:**

**/etc/sysctl.d/99-openvpn-forward.conf**


## 26. BACKUP - MUITO IMPORTANTE

**SIM.**

**É RECOMENDADO manter uma cópia externa dos arquivos críticos.**

**NÃO depender somente do VPS.**

**Principalmente guardar:**

## 1. CA PRIVATE KEY

**/etc/openvpn/easy-rsa/pki/private/ca.key**


## 2. CA CERTIFICATE

**/etc/openvpn/easy-rsa/pki/ca.crt**


## 3. EASY-RSA PKI COMPLETA

**/etc/openvpn/easy-rsa/pki/**


## 4. TLS KEY

**/etc/openvpn/ta.key**


## 5. SERVER CONFIG

**/etc/openvpn/server/server.conf**


## 6. CCD

**/etc/openvpn/ccd/**


## 7. REGRAS FIREWALL

**/etc/iptables/rules.v4**


## 8. CLIENTES .OVPN

/etc/openvpn/*.ovpn


## 27. ATENÇÃO ÀS CHAVES PRIVADAS

**As chaves PRIVADAS são extremamente importantes.**

**Principalmente:**

**ca.key**
**server.key**
**g806s.key**
**smart-pc.key**
**ta.key**


**A CA PRIVATE KEY:**

**/etc/openvpn/easy-rsa/pki/private/ca.key**

**é a mais crítica.**

**Quem possuir essa chave pode potencialmente emitir novos**
**certificados assinados pela nossa CA.**


**PORTANTO:**

**Não guardar em pasta pública.**

**Não enviar por WhatsApp.**

**Não deixar em computador sem proteção.**

**Não colocar em GitHub.**

**Manter backup externo em local protegido.**


## 28. O QUE ACONTECE SE PERDER UMA CHAVE?

**PERDEU A CHAVE PRIVADA DE UM CLIENTE:**

**Exemplo:**

**smart-pc.key**

**É possível criar um novo cliente/certificado.**


**PERDEU A CHAVE DO SERVIDOR:**

**server.key**

Pode ser necessário gerar uma nova chave/certificado de servidor,
**usando a CA existente.**


**PERDEU A CA PRIVATE KEY:**

**ca.key**

**SITUAÇÃO CRÍTICA.**

**Não será possível simplesmente continuar emitindo novos**
**certificados com a mesma CA.**

**Por isso o backup da CA é prioridade máxima.**


**PERDEU ta.key:**

**Será necessário substituir a chave tls-crypt e atualizar todos**
**os clientes que utilizam essa chave.**


## 29. RECOMENDAÇÃO DE BACKUP

**Manter pelo menos:**

**BACKUP 1:**
VPS

**BACKUP 2:**
**local externo/protegido da Smart Projetos**


**O backup deve ser protegido por criptografia/senha.**

**IMPORTANTE:**

**O backup não deve ser simplesmente uma pasta aberta no OneDrive**
**ou em outro armazenamento acessível por qualquer pessoa.**

**Principalmente:**

**ca.key**
**server.key**
**client keys**
**ta.key**
**.ovpn**


## 30. CHECKLIST PARA ADICIONAR NOVO CLIENTE

**EXEMPLO:**

**Novo técnico:**
**joao-pc**


## 1. Criar chave:

    cd /etc/openvpn/easy-rsa

    ./easyrsa gen-req joao-pc nopass


## 2. Assinar:

    ./easyrsa sign-req client joao-pc


## 3. Confirmar:

yes


## 4. Separar:

**ca.crt**
**joao-pc.crt**
**joao-pc.key**
**ta.key**


## 5. Criar:

**joao-pc.ovpn**


## 6. Transferir o .ovpn para o equipamento.


## 7. Instalar OpenVPN Connect.


## 8. Importar o .ovpn.


## 9. Conectar.


## 10. Testar:

    ping 10.8.0.1


## 11. Testar acesso ao equipamento:

    ping 192.168.1.10


## 12. Testar porta necessária:

S7:
    Test-NetConnection 192.168.1.10 -Port 102

**Modbus TCP:**
    Test-NetConnection 192.168.1.10 -Port 502


## 31. CHECKLIST PARA NOVO G806S

## 1. Criar certificado próprio para o G806S.

**Exemplo:**

**g806s-CLIENTE01**


## 2. Criar certificado:

    ./easyrsa gen-req g806s-CLIENTE01 nopass


## 3. Assinar:

    ./easyrsa sign-req client g806s-CLIENTE01


## 4. Criar o .ovpn.


## 5. Importar o .ovpn no G806S.


## 6. Configurar CLIENT_1 / CLIENT_2 / CLIENT_3.


## 7. Configurar a LAN do equipamento.


**Exemplo:**

**192.168.1.0/24**


## 8. Configurar rota correspondente no G806S.


## 9. Criar arquivo CCD no VPS.

**Exemplo:**

**/etc/openvpn/ccd/g806s-CLIENTE01**


**Com:**

**iroute 192.168.1.0 255.255.255.0**


## 10. Reiniciar OpenVPN:

    systemctl restart openvpn-server@server


## 11. Confirmar conexão.


## 12. Testar:

    ping IP_DO_G806S_VPN


## 13. Testar equipamento atrás do G806S.


## 32. EXEMPLO DE FLUXO DE COMUNICAÇÃO

**TÉCNICO:**

**10.8.0.3**

        |
        | OpenVPN
        v

**VPS:**

**10.8.0.1**

        |
        | VPN
        v

**G806S:**

**10.8.0.2**

        |
        | LAN
        v

**CLP:**

**192.168.1.10**


**Para Siemens:**

PC
  |
  | TCP 102
  v
**192.168.1.10**


**Para Modbus TCP:**

PC
  |
  | TCP 502
  v
**192.168.1.10**


## 33. PORTAS IMPORTANTES

**OPENVPN:**

**UDP 1194**


**VPN:**

**10.8.0.0/24**


**VPS:**

**10.8.0.1**


**G806S:**

**10.8.0.2**


**PC/TÉCNICO:**

**Exemplo:**
**10.8.0.3**


**REDE INDUSTRIAL:**

**192.168.1.0/24**


**SIEMENS S7:**

**TCP 102**


**MODBUS TCP:**

**TCP 502**


## 34. ESTADO FINAL VALIDADO

**VPN SERVER:**
OK

**G806S conectado:**
OK

**PC conectado:**
OK

**PC fora da LAN industrial:**
OK

    Ping VPS:
OK

    Ping G806S:
OK

    Ping CLP:
OK

**TCP 102:**
OK

**TIA Portal ONLINE:**
OK

**IP Forwarding:**
OK

**NAT:**
OK

**NAT persistente após reboot:**
**CONFIGURADO**

FIM

