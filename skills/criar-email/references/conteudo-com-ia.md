# Conteúdo com design de IA

Use quando o cliente quer um e-mail com layout visual em vez de um texto simples. O design é feito por três tools em cadeia: `create_email_template`, `edit_email_template` e `finalize_email_template`.

## 1. Descrição

Peça em uma pergunta aberta do que o e-mail fala, para quem e qual o link do botão, se houver. Complete o que faltar com perguntas curtas, uma por vez. O assunto é combinado à parte, com o cliente.

## 2. Layout

Só pergunte se o cliente se importar com o formato. Sem preferência, use `single-column` (uma coluna, recomendado). Opções em linguagem simples:

| Para o cliente | `layout` |
| --- | --- |
| Uma coluna, texto e botão (recomendado) | `single-column` |
| Imagem grande no topo e texto abaixo | `hero-content` |
| Duas colunas | `two-column` |
| Três colunas, como vitrine de produtos | `three-column` |
| Mosaico de imagens | `grid-mosaic` |
| Aviso curto e direto | `transactional-minimalist` |

## 3. Criar

Chame `create_email_template` com:

- `prompt`: a descrição do cliente com as palavras dele (pelo menos 20 caracteres). Não acrescente estrutura técnica, rótulos ou instruções de formatação; a tool já sabe montar o e-mail.
- `layout`: o do passo 2.
- `language`: `pt-BR`.

A resposta traz `sessionId` e `htmlPreview`. Se o seu ambiente consegue mostrar HTML, mostre a prévia; senão, descreva as seções (título, imagem, texto, botão). Nunca cole o código.

## 4. Ajustar

Cada pedido de mudança ("troque a cor do botão", "coloque o nome da empresa") é uma edição do mesmo design: `edit_email_template` com o `sessionId` e `editInstructions` detalhando a mudança, com o texto exato entre aspas. Nunca crie outro design para uma edição. Repita até o cliente aprovar.

## 5. Finalizar e gravar

1. `finalize_email_template` com o `sessionId`. A resposta traz `fullJson`, o design no formato do editor visual da LeadLovers.
2. No `create_email`, envie `body` com o `htmlPreview` da última versão aprovada e `json` com o `fullJson` como texto. Não envie `editor`: com `json`, o e-mail já abre no editor visual.

O rascunho vale por 1 hora. Se a tool disser que a sessão expirou ou não foi encontrada, explique e ofereça criar o design de novo.

## Nível 1 de projeto novo

O e-mail do nível 1 que vem com o projeto é preenchido com `update_email`, que recebe o HTML (`body`) mas não o design (`json`). O design entra pronto, mas esse e-mail continua no editor de HTML da LeadLovers, sem o editor visual. Avise o cliente antes, porque mudanças futuras feitas por ele na tela serão no HTML.

## E-mail que já existe

O design com IA serve para e-mail novo. Para mudar um e-mail que já existe, altere o texto pela seção "Ajustar, ativar ou pausar depois" da skill ou use o editor da LeadLovers.
