---
title: Criação e configuração de clientes OpenVPN
layout: default
---

[← Início da documentação]({{ site.baseurl }}/)

# Criação e configuração de clientes OpenVPN

**SMART PROJETOS - PROCEDIMENTO VPN**
    OPENVPN + VPS + USR G806S

## 8. CRIAR CONFIGURAÇÃO ESPECÍFICA DO G806S

**Diretório:**

**/etc/openvpn/ccd**

**Criar arquivo:**

**/etc/openvpn/ccd/g806s**


**Conteúdo:**

**iroute [REDE_LAN_INDUSTRIAL_FIXA] 255.255.255.0**


**EXPLICAÇÃO:**

O "push route" informa ao cliente VPN que a rede
**[REDE_LAN_INDUSTRIAL_FIXA]/24 existe através da VPN.**

O "iroute" informa ao servidor OpenVPN que a rede
**[REDE_LAN_INDUSTRIAL_FIXA]/24 está atrás especificamente do cliente g806s.**


## 9. ATIVAR IP FORWARDING NO VPS

**Ativar imediatamente:**

    sysctl -w net.ipv4.ip_forward=1


**Tornar permanente:**

    echo 'net.ipv4.ip_forward=1' > /etc/sysctl.d/99-openvpn-forward.conf


**Aplicar:**

    sysctl -p /etc/sysctl.d/99-openvpn-forward.conf


**Verificar:**

    sysctl net.ipv4.ip_forward


**Resultado esperado:**

net.ipv4.ip_forward = 1


## 10. NAT NO VPS

**Foi utilizada a interface pública:**

**enX0**


**Regra:**

    iptables -t nat -A POSTROUTING -s [REDE_VPN_FIXA]/24 -o enX0 -j MASQUERADE


**Verificar:**

    iptables -t nat -L -n


**Deve aparecer:**

**MASQUERADE**
**[REDE_VPN_FIXA]/24**
**0.0.0.0/0**


## 11. TORNAR IPTABLES PERSISTENTE

**Instalar:**

    apt install iptables-persistent -y


**Salvar regras:**

    netfilter-persistent save


**Arquivo:**

**/etc/iptables/rules.v4**


**Verificar:**

    cat /etc/iptables/rules.v4


**Deve existir a regra:**

**-A POSTROUTING -s [REDE_VPN_FIXA]/24 -o enX0 -j MASQUERADE**


**IMPORTANTE:**
Sem isso, o NAT criado manualmente pode desaparecer após
**reinicialização do VPS.**


## 12. INICIAR / REINICIAR OPENVPN

**Iniciar:**

    systemctl start openvpn-server@server


**Reiniciar:**

    systemctl restart openvpn-server@server


**Verificar:**

    systemctl status openvpn-server@server --no-pager


**Resultado esperado:**

**Active: active (running)**

**Status:**
**Initialization Sequence Completed**


**Para habilitar no boot:**

    systemctl enable openvpn-server@server


## 13. VERIFICAR REDE DO VPS

**Comandos úteis:**

    ip addr

    ip route


**A rede VPN deve aparecer aproximadamente como:**

**[REDE_VPN_FIXA]/24 dev tun0**


**E o VPS:**

**[IP_VPN_VPS_FIXO]**


## 14. CRIAR UM NOVO CLIENTE OPENVPN

Exemplo: criar o cliente "smart-pc".

**Entrar no Easy-RSA:**

    cd /etc/openvpn/easy-rsa


**Criar chave privada + requisição:**

    ./easyrsa gen-req smart-pc nopass


**Assinar como cliente:**

    ./easyrsa sign-req client smart-pc


**Confirmar:**

yes


**Serão criados:**

**/etc/openvpn/easy-rsa/pki/private/smart-pc.key**

**/etc/openvpn/easy-rsa/pki/issued/smart-pc.crt**


**Também são necessários:**

**/etc/openvpn/easy-rsa/pki/ca.crt**

**/etc/openvpn/ta.key**


## 15. ARQUIVOS DE UM CLIENTE

**Um cliente precisa essencialmente de:**

CA:
**ca.crt**

**Certificado do cliente:**
**smart-pc.crt**

**Chave privada do cliente:**
**smart-pc.key**

**Chave TLS:**
**ta.key**

**Configuração:**
**smart-pc.ovpn**


## 16. ARQUIVO .OVPN

**O arquivo .ovpn é o arquivo de configuração que será importado**
**no OpenVPN Connect.**

**Ele contém as informações necessárias para o cliente conectar**
**ao servidor.**

No nosso caso, o arquivo foi criado como:

**/etc/openvpn/smart-pc.ovpn**


**Depois foi transferido para o PC.**


**IMPORTANTE:**

**O .ovpn pode conter a chave privada do cliente.**

**Portanto:**

**NÃO enviar esse arquivo por WhatsApp.**

**NÃO deixar em pasta pública.**

**NÃO colocar em repositório público.**

**Tratar o .ovpn como CREDENCIAL.**


## 17. CONFIGURAÇÃO DO G806S

**No USR-G806S:**

VPN
  >
**OpenVPN**


**Utilizar um dos slots de CLIENT.**

**No nosso caso:**

**CLIENT_1**


**Configuração:**

**Enable: ON**

**Enable OpenVPN Config from file: ON**

    OpenVPN Config File:
**arquivo .ovpn correspondente ao G806S**


**Depois:**

Save & Apply


**Resultado esperado:**

**CLIENT_1**
**Status: Connected**


**No nosso teste:**

**IP VPN do G806S:**

**[IP_VPN_G806S_FIXO]**


## 18. ROTA NO G806S

**O G806S possui a configuração de rota local do OpenVPN.**

**Para a rede industrial:**

**Subnet:**
**[REDE_LAN_INDUSTRIAL_FIXA]**

**Netmask:**
**255.255.255.0**


**Isso permite ao G806S encaminhar o tráfego recebido pela VPN**
**para a LAN industrial.**


## 19. CLIENTE WINDOWS

**Instalar:**

    OpenVPN Connect


**Importar:**

**smart-pc.ovpn**


**Conectar.**

**Resultado esperado:**

**Securely Connected**


**O PC recebe um IP da VPN.**

**No nosso teste:**

PC:
**[IP_VPN_TECNICO_FIXO]**
