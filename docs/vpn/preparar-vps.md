---
title: Preparação e inicialização do VPS
layout: default
---

[← Início da documentação]({{ site.baseurl }}/)

# Preparação e inicialização do VPS

**SMART PROJETOS - PROCEDIMENTO VPN**
    OPENVPN + VPS + USR G806S

**OBJETIVO**
**Criar uma VPN para permitir que notebooks/técnicos acessem**
**remotamente equipamentos industriais conectados à LAN de um**
**gateway USR G806S.**

**ARQUITETURA VALIDADA**

**INTERNET**
                    |
                    |
**+-------------+**
             |     VPS     |
             | Ubuntu      |
             | [IP_PUBLICO_VPS]
**+-------------+**
                    |
    OpenVPN Server
**[IP_VPN_VPS_FIXO]**
                    |
**VPN [REDE_VPN_FIXA]/24**
                    |
**+---------+---------+**
          |                   |
**G806S               Notebook**
**[IP_VPN_G806S_FIXO]            [IP_VPN_TECNICO_FIXO]**
          |
          |
**LAN industrial**
**[REDE_LAN_INDUSTRIAL_FIXA]/24**
          |
**+---- CLP [IP_EQUIPAMENTO_INDUSTRIAL_FIXO]**


**IMPORTANTE:**
**O G806S funciona como CLIENTE OpenVPN.**

**O VPS funciona como SERVIDOR OpenVPN.**

**O notebook/técnico também funciona como CLIENTE OpenVPN.**


## 1. ACESSAR O VPS

**SSH:**

    ssh root@[IP_PUBLICO_VPS]

**IP público do VPS:**
**[IP_PUBLICO_VPS]**


## 2. ATUALIZAR UBUNTU

    apt update
    apt upgrade -y
