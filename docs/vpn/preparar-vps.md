---
title: Preparação e inicialização do VPS
layout: default
---

[← Início da documentação](/)

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
             | 191.252.218.108
**+-------------+**
                    |
    OpenVPN Server
**10.8.0.1**
                    |
**VPN 10.8.0.0/24**
                    |
**+---------+---------+**
          |                   |
**G806S               Notebook**
**10.8.0.2            10.8.0.3**
          |
          |
**LAN industrial**
**192.168.1.0/24**
          |
**+---- CLP 192.168.1.10**


**IMPORTANTE:**
**O G806S funciona como CLIENTE OpenVPN.**

**O VPS funciona como SERVIDOR OpenVPN.**

**O notebook/técnico também funciona como CLIENTE OpenVPN.**


## 1. ACESSAR O VPS

**SSH:**

    ssh root@191.252.218.108

**IP público do VPS:**
**191.252.218.108**


## 2. ATUALIZAR UBUNTU

    apt update
    apt upgrade -y

