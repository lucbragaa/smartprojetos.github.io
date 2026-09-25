---
title: Gateway portátil Smart: implementação e testes
layout: default
---

[← Início da documentação]({{ site.baseurl }}/)

# Gateway portátil Smart: implementação e testes

**GATEWAY PORTÁTIL SMART**
**Resumo da Implementação e Teste de Comunicação via VPN + DNAT + SNAT**

## 1. OBJETIVO

**Validar o conceito de Gateway Portátil Smart utilizando um USR-G806S conectado**
a uma VPN OpenVPN dedicada, permitindo acesso remoto a equipamentos de uma LAN
**industrial mesmo quando os equipamentos não possuem Gateway configurado.**

**O objetivo principal é permitir que um único Gateway Portátil possa ser levado**
para diferentes painéis/máquinas e utilizado para manutenção remota, sem
**necessidade de alterar a infraestrutura da VPN central a cada nova faixa de**
**IP industrial.**


## 2. ARQUITETURA DEFINIDA

Foi criada uma segunda VPN no mesmo VPS, separada da VPN utilizada pelos
**Gateways Fixos.**

**GATEWAY FIXO**
**Servidor OpenVPN:**
**UDP 1194**
**Rede VPN: [REDE_VPN_FIXA]/24**

**GATEWAY PORTÁTIL**
**Servidor OpenVPN:**
**UDP 1195**
**Rede VPN: [REDE_VPN_PORTATIL]/24**


## 3. ENDEREÇAMENTO DO TESTE

**VPS - VPN PORTÁTIL**
**[IP_VPN_VPS_PORTATIL]**

**G806S PORTÁTIL**
**VPN: [IP_VPN_G806S_PORTATIL]**

**PC SMART**
**VPN: [IP_VPN_TECNICO_PORTATIL]**

**LAN INDUSTRIAL DO TESTE**
**G806S LAN: [IP_LAN_G806S_PORTATIL]/24**

**PLC:**
**[IP_PLC_PORTATIL]/24**

**IMPORTANTE:**
**O PLC estava configurado sem Gateway.**


## 4. CONFIGURAÇÃO DO G806S

O CLIENT_1 do G806S, utilizado para a VPN FIXA, foi desligado durante o teste.

**CLIENT_2 foi configurado como:**

**Descrição:**
**Smart VPN Portait**

**Status:**
**Connected**

**O CLIENT_2 recebeu o endereço:**

**[IP_VPN_G806S_PORTATIL]**


## 5. CONFIGURAÇÃO DE FIREWALL / ZONAS

**A interface:**

**tun_CLIENT_2**

**foi associada à zona:**

vpn

**Na zona vpn:**

Input   = accept
Output  = accept
Forward = accept
Masquerading = habilitado
MSS Clamping = habilitado

**Foi configurado também o encaminhamento:**

vpn → lan

com Forward = accept.

**Não foi necessário habilitar:**

vpn → wan

para o fluxo VPN → LAN.

**Também não foi necessário adicionar a interface LAN à zona VPN.**

**Estrutura:**

**tun_CLIENT_2**
      ↓
**zona vpn**
      ↓
**zona lan**


## 6. PORT FORWARD / DNAT

**Foi utilizada a função:**

Firewall → Port Forwards

**Exemplo de regra para Siemens S7:**

**External zone:**
vpn

**External port:**
102

**Internal zone:**
lan

**Internal IP:**
**[IP_PLC_PORTATIL]**

**Internal port:**
102

**Fluxo:**

**[IP_VPN_G806S_PORTATIL]:102**
      ↓
**DNAT**
      ↓
**[IP_PLC_PORTATIL]:102**


## 7. DESCOBERTA IMPORTANTE SOBRE O GATEWAY DOS EQUIPAMENTOS

**Durante o teste foi utilizado inicialmente um PC industrial em:**

**[IP_EQUIPAMENTO_TESTE_PORTATIL]**

Quando esse equipamento estava sem Gateway configurado, o Port Forward não
**conseguia completar a comunicação.**

**Ao configurar temporariamente:**

Gateway = [IP_LAN_G806S_PORTATIL]

**o tráfego passou a funcionar.**

**Isso demonstrou que havia uma questão de RETORNO do tráfego:**

**O equipamento da LAN precisava saber para onde devolver os pacotes destinados**
**à rede VPN.**


## 8. SOLUÇÃO: SNAT

Para eliminar a necessidade de configurar Gateway nos equipamentos industriais,
**foi utilizada uma regra de SNAT.**

**A lógica é:**

**Origem original:**

**[IP_VPN_TECNICO_PORTATIL]**

**é traduzida pelo G806S para:**

**[IP_LAN_G806S_PORTATIL]**

Assim, para o equipamento industrial, a comunicação passa a parecer:

**Origem:**
**[IP_LAN_G806S_PORTATIL]**

**Destino:**
**[IP_PLC_PORTATIL]**

Como ambos pertencem à mesma LAN, o equipamento consegue responder
**diretamente ao G806S.**

**O G806S então desfaz as traduções e entrega a resposta ao cliente pela VPN.**


## 9. FLUXO COMPLETO

**PC SMART**
**[IP_VPN_TECNICO_PORTATIL]**
    |
    | OpenVPN
    |
    v
VPS
**[IP_VPN_VPS_PORTATIL]**
    |
    | VPN Portátil
    |
    v
**G806S**
VPN = [IP_VPN_G806S_PORTATIL]
LAN = [IP_LAN_G806S_PORTATIL]
    |
    | DNAT
    | [IP_VPN_G806S_PORTATIL]:102
    |        ↓
    | [IP_PLC_PORTATIL]:102
    |
    | SNAT
    | origem VPN
    |        ↓
    | [IP_LAN_G806S_PORTATIL]
    |
    v
PLC
**[IP_PLC_PORTATIL]**
**SEM GATEWAY**


## 10. TESTES REALIZADOS

**10.1 VPN**

**PC Smart conseguiu acessar:**

**[IP_VPN_G806S_PORTATIL]**

**Ping:**
OK

**A interface web do G806S foi acessada remotamente através de:**

**http://[IP_VPN_G806S_PORTATIL]**


10.2 G806S → PLC

**O próprio G806S conseguiu executar Ping para:**

**[IP_PLC_PORTATIL]**

**Resultado:**
0% de perda


**10.3 PLC TCP/102**

Fisicamente conectado à LAN industrial, foi confirmado:

**[IP_PLC_PORTATIL]:102**

**TCP:**
OK


**10.4 DNAT / TESTE INTERMEDIÁRIO**

**Foi criado um teste com:**

**[IP_VPN_G806S_PORTATIL]:1102**
        ↓
**[IP_EQUIPAMENTO_TESTE_PORTATIL]:9000**

Inicialmente, o equipamento [IP_EQUIPAMENTO_TESTE_PORTATIL] não conseguia responder corretamente
**sem um caminho de retorno.**

Após a utilização do Gateway/SNAT, a conexão passou a ser estabelecida.


**10.5 TIA PORTAL**

**Foi realizado acesso ONLINE ao PLC através da VPN Portátil.**

**O TIA Portal conseguiu estabelecer conexão utilizando o endereço virtual**
**apresentado pelo Gateway Portátil.**

**Resultado:**
**COMUNICAÇÃO ONLINE VALIDADA.**


## 11. CONCLUSÃO DO TESTE

**Foi validado o seguinte conceito:**

**VPN PORTÁTIL**
+
**G806S**
+
Firewall VPN → LAN
+
**DNAT / Port Forward**
+
**SNAT**
=
**ACESSO REMOTO A EQUIPAMENTOS INDUSTRIAIS SEM NECESSIDADE DE GATEWAY NO**
**EQUIPAMENTO INDUSTRIAL**


## 12. PRINCIPAL VANTAGEM

**O equipamento industrial não precisa possuir:**

Gateway = 192.168.x.1

**O G806S assume o papel de ponto de retorno da comunicação através do SNAT.**

Isso é particularmente interessante em instalações industriais existentes,
onde muitos PLCs, IHMs, inversores e outros dispositivos já estão configurados
**com IP fixo e não possuem Gateway configurado.**


## 13. IMPLICAÇÃO PARA O GATEWAY PORTÁTIL SMART

**A arquitetura permite manter a VPN central estável enquanto a rede industrial**
**atendida pelo Gateway Portátil pode variar.**

**Exemplo:**

**PAINEL A**
**PLC:**
**[IP_PLC_PAINEL_A]**

**PAINEL B**
**PLC:**
**[IP_PLC_PAINEL_B]**

**PAINEL C**
**PLC:**
**[IP_PLC_PAINEL_C]**

**A VPN Portátil continua utilizando a mesma estrutura.**

**A adaptação da rede industrial ocorre no próprio Gateway.**


## 14. LIMITAÇÕES OBSERVADAS

**O Port Forward é baseado em portas.**

**Exemplo:**

**TCP 102**
**TCP 502**
**TCP 80**
**TCP 443**
**etc.**

Portanto, o acesso não representa automaticamente toda a LAN industrial como
**uma rede roteada tradicional.**

Para vários equipamentos, podem ser necessárias várias regras de DNAT.

**Exemplo:**

**[IP_VPN_G806S_PORTATIL]:102**
        ↓
**PLC:102**

**[IP_VPN_EQUIPAMENTO_PORTATIL_2]:102**
        ↓
**IHM/PLC:102**

**[IP_VPN_EQUIPAMENTO_PORTATIL_3]:502**
        ↓
**Equipamento Modbus:502**


## 15. OBSERVAÇÃO SOBRE TIA PORTAL / PROFINET

**O acesso IP direto ao equipamento foi validado.**

Entretanto, recursos de descoberta PROFINET/DCP como "Accessible Devices" não
**devem ser considerados automaticamente suportados através de uma VPN roteada**
**com NAT.**

**O funcionamento validado está baseado em comunicação IP direta com o**
**equipamento.**


## 16. ARQUITETURA PRELIMINAR DO PRODUTO

**GATEWAY FIXO**
**VPN dedicada**
**Roteamento tradicional**
**LAN conhecida**
**Gateway fixo no painel**
**Equipamento integrado permanentemente ao cliente**


**GATEWAY PORTÁTIL**
**VPN dedicada**
**IP VPN fixo**
**DNAT / Port Forward**
**SNAT**
**LAN industrial variável**
**Equipamentos podem permanecer sem Gateway**
**Equipamento portátil para manutenção**


## 17. PRÓXIMOS TESTES

## 1. Repetir o mesmo cenário utilizando o PC conectado ao hotspot do celular,
**eliminando a rede local atual do PC Smart.**

## 2. Validar comunicação completa pela Internet:

**PC Smart**
  ↓
**Internet móvel**
  ↓
VPS
  ↓
**VPN Portátil**
  ↓
**G806S**
  ↓
**DNAT + SNAT**
  ↓
PLC

## 3. Testar mais de um equipamento simultaneamente.

## 4. Testar diferentes portas/protocolos:
**- Siemens S7 TCP/102**
**- Modbus TCP/502**
**- HTTP**
**- HTTPS**
**- Outros protocolos necessários**

## 5. Avaliar até onde o USR-G806S suporta a quantidade de regras necessária.

## 6. Comparar posteriormente com um Gateway Teltonika para a versão comercial
**definitiva do Gateway Portátil Smart.**
