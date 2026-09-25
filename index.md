# Documentação Smart Projetos

Guias para instalar, configurar e operar o acesso remoto a equipamentos industriais por VPN.

> Os endereços IP e as redes reais foram mascarados. Substitua os valores entre colchetes pelos dados do ambiente ao aplicar os procedimentos.

## Comece por aqui

Se você está chegando agora, siga a sequência:

1. [Visão geral da VPN fixa]({{ site.baseurl }}/docs/vpn/visao-geral.html) — entenda os componentes, endereços e o caminho da conexão.
2. [Preparar o VPS]({{ site.baseurl }}/docs/vpn/preparar-vps.html) — acessar e atualizar o servidor Ubuntu.
3. [Instalar o servidor OpenVPN]({{ site.baseurl }}/docs/vpn/servidor-openvpn.html) — configurar OpenVPN, Easy-RSA, roteamento e firewall.
4. [Configurar clientes]({{ site.baseurl }}/docs/vpn/clientes.html) — emitir certificados e conectar o G806S ou um computador.
5. [Operação e manutenção]({{ site.baseurl }}/docs/vpn/operacao.html) — testar a conexão, diagnosticar problemas e manter backups.

## Documentação

### VPN fixa

Acesso roteado entre o VPS, um gateway USR-G806S e a rede industrial.

- [Visão geral e topologia]({{ site.baseurl }}/docs/vpn/visao-geral.html)
- [Preparação do VPS]({{ site.baseurl }}/docs/vpn/preparar-vps.html)
- [Instalação e configuração do servidor]({{ site.baseurl }}/docs/vpn/servidor-openvpn.html)
- [Criação e configuração de clientes]({{ site.baseurl }}/docs/vpn/clientes.html)
- [Operação, testes e manutenção]({{ site.baseurl }}/docs/vpn/operacao.html)

### Gateway portátil

Arquitetura de VPN dedicada com DNAT e SNAT para manutenção em redes industriais variáveis, inclusive quando os equipamentos não têm gateway configurado.

- [Gateway portátil: implementação e testes]({{ site.baseurl }}/docs/gateway-portatil.html)

## Endereços e portas de referência

| Item | Valor documentado |
| --- | --- |
| OpenVPN fixo | UDP 1194 |
| Rede VPN fixa | [REDE_VPN_FIXA]/24 |
| LAN industrial fixa | [REDE_LAN_INDUSTRIAL_FIXA]/24 |
| Siemens S7 | TCP 102 |
| Modbus TCP | TCP 502 |
| OpenVPN portátil | UDP 1195 |
| Rede VPN portátil | [REDE_VPN_PORTATIL]/24 |

Os endereços acima descrevem os cenários registrados nos procedimentos. Confirme-os antes de aplicar comandos em outro ambiente.

## Segurança

Arquivos .ovpn, chaves privadas e credenciais não devem ser publicados. A documentação explica quais arquivos são sensíveis e como planejar backups protegidos: consulte [Operação e manutenção]({{ site.baseurl }}/docs/vpn/operacao.html).

## Sobre os procedimentos

O conteúdo foi organizado a partir dos registros técnicos existentes. Passos marcados como validados refletem os testes registrados; adapte endereços, interfaces e regras à instalação em uso.
