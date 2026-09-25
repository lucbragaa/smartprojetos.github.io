---
title: Visão geral da infraestrutura VPN
layout: default
---

[← Início da documentação]({{ site.baseurl }}/)

# Visão geral da infraestrutura VPN

**OBJETIVO**

**TOPOLOGIA**

VPS
**[IP_PUBLICO_VPS]**

VPN
**[REDE_VPN_FIXA]/24**

VPS
**[IP_VPN_VPS_FIXO]**

**G806S**
**[IP_VPN_G806S_FIXO]**

**REDE INDUSTRIAL**
**[REDE_LAN_INDUSTRIAL_FIXA]/24**

**OPENVPN**
**UDP 1194**

**SIEMENS**
**TCP 102**

**MODBUS TCP**
**TCP 502**

**DOCUMENTOS RELACIONADOS**

**01 - Preparação e Inicialização do VPS**
**02 - Instalação e Configuração do OpenVPN Server**
**03 - Criação e Configuração de Clientes OpenVPN**
04 - Operação, Testes e Manutenção da VPN
