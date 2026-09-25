# Contexto do projeto para continuidade

Este arquivo registra o contexto técnico e editorial do repositório para a próxima sessão de trabalho ou outra IA. Ele é excluído do GitHub Pages por `exclude` em `_config.yml` e não deve aparecer no site publicado.

## Objetivo do repositório

O repositório publica a documentação técnica da Smart Projetos no GitHub Pages. O conteúdo atual descreve dois cenários de acesso remoto a redes industriais:

1. VPN fixa com VPS Ubuntu, OpenVPN, gateway USR-G806S e uma LAN industrial roteada.
2. Gateway portátil com uma VPN separada e regras DNAT/SNAT para acessar equipamentos em redes industriais variáveis, inclusive equipamentos sem gateway configurado.

## Estrutura atual

```text
smartprojetos.github.io/
├── _config.yml                 # Configuração do Jekyll e exclusão deste arquivo
├── index.md                    # Página inicial e roteiro de leitura
├── README.md                   # Apresentação curta do repositório
├── CONTEXTO_IA.md              # Este arquivo; não é publicado
└── docs/
    ├── gateway-portatil.md     # Implementação e testes do gateway portátil
    └── vpn/
        ├── visao-geral.md      # Topologia, endereçamento e portas
        ├── preparar-vps.md     # Acesso e atualização do Ubuntu
        ├── servidor-openvpn.md # OpenVPN, Easy-RSA e configuração do servidor
        ├── clientes.md         # G806S, clientes de PC, certificados e rotas
        └── operacao.md         # Testes, diagnóstico, chaves e backup
```

## Navegação e publicação

- O site usa `jekyll-theme-minimal`.
- A página inicial é `index.md`.
- Os links internos da página inicial usam caminhos absolutos com extensão `.html`, por exemplo `/docs/vpn/clientes.html`. Essa é a URL gerada pelo Jekyll para os arquivos Markdown.
- Cada página dentro de `docs/` tem front matter Jekyll e um link de retorno para a página inicial.
- `CONTEXTO_IA.md` permanece na raiz para ser fácil de localizar e está explicitamente excluído em `_config.yml`.

## Fontes usadas na primeira organização

Os procedimentos vieram de `D:\projects\docs` e foram convertidos para Markdown organizado:

- `00 - Visão Geral da Infraestrutura VPN.txt`
- `01 - Preparação e Inicialização do VPS.txt`
- `02 - Instalação e Configuração do OpenVPN Server.txt`
- `03 - Criação e Configuração de Clientes OpenVPN.txt`
- `04 - Operação, Testes e Manutenção da VPN.txt`
- `00 - Separar em partes.txt` para o cenário do gateway portátil

O arquivo `000 - Infraestrutura.txt` foi usado como referência de endereços, caminhos e componentes, mas não ganhou uma página própria porque seu conteúdo está incorporado na visão geral e nas demais páginas.

## Conteúdo técnico documentado

### VPN fixa

- VPS documentado: `191.252.218.108`.
- OpenVPN: UDP `1194`, rede `10.8.0.0/24` e VPS VPN `10.8.0.1`.
- G806S: VPN `10.8.0.2`; LAN industrial de referência `192.168.1.0/24`.
- Exemplo de CLP: `192.168.1.10`.
- Protocolos de teste: Siemens S7 TCP `102` e Modbus TCP `502`.

### Gateway portátil

- OpenVPN separado: UDP `1195`, rede `10.9.0.0/24` e VPS VPN `10.9.0.1`.
- O cenário documenta DNAT/Port Forward e SNAT no G806S para permitir o retorno do tráfego quando o equipamento industrial não tem gateway.
- O acesso validado é por IP e porta. A descoberta PROFINET/DCP não deve ser considerada suportada automaticamente.

## Decisões editoriais tomadas

- A página inicial foi pensada para quem está começando: primeiro mostra a sequência de leitura e depois agrupa os guias por cenário.
- A documentação separa VPN fixa e gateway portátil porque são arquiteturas e faixas de endereçamento diferentes.
- Os comandos originais foram preservados como blocos indentados em Markdown. Eles foram registrados como procedimentos de referência e podem exigir adaptação para outro ambiente.
- Os textos não foram alterados para ocultar os endereços de exemplo; antes de tornar o repositório público, revisar se IPs, nomes de clientes, topologia e qualquer dado operacional podem ser divulgados.

## Cuidados de segurança

- Nunca adicionar chaves privadas, certificados com chave privada, arquivos `.ovpn` com credenciais ou backups de PKI ao repositório.
- Arquivos especialmente sensíveis incluem `ca.key`, chaves privadas de servidor e clientes, `ta.key` e perfis `.ovpn` que contenham chaves.
- A documentação pode descrever caminhos e nomes de arquivos sensíveis, mas não deve conter seu conteúdo.

## Estado de trabalho

- A estrutura Markdown e os links de navegação foram criados localmente.
- As alterações ainda precisam ser revisadas, commitadas e enviadas ao repositório remoto para que o GitHub Pages publique a versão atual.
- Nenhum build local do Jekyll foi executado nesta sessão.

## Próximas ações sugeridas

1. Revisar os dados operacionais e remover ou generalizar informações que não devam ser públicas.
2. Conferir a publicação no GitHub Pages depois do push.
3. Se for necessário manter versões internas e públicas, separar o conteúdo confidencial em um repositório privado ou em documentação interna.
4. Atualizar este arquivo quando a arquitetura, a navegação ou a forma de publicação mudar.
