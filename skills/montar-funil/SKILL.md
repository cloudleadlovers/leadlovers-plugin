---
name: montar-funil
description: Monta um funil completo na LeadLovers a partir de um objetivo de negócio, com o cliente aprovando o plano antes. Use quando o cliente quiser captar contatos para um e-book, aula, cupom ou lista de espera, lançar ou vender um curso ou produto, ou disser que quer um funil, uma estratégia ou "tudo pronto". Faz uma entrevista curta, propõe em linguagem simples um projeto de e-mail, uma página de captura com formulário, uma página de obrigado e uma sequência de e-mails automáticos, pergunta uma vez se os e-mails ficam ativos ou salvos e cria tudo na ordem certa. Nunca cria campanha para a base inteira.
metadata:
  version: "1.0"
---

# Montar funil na LeadLovers

## Objetivo

Sair de um objetivo de negócio ("quero captar contatos para meu e-book") para um funil criado na conta: projeto, página de captura, página de obrigado e e-mails automáticos, com o cliente aprovando o plano e escolhendo o estado de envio uma única vez.

## Quando não usar

- O cliente quer só uma peça (um projeto, uma página, um e-mail): use `criar-projeto`, `criar-pagina` ou `criar-email`.
- O cliente quer uma campanha para a base: use `criar-email`. Esta skill nunca cria campanha.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`: "projeto", "página de captura", "e-mail automático", "ativo", "salvo".
- Nunca mostre id, nome de tool, nome de campo, JSON ou código HTML.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## Passo 1, entrevista curta

No máximo 4 perguntas, uma por vez, pulando o que o cliente já disse:

1. **Oferta:** o que ele vende ou divulga (curso, produto, serviço, evento).
2. **Público:** para quem é.
3. **Isca:** o que a pessoa ganha ao se cadastrar (e-book, aula gratuita, cupom, lista de espera), com o link de entrega se houver.
4. **Canal:** este funil é de e-mail. Se o cliente quiser outro canal, explique que o funil completo é montado por e-mail e ofereça criar o projeto do outro canal com `criar-projeto`.

## Passo 2, plano para aprovar

Antes de qualquer chamada de escrita, chame `get_machines` para evitar nome de projeto repetido e apresente o plano em linguagem simples:

> **Projeto de e-mail:** E-book Receitas Fit
> **Página de captura:** ebook-receitas-fit, com título, benefícios e o formulário de nome e e-mail
> **Página de obrigado:** obrigado-receitas-fit, dizendo que o e-book chegou por e-mail
> **E-mails automáticos (Sequência Inicial):**
> 1. Assim que a pessoa se cadastrar: "Seu e-book chegou", com o link de entrega
> 2. 1 dia depois: uma receita do e-book e uma dica
> 3. 2 dias depois: apresentação da oferta, com o link de compra

Na mesma mensagem, faça a pergunta de envio **uma vez para todos os e-mails**: "Quer deixar os e-mails ativos ou apenas salvos? Ativos: eles disparam para cada contato que se cadastrar. Salvos: ficam prontos, sem enviar, até você revisar e ativar." Recomende salvar e revisar antes de ativar.

Ajuste o plano até o cliente aprovar. Se ele escolher "ativos", mostre também o texto de cada e-mail e espere a aprovação antes de criar, porque eles vão disparar.

## Passo 3, criar na ordem

Siga `references/regras-de-criacao.md` em cada etapa. A ordem é fixa:

1. **Projeto** de e-mail com `create_machine`. Guarde a "Sequência Inicial" (`sequenceId`) e o e-mail do nível 1 (`emailId`). Não crie sequência nem outro e-mail de nível 1.
2. **Página de captura** com `create_page` (tipo `capture`, com o formulário padrão) no projeto criado.
3. **Página de obrigado** com `create_page` (tipo `next-steps`) no mesmo projeto.
4. **Primeiro e-mail:** preencha o nível 1 com `update_email` (`id` do `emailId`, `subject`, `body` e `enabled` conforme a escolha do passo 2).
5. **Demais e-mails**, na ordem, com `create_email` (`type: sequence`, projeto, "Sequência Inicial", `subject`, `body`, `enabled` da mesma escolha e o intervalo em `settings.sequence`).

Escreva cada página e cada e-mail a partir da entrevista, em português simples, com o nome do contato (`*|NOME|*`) na saudação dos e-mails e sem campos personalizados.

Se uma etapa falhar, pare, diga o que já foi criado e o que falhou, e ofereça continuar dali. Nunca recrie o que já existe: reaproveite o projeto, as páginas e os e-mails já criados.

## Resultado

Liste o que foi criado e o estado de envio de cada e-mail, como a resposta das tools confirmou:

> Criei o projeto E-book Receitas Fit com:
> - a página de captura ebook-receitas-fit e a página de obrigado obrigado-receitas-fit;
> - 3 e-mails automáticos na Sequência Inicial, salvos, sem enviar.

Termine com o que o cliente ainda faz na LeadLovers:

- conferir a publicação e o endereço das páginas;
- configurar, na página de captura, o envio para a página de obrigado depois do cadastro, que não é feito por aqui;
- revisar os e-mails e ativá-los quando aprovar (ou pedir "ative os e-mails do projeto E-book Receitas Fit");
- conferir se o projeto está ativo para receber contatos.

## Erros

- Conexão sem permissão de escrita: a conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo. Nada foi criado.
- LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; reconecte a LeadLovers no assistente.
- Recusa ao deixar um e-mail ativo (`SENDER_REQUIRED`, `ACCOUNT_SUSPENDED`, `EMAIL_CUSTOM_FIELD_NOT_SUPPORTED`): explique o motivo, ofereça salvar esse e os próximos e continue o funil.
- Qualquer outro erro: traduza em uma frase, diga o que já foi criado e o próximo passo.
