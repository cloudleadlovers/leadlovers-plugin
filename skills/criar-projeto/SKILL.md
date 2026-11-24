---
name: criar-projeto
description: Cria um projeto (máquina) novo na LeadLovers conduzindo o cliente passo a passo. Use quando o cliente quiser criar um projeto, uma máquina nova ou um projeto de e-mail, SMS, WhatsApp, Instagram, Telegram ou voz, ou quando outra tarefa precisar de um projeto que ainda não existe. Pergunta o tipo de comunicação e o nome, evita nome repetido e aproveita a Sequência Inicial e o primeiro e-mail que todo projeto novo já traz, sem criar duplicados.
metadata:
  version: "1.0"
---

# Criar projeto na LeadLovers

## Objetivo

Criar um projeto com o tipo de comunicação e o nome que o cliente escolheu, sem duplicar projeto, sequência ou e-mail, e deixar claro o que ele já traz.

## Quando não usar

- O cliente quer projeto, página e e-mails de uma vez: use `montar-funil` quando ela estiver disponível.
- O projeto já existe e o cliente quer um e-mail nele: use `criar-email`.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`. Diga sempre "projeto"; "máquina" só para explicar que é o nome antigo da mesma coisa.
- Nunca mostre id, nome de tool, nome de campo ou JSON.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## Passo 1, tipo de comunicação

Pule esta pergunta se o pedido já disser o canal ("projeto de WhatsApp"). Senão, pergunte com uma linha sobre cada opção:

1. E-mail (recomendado se o cliente não souber): e-mails automáticos em sequência e campanhas.
2. SMS: mensagens de texto curtas para o celular.
3. WhatsApp: mensagens pelo WhatsApp.
4. Instagram: mensagens pelo Instagram.
5. Telegram: mensagens pelo Telegram.
6. Voz: mensagens de voz.

O que precisar ser conectado ao canal (número, conta) é configurado depois, na LeadLovers; não prometa que o canal já sai funcionando.

## Passo 2, nome

Sugira um nome a partir do objetivo do cliente ("Curso de Confeitaria", "E-book Receitas Fit"), nunca um genérico como "Projeto Inicial", e pergunte se pode usar esse ou se ele prefere outro. Até 100 caracteres.

Antes de criar, chame `get_machines` e compare o nome com os projetos existentes ignorando maiúsculas, acentos e espaços extras. Se houver um igual ou quase igual, ofereça:

1. Usar o projeto que já existe (recomendado quando o objetivo é o mesmo).
2. Criar com outro nome.

A LeadLovers não aceita dois projetos com o mesmo nome. Se o cliente insistir num nome que já existe, explique isso e repita as duas opções; nunca ofereça criar com o nome repetido.

## Passo 3, confirmar e criar

Confirme em uma linha: "Vou criar o projeto de e-mail Curso de Confeitaria. Posso criar?". Não existe forma de excluir um projeto por aqui, então espere o "sim".

<!-- funil -->
### Criar o projeto

- O nome não pode repetir o de um projeto da conta: a LeadLovers recusa com `NAME_EXISTS`. Confira com `get_machines` antes de propor o nome. Se o cliente insistir num nome que já existe, explique que não dá e ofereça usar o projeto existente ou outro nome; nunca ofereça criar com o nome repetido nem chame `create_machine` com ele.
- Chame `create_machine` com `name` e `type` (`email`, `sms`, `whatsapp`, `instagram`, `telegram` ou `voice`).
- Resposta com `sequenceId`: o projeto já veio com a sequência "Sequência Inicial". Num projeto de e-mail, `emailId` é o e-mail do nível 1 dessa sequência, ainda sem conteúdo e salvo, sem enviar. Guarde os dois ids.
- Nunca chame `create_sequence` com o nome "Sequência Inicial": a sequência já existe e a resposta seria `NAME_EXISTS`.
- Nunca crie outro e-mail para o nível 1. O primeiro e-mail automático do projeto preenche esse nível com `update_email`; um e-mail novo viraria o nível 2 e os contatos ficariam parados no nível 1, que está sem enviar.
- `emailId` nulo num projeto de e-mail: o primeiro e-mail não pôde ser criado junto. O primeiro e-mail automático é então criado com `create_email` na "Sequência Inicial".
- Resposta sem `sequenceId`: crie a sequência com `create_sequence`, nome "Sequência Inicial".
- `NAME_EXISTS` em `create_machine`: já existe um projeto com esse nome. Ofereça usar o existente ou escolher outro nome; não repita a chamada com o mesmo nome.

### Ícone das páginas

- O ícone da aba do navegador é um por projeto e vale para todas as páginas dele (veja "Ícone das páginas" no glossário). Num projeto de e-mail, depois de criar o projeto, ofereça uma vez: "Quer que as páginas desse projeto mostrem o ícone da sua marca na aba do navegador?", com as opções: o ícone do site do cliente, o logo dele, ou deixar sem ícone por enquanto.
- Precisa de um endereço `https://` de uma imagem PNG, JPG, WEBP, GIF ou ICO. Se o cliente tiver um site e você conseguir ler o site, prefira o ícone que ele declara para celular (`apple-touch-icon`) ou o maior PNG quadrado, e não o `favicon.ico`. Senão, peça o endereço da imagem do logo ou do ícone. Nunca invente um endereço.
- Logo em SVG não serve: peça a versão em PNG.
- Com o "sim", chame `set_machine_favicon` com o projeto e o endereço. A LeadLovers reduz para um ícone quadrado pequeno; diga isso se o logo for largo, porque ele aparece inteiro e bem pequeno.
- Nunca coloque o ícone no HTML de uma página (`<link rel="icon">`): ele já sai do projeto para todas as páginas.
- Recusas de `set_machine_favicon` (`FAVICON_UNSUPPORTED_TYPE`, `FAVICON_URL_NOT_ALLOWED`, `FAVICON_DOWNLOAD_FAILED`, `FAVICON_TOO_LARGE`, `FAVICON_CORRUPTED`): o projeto fica com o ícone que tinha. Explique em uma frase e peça outra imagem; não tente de novo com o mesmo endereço.
<!-- /funil -->

## Sequência extra

Só se o cliente pedir uma sequência além da inicial (por exemplo, uma para quem comprou). Antes de criar:

1. Explique em uma frase o que é uma sequência (o caminho de e-mails automáticos que o contato percorre, em ordem).
2. Pergunte o nome e espere a resposta. Pode sugerir um ("Compradores"), mas não crie com um nome que o cliente não aprovou.

Só então chame `create_sequence` com o projeto e o nome. Nome repetido no mesmo projeto responde `NAME_EXISTS`: ofereça usar a existente.

## Erros

- Resposta dizendo que a conexão não tem permissão para a ação (precisa do acesso de escrita): explique que a conexão com a LeadLovers só permite consultar e que, para criar, é preciso reconectar a LeadLovers no assistente e aprovar o acesso completo.
- Resposta dizendo que a LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; basta reconectar a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase simples, diga o próximo passo e não tente de novo sem o cliente pedir.

## Resultado

Diga o que a tool confirmou:

- E-mail: "Criei o projeto de e-mail Curso de Confeitaria. Ele já vem com a Sequência Inicial e um primeiro e-mail ainda vazio, guardado sem enviar."
- WhatsApp: "Criei o projeto de WhatsApp Atendimento. Ele já vem com a Sequência Inicial e uma primeira mensagem, salva sem enviar."
- Outros canais: "Criei o projeto de SMS Avisos. Ele já vem com a Sequência Inicial."

Num projeto de e-mail, ofereça o ícone das páginas (veja "Ícone das páginas") antes do próximo passo, e diga o que a tool confirmou: "Pronto: as páginas do projeto Curso de Confeitaria mostram o seu ícone na aba do navegador."

Termine oferecendo o próximo passo. Num projeto de e-mail: escrever o primeiro e-mail (`criar-email`) ou criar a página de captura do projeto (`criar-pagina`). Num projeto de WhatsApp: escrever a primeira mensagem ou criar o link de WhatsApp para a página (`criar-mensagem-whatsapp`), e diga que o número de WhatsApp é conectado na LeadLovers. Num projeto de outro canal, não ofereça página de captura nem e-mail: o formulário de captura padrão pede e-mail e o funil completo é montado por e-mail. Diga que a conexão do canal e as mensagens dele se configuram na LeadLovers.
