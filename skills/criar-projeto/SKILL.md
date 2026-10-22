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

## Passo 3, confirmar e criar

Confirme em uma linha: "Vou criar o projeto de e-mail Curso de Confeitaria. Posso criar?". Não existe forma de excluir um projeto por aqui, então espere o "sim".

<!-- funil -->
### Criar o projeto

- Chame `create_machine` com `name` e `type` (`email`, `sms`, `whatsapp`, `instagram`, `telegram` ou `voice`).
- Resposta com `sequenceId`: o projeto já veio com a sequência "Sequência Inicial". Num projeto de e-mail, `emailId` é o e-mail do nível 1 dessa sequência, ainda sem conteúdo e salvo, sem enviar. Guarde os dois ids.
- Nunca chame `create_sequence` com o nome "Sequência Inicial": a sequência já existe e a resposta seria `NAME_EXISTS`.
- Nunca crie outro e-mail para o nível 1. O primeiro e-mail automático do projeto preenche esse nível com `update_email`; um e-mail novo viraria o nível 2 e os contatos ficariam parados no nível 1, que está sem enviar.
- `emailId` nulo num projeto de e-mail: o primeiro e-mail não pôde ser criado junto. O primeiro e-mail automático é então criado com `create_email` na "Sequência Inicial".
- Resposta sem `sequenceId`: crie a sequência com `create_sequence`, nome "Sequência Inicial".
- `NAME_EXISTS` em `create_machine`: já existe um projeto com esse nome. Ofereça usar o existente ou escolher outro nome; não repita a chamada com o mesmo nome.
<!-- /funil -->

## Sequência extra

Só se o cliente pedir uma sequência além da inicial (por exemplo, uma para quem comprou). Explique em uma frase o que é uma sequência, pergunte o nome e chame `create_sequence` com o projeto e o nome. Nome repetido no mesmo projeto responde `NAME_EXISTS`: ofereça usar a existente.

## Erros

- Resposta dizendo que a conexão não tem permissão para a ação (precisa do acesso de escrita): explique que a conexão com a LeadLovers só permite consultar e que, para criar, é preciso reconectar a LeadLovers no assistente e aprovar o acesso completo.
- Resposta dizendo que a LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; basta reconectar a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase simples, diga o próximo passo e não tente de novo sem o cliente pedir.

## Resultado

Diga o que a tool confirmou:

- E-mail: "Criei o projeto de e-mail Curso de Confeitaria. Ele já vem com a Sequência Inicial e um primeiro e-mail ainda vazio, guardado sem enviar."
- Outros canais: "Criei o projeto de WhatsApp Atendimento. Ele já vem com a Sequência Inicial."

Termine oferecendo o próximo passo: escrever o primeiro e-mail (`criar-email`) ou criar a página de captura do projeto (`criar-pagina`).
