# Documentação Smart Projetos

Guias para instalar, configurar e operar o acesso remoto a equipamentos industriais por VPN.

## Comece por aqui

Se você está chegando agora, siga a sequência:

1. [Visão geral da VPN fixa](/docs/vpn/visao-geral.html) — entenda os componentes, endereços e o caminho da conexão.
2. [Preparar o VPS](/docs/vpn/preparar-vps.html) — acessar e atualizar o servidor Ubuntu.
3. [Instalar o servidor OpenVPN](/docs/vpn/servidor-openvpn.html) — configurar OpenVPN, Easy-RSA, roteamento e firewall.
4. [Configurar clientes](/docs/vpn/clientes.html) — emitir certificados e conectar o G806S ou um computador.
5. [Operação e manutenção](/docs/vpn/operacao.html) — testar a conexão, diagnosticar problemas e manter backups.

## Documentação

### VPN fixa

Acesso roteado entre o VPS, um gateway USR-G806S e a rede industrial.

- [Visão geral e topologia](/docs/vpn/visao-geral.html)
- [Preparação do VPS](/docs/vpn/preparar-vps.html)
- [Instalação e configuração do servidor](/docs/vpn/servidor-openvpn.html)
- [Criação e configuração de clientes](/docs/vpn/clientes.html)
- [Operação, testes e manutenção](/docs/vpn/operacao.html)

### Gateway portátil

Arquitetura de VPN dedicada com DNAT e SNAT para manutenção em redes industriais variáveis, inclusive quando os equipamentos não têm gateway configurado.

- [Gateway portátil: implementação e testes](/docs/gateway-portatil.html)

## Endereços e portas de referência

| Item | Valor documentado |
| --- | --- |
| OpenVPN fixo | UDP 1194 |
| Rede VPN fixa | 10.8.0.0/24 |
| LAN industrial fixa | 192.168.1.0/24 |
| Siemens S7 | TCP 102 |
| Modbus TCP | TCP 502 |
| OpenVPN portátil | UDP 1195 |
| Rede VPN portátil | 10.9.0.0/24 |

Os endereços acima descrevem os cenários registrados nos procedimentos. Confirme-os antes de aplicar comandos em outro ambiente.

## Segurança

Arquivos .ovpn, chaves privadas e credenciais não devem ser publicados. A documentação explica quais arquivos são sensíveis e como planejar backups protegidos: consulte [Operação e manutenção](/docs/vpn/operacao.html).

## Sobre os procedimentos

O conteúdo foi organizado a partir dos registros técnicos existentes. Passos marcados como validados refletem os testes registrados; adapte endereços, interfaces e regras à instalação em uso.


