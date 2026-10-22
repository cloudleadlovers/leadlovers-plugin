# LeadLovers para Claude

![LeadLovers](logo.png)

Conecte o Claude à sua conta [LeadLovers](https://leadlovers.com) e trabalhe nela conversando em português: crie projetos, e-mails e campanhas, páginas de captura e funis completos, encontre e organize contatos. O plugin junta o conector da LeadLovers com seis skills que conduzem a conversa como um especialista da plataforma faria: perguntam só o que falta, em linguagem simples, nunca pedem códigos ou ids e confirmam antes de enviar um e-mail ou mudar muitos contatos de uma vez.

## O que vem no plugin

| Skill | Use quando quiser |
| --- | --- |
| `leadlovers:primeiros-passos` | Saber o que dá para fazer, ver um resumo da conta ou decidir por onde começar |
| `leadlovers:criar-projeto` | Criar um projeto (máquina) de e-mail, SMS, WhatsApp, Instagram, Telegram ou voz |
| `leadlovers:criar-email` | Criar, ajustar, ativar ou pausar um e-mail, uma campanha ou um e-mail automático de sequência |
| `leadlovers:criar-pagina` | Criar ou editar uma página de captura, de obrigado, de vendas ou de download |
| `leadlovers:organizar-contatos` | Encontrar, marcar com tag, mover, cadastrar ou corrigir contatos |
| `leadlovers:montar-funil` | Sair de um objetivo, como captar contatos para um e-book, para um funil pronto |

Você não precisa chamar as skills pelo nome: peça o que quer ("crie um e-mail de boas-vindas para o meu projeto do e-book") e o Claude escolhe a skill certa.

## Como instalar

**Claude (web, desktop e Cowork):** em Customize > Plugins, adicione o marketplace `cloudleadlovers/leadlovers-plugin` e instale o plugin LeadLovers. Depois, na aba Connectors do plugin, conecte a LeadLovers.

**Claude Code:**

```
/plugin marketplace add cloudleadlovers/leadlovers-plugin
/plugin install leadlovers@leadlovers
```

Ao conectar, o Claude abre a LeadLovers no navegador para você entrar e autorizar o acesso. Nenhuma chave de API é copiada nem gravada em arquivo, e a autorização pode ser revogada a qualquer momento no seu perfil da LeadLovers.

Se você já adicionou o conector da LeadLovers à mão (com o endereço `https://mcp.leadlovers.com/mcp`), remova-o antes de instalar o plugin, para não ficar com duas conexões.

## O que o plugin acessa

O plugin não roda nenhum programa no seu computador. Ele tem só textos de instrução (as skills) e o endereço do conector remoto da LeadLovers, `https://mcp.leadlovers.com/mcp`. O conector age na conta que você autorizou, pela API da LeadLovers, e só quando o Claude usa uma das ferramentas dele. Para criar o design de um e-mail com IA ou buscar imagens, o conector usa serviços parceiros, listados na [política de privacidade](https://mcp.leadlovers.com/politica-de-privacidade).

## English

The official LeadLovers plugin for Claude. It bundles the LeadLovers remote MCP connector (`https://mcp.leadlovers.com/mcp`, OAuth, no API key to copy) with six Brazilian Portuguese skills that walk non-technical LeadLovers customers from a request to a finished project, email, landing page, contact change or funnel. The skills ask only for what is missing, never ask for ids, and confirm before sending an email or changing many contacts. The plugin runs no local code. Privacy policy: <https://mcp.leadlovers.com/privacy-policy>.

## Para quem mantém este repositório

A pasta `skills/` é uma cópia gerada das skills mantidas no repositório do servidor MCP da LeadLovers e chega aqui por pull request automático. Não edite as skills neste repositório: a próxima sincronização sobrescreve a mudança.
