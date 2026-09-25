---
title: Instalação e configuração do servidor OpenVPN
layout: default
---

[← Início da documentação](/)

# Instalação e configuração do servidor OpenVPN

**SMART PROJETOS - PROCEDIMENTO VPN**
    OPENVPN + VPS + USR G806S

## 3. INSTALAÇÃO DO OPENVPN

**O servidor utiliza:**

    OpenVPN 2.6.x
**Easy-RSA**
**Ubuntu 24.04**

**Instalação dos pacotes:**

    apt update
    apt install openvpn easy-rsa -y

**Para persistência das regras de firewall/NAT:**

    apt install iptables-persistent -y


## 4. EASY-RSA / AUTORIDADE CERTIFICADORA (CA)

**Diretório utilizado:**

**/etc/openvpn/easy-rsa**

**A CA é criada dentro:**

**/etc/openvpn/easy-rsa/pki/**

**A CA possui:**

ca.crt       = certificado público da autoridade
ca.key       = CHAVE PRIVADA DA AUTORIDADE - MUITO SENSÍVEL


**Inicialização do Easy-RSA:**

    cd /etc/openvpn/easy-rsa

    ./easyrsa init-pki
    ./easyrsa build-ca


**IMPORTANTE:**
Durante a criação da CA, será solicitado o Common Name.

**A CA é a autoridade que assina os certificados do servidor e**
**dos clientes.**


## 5. CRIAR CERTIFICADO DO SERVIDOR

**Criar certificado/requisição:**

    ./easyrsa gen-req server nopass

**Assinar certificado:**

    ./easyrsa sign-req server server

**Confirmar com:**

yes


**Arquivos importantes:**

**/etc/openvpn/easy-rsa/pki/ca.crt**

**/etc/openvpn/easy-rsa/pki/issued/server.crt**

**/etc/openvpn/easy-rsa/pki/private/server.key**


## 6. CRIAR CHAVE TLS-CRYPT

**Foi utilizada:**

    openvpn --genkey secret /etc/openvpn/ta.key

**Arquivo:**

**/etc/openvpn/ta.key**


**Essa chave é utilizada pelo:**

**tls-crypt /etc/openvpn/ta.key**


## 7. CONFIGURAÇÃO DO OPENVPN SERVER

**Arquivo principal:**

**/etc/openvpn/server/server.conf**


**CONFIGURAÇÃO UTILIZADA:**

**port 1194**
**proto udp**
**dev tun**

**ca /etc/openvpn/easy-rsa/pki/ca.crt**
**cert /etc/openvpn/easy-rsa/pki/issued/server.crt**
**key /etc/openvpn/easy-rsa/pki/private/server.key**
**dh none**

**topology subnet**
**server 10.8.0.0 255.255.255.0**

push "route 192.168.1.0 255.255.255.0"

**keepalive 10 120**

**tls-crypt /etc/openvpn/ta.key**

**cipher AES-256-GCM**
**auth SHA256**

**user nobody**
**group nogroup**

**persist-key**
**persist-tun**

**status /var/log/openvpn-status.log**
**verb 3**

**client-config-dir /etc/openvpn/ccd**


**NOTA:**
**O client-config-dir é utilizado para as rotas específicas do**
**cliente G806S.**

