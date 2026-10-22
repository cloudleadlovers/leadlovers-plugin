# Estado de envio

## Os dois sentidos de "ativo"

Para o cliente, e-mail ativo é e-mail que dispara. Na LeadLovers isso é `enabled: true`. A API também tem um campo `active`, que só diz que o e-mail ocupa a posição dele na sequência; ele nunca é usado com o cliente. Todo e-mail é criado ocupando a posição e, sem `enabled: true`, com o envio pausado.

## Campanha (para a conta toda ou para um projeto)

| Escolha do cliente | O que fazer |
| --- | --- |
| Apenas criar | `enabled: false`, sem data. A campanha fica salva, desativada, e o cliente envia depois pela tela Campanhas, aba Agendadas |
| Enviar agora | Para todos: `enabled: true`, sem data, depois do resumo confirmado; contatos reais recebem. Do projeto: não é possível por aqui; explique, peça o aceite e crie com `enabled: false`, sem data |
| Agendar | Pergunte data e hora (horário de Brasília) e mande em `settings.broadcast.sendAt` com o fuso `-03:00` (por exemplo, `2026-10-23T09:00:00-03:00`). Para todos: `enabled: true`, depois do resumo confirmado; a campanha sai sozinha nesse horário. Do projeto: explique, peça o aceite e crie com `enabled: false`; ela aparece na aba Agendadas com essa data e hora, e o cliente só precisa ativar |

**Liberação por conta.** Ligar o envio de campanha por aqui chega conta a conta. Numa conta ainda fora da liberação, `create_email` com `enabled: true` responde `CAMPAIGN_SEND_NOT_ENABLED` e nada é criado: explique, pergunte se pode criar a campanha desativada (com a mesma data, se agendada) e diga que ela é ativada na tela Campanhas, aba Agendadas. Só crie com o aceite, e nunca repita `enabled: true`.

Para ligar o envio de uma campanha valem as exigências do e-mail automático (conteúdo, conta de disparo ativa, conta sem suspensão), menos a de campo personalizado: a campanha pode usar campos personalizados. A resposta traz quantos contatos ela alcança (`totalLeads`); o número final pode ser um pouco menor, porque contatos de sequências que bloqueiam campanhas ficam de fora.

Campanha já enviada (`EMAIL_ALREADY_SENT`) não muda mais; campanha agendada ou em envio (`EMAIL_SCHEDULED`) só muda depois de desativada na tela Campanhas.

<!-- funil -->
### E-mail automático: ativo ou salvo

Pergunte sempre, antes de criar: "Quer deixar o e-mail ativo ou apenas salvar? Ativo: ele será disparado assim que um contato chegar a esta etapa. Salvo: ele fica no lugar dele, sem enviar, e os contatos que chegarem aqui esperam até você ativar."

- **Ativo:** `enabled: true`. A LeadLovers envia o e-mail a cada contato que chega ao nível, seguindo o intervalo. Contatos reais recebem.
- **Salvo:** `enabled: false`. Nada é enviado. Os contatos que chegam ao nível ficam esperando e passam a receber quando o e-mail for ativado.
- **Recomendação:** salvar quando o cliente ainda quer revisar o texto ou o projeto já recebe contatos de verdade; ativo quando ele já aprovou o conteúdo e quer que o funil rode sozinho.

Para ficar ativo, a LeadLovers exige:

- conteúdo não vazio (`EMAIL_BODY_REQUIRED`);
- uma conta de disparo de e-mail ativa (`SENDER_REQUIRED`), configurada na LeadLovers;
- conta sem suspensão por pagamento em atraso (`ACCOUNT_SUSPENDED`);
- conteúdo sem tag de campo personalizado que o e-mail ainda não usava (`EMAIL_CUSTOM_FIELD_NOT_SUPPORTED`). As tags `*|NOME|*`, `*|EMAIL|*` e `*|TELEFONE|*` são da própria LeadLovers e não contam.

Se o conteúdo usar campo personalizado, avise antes de criar. Em qualquer dessas recusas, explique o motivo em uma frase e ofereça apenas salvar; nunca diga que ficou ativo.
<!-- /funil -->

## Ativar ou pausar depois

- E-mail automático: `update_email` com `enabled: true` (ativar) ou `enabled: false` (pausar), depois de confirmar com o cliente qual e-mail é e o que vai acontecer ("a partir de agora ele dispara para cada contato que chegar a esta etapa").
- Campanha: ativar, pausar e mudar a data são feitos na tela Campanhas da LeadLovers.
