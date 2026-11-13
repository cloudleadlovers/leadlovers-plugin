---
name: criar-email
description: Cria, ajusta, ativa ou pausa e-mails na LeadLovers com o cliente, sem disparar nada por engano. Use quando o cliente falar em e-mail, campanha, newsletter, disparo, broadcast, e-mail marketing, e-mail de boas-vindas ou e-mail automático de uma sequência, ou quiser mudar o texto, ativar ou pausar um e-mail que já existe. Sempre pergunta o tipo (campanha para todos os contatos, campanha de um projeto ou e-mail automático de uma sequência) e o que fazer com o envio (enviar agora, agendar ou só criar a campanha; deixar ativo ou só salvar o e-mail automático), e confirma um resumo antes de criar. Escreve o conteúdo a partir do texto do cliente ou cria um design com IA.
metadata:
  version: "1.0"
---

# Criar e-mail na LeadLovers

## Regra que não pode falhar

`create_email` só é chamado depois que o cliente:

1. respondeu o **tipo** do e-mail (passo 1);
2. respondeu o **estado de envio** (passo 4);
3. disse "sim" ao **resumo** (passo 5).

Isso vale mesmo quando o pedido parece completo ("mande agora uma campanha para todos"): ele ainda passa pela confirmação. E nunca diga que um e-mail foi enviado, agendado ou ativado sem a resposta da tool confirmar.

## Quando não usar

- Mudar, ativar ou pausar um e-mail que já existe: siga a seção "Ajustar, ativar ou pausar depois", no fim. Nunca use `create_email` para isso, porque ele cria outro e-mail.
- O cliente quer o funil inteiro (projeto, página e e-mails): use `montar-funil` quando ela estiver disponível.
- Página de captura ou de vendas: use `criar-pagina`.
- Mensagem ou disparo de WhatsApp, num projeto de WhatsApp: use `criar-mensagem-whatsapp`.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`: "projeto", "campanha", "e-mail automático", "ativo", "salvo".
- "Ativo" é sempre o sentido do cliente: o e-mail dispara. Nunca use "ativo" para dizer que o e-mail só ocupa o nível.
- Nunca mostre id, nome de tool, nome de campo, JSON ou código HTML.
- Uma pergunta por vez, com opções fechadas e uma recomendada. Projeto e sequência são escolhidos pelo nome; nunca peça id.

## Passo 1, tipo

Se o pedido não deixar o tipo claro, **pergunte sempre, nunca presuma**:

1. **Campanha para todos os contatos da conta**: um e-mail avulso para a base inteira. Na tool: `type: broadcast`, sem `machineId`.
2. **Campanha para os contatos de um projeto**: um e-mail avulso só para quem está num projeto. Na tool: `type: broadcast` com `machineId`.
3. **E-mail automático de uma sequência**: vai sozinho para cada contato quando ele chega a uma etapa do projeto (boas-vindas, aula 1, lembrete). Na tool: `type: sequence` com `machineId` e `sequenceId`.

Sugira a opção que o pedido indica ("boas-vindas", "depois que se cadastrar", "sequência": 3; "newsletter", "promoção para todos", "aviso geral": 1), mas espere a resposta.

## Passo 2, destino

- **Tipo 1:** não há destino a escolher.
- **Tipo 2:** chame `get_machines` e ofereça pelo nome os projetos de e-mail (tipo 1 na listagem), fora os de automação (veja o glossário). Um só: confirme que é ele. Nenhum: explique que a campanha do projeto precisa de um projeto de e-mail e ofereça criar um com `criar-projeto`.
- **Tipo 3:** escolha o projeto como no tipo 2, depois a sequência com `get_sequences` (se houver só uma, diga qual é e siga). Mostre os e-mails que a sequência já tem com `get_emails` (posição e assunto), para o cliente entender onde o novo vai entrar.

<!-- funil -->
### E-mail automático: intervalo e nível 1

- **Intervalo.** O intervalo conta a partir do e-mail anterior da sequência (de quando o contato chegou ao nível), não da entrada do contato. Combine com o cliente o dia da sequência em que o e-mail sai, contado da entrada (dia 0), e calcule `sendAfter.days` como esse dia menos o dia do e-mail anterior, que vem em `sequenceDay` no `get_emails` (dia 5 depois de um no dia 2 dá 3, nunca 5). Para o primeiro e-mail de boas-vindas, sugira "assim que o contato entrar" (dia 0). Envie em `settings.sequence` com `sendAfter` (`days`, `hours`, `minutes`), `startTime: "00:00"` e `endTime: "23:59"`. Janela de horário (por exemplo, só das 8h às 18h) e dias da semana (`weekdays`, 1 = segunda a 7 = domingo) só se o cliente pedir.
- **Vários e-mails de uma vez.** Antes de gravar, confirme com o cliente uma tabela com nível, dia da sequência e intervalo desde o anterior (ex.: nível 2, dia 3, 3 dias depois do anterior; nível 3, dia 6, 3 dias depois). Os dias crescem, e cada intervalo é a diferença para a linha de cima.
- **Nível 1 de projeto novo.** Se a sequência tem só o e-mail do nível 1 que veio com o projeto (o `emailId` que `create_machine` devolveu, ou um nível 1 que `get_email` mostra sem conteúdo), o primeiro e-mail automático **preenche esse nível** com `update_email` (`id` desse e-mail, `subject`, `body` e `enabled` conforme o estado de envio) em vez de criar outro. Criar um novo faria dele o nível 2, e os contatos ficariam parados no nível 1, que está sem enviar. O nível 1 sai na entrada do contato (intervalo zero); se o cliente quiser outro, mande junto `sequence.sendAfter` no mesmo `update_email`.
<!-- /funil -->

## Passo 3, conteúdo

Pergunte como o cliente prefere:

1. **Ele traz o texto** (ou a ideia em poucas linhas): monte o assunto e o HTML (título, parágrafos, um botão se houver link) como em "Montar o HTML do e-mail", logo abaixo. Mostre o assunto e o texto como o contato vai ler, nunca o código, e ajuste até ele aprovar. A aprovação precisa ser do texto: se a resposta falar de outra coisa (por exemplo, "apenas salvar"), anote e pergunte de novo se o texto está aprovado. O e-mail leva exatamente o texto aprovado; qualquer mudança depois disso, até de grafia, volta para o cliente antes de criar.
2. **Design com IA**: um e-mail com layout visual. Siga `references/conteudo-com-ia.md`.

<!-- funil -->
### Montar o HTML do e-mail

Escreva o `body` no formato que o editor visual da LeadLovers entende, para o cliente poder ajustar depois com um clique e o e-mail abrir bem em qualquer caixa de entrada. Vale para `create_email` e para o `update_email` que preenche o nível 1:

- uma tabela principal centralizada, com uma linha (`<tr>`) por seção (topo, título, texto, botão, rodapé), com `width="600"` e `style="width:100%;max-width:600px"`. Nunca `width:600px` no estilo: no celular o e-mail estoura a tela e rola para o lado;
- em volta dela, uma tabela só do Outlook, que não lê `max-width`: `<!--[if mso]><table role="presentation" width="600" align="center" cellpadding="0" cellspacing="0" border="0"><tr><td><![endif]-->` antes e `<!--[if mso]></td></tr></table><![endif]-->` depois;
- imagem que ocupa a largura com `width` no atributo e `width:100%;height:auto` no estilo;
- todo o estilo inline, em cada elemento, sem `<style>`, e nenhum `<script>`;
- só fontes seguras: Arial, Georgia, Verdana, Trebuchet MS, Tahoma ou Times New Roman;
- toda imagem com `alt` que descreve a imagem.

Não envie `editor`: a LeadLovers escolhe o editor do e-mail.
<!-- /funil -->

Para o nome do contato, use a tag `*|NOME|*` no texto ("Oi, *|NOME|*!"); `*|EMAIL|*` e `*|TELEFONE|*` também são preenchidas pela LeadLovers. Evite tags de campo personalizado: elas impedem deixar o e-mail ativo (veja `references/estado-de-envio.md`). Imagens só quando o cliente pedir, com `search_images`.

O remetente não é perguntado: omita `settings.from` e a LeadLovers usa a conta de disparo do projeto ou, sem ela, a padrão da conta.

## Passo 4, estado de envio

Pergunte **sempre**, mesmo que o pedido pareça dizer. Detalhes, limites de hoje e recusas em `references/estado-de-envio.md`.

| Tipo | Pergunta ao cliente | Na tool |
| --- | --- | --- |
| Campanha (tipos 1 e 2) | "Quer enviar agora, agendar para uma data e hora, ou apenas criar a campanha e enviar depois?" | Apenas criar: `enabled: false`, sem data. Tipo 1, enviar agora: `enabled: true`, sem data. Tipo 1, agendar: `enabled: true` com a data e a hora em `settings.broadcast.sendAt`. Tipo 2: sempre `enabled: false`; veja abaixo |
| E-mail automático (tipo 3) | "Quer deixar o e-mail ativo ou apenas salvar? Ativo: ele será disparado assim que um contato chegar a esta etapa. Salvo: ele fica no lugar dele, sem enviar, e os contatos que chegarem aqui esperam até você ativar." | Ativo: `enabled: true`. Salvo: `enabled: false` |

**Campanha para todos com "enviar agora" ou "agendar".** O envio é ligado por aqui (`enabled: true`), só depois do "sim" ao resumo do passo 5, porque contatos reais recebem. Essa liberação chega conta a conta: numa conta ainda fora dela, `create_email` recusa com `CAMPAIGN_SEND_NOT_ENABLED` e nada é criado. Nesse caso, diga que o envio de campanha por aqui ainda não foi liberado para a conta, pergunte se pode criar a campanha desativada (com a mesma data, se for agendada) e explique que ela é ativada na tela Campanhas, aba Agendadas; só crie com o aceite, com `enabled: false`. Nunca repita `enabled: true` depois dessa recusa.

**Campanha do projeto com "enviar agora" ou "agendar".** O envio não é ligado por aqui. Diga isso antes de criar, pergunte se pode criar a campanha desativada e explique que ela é ativada na LeadLovers; só crie com o aceite, com `enabled: false`.

Para agendar, pergunte a data e a hora (horário de Brasília), repita no resumo com o dia da semana ("sexta, 23/10, às 9h") e mande em `settings.broadcast.sendAt` com o fuso `-03:00` (por exemplo, `2026-10-23T09:00:00-03:00`). A data precisa estar no futuro. Desativada, a campanha aparece na aba Agendadas com essa data e hora, e o cliente só precisa ativar.

## Passo 5, confirmação

Monte um resumo curto e espere um "sim" explícito:

- **Tipo e destino:** "Campanha para todos os seus contatos", "Campanha para o projeto Curso de Confeitaria", "E-mail automático do projeto Curso de Confeitaria, Sequência Inicial, depois do e-mail Boas-vindas".
- **Assunto.**
- **Público:** conta toda, "cerca de N contatos" pelo total que `get_leads` informa sem filtros (página 1, `pageSize: "10"`); projeto, pelo `leadCount` de `get_machines` com `includeLeadCount: true`; e-mail automático, "cada contato que chegar a esta etapa".
- **Envio, em palavras:** "sai agora, assim que você confirmar", "sai sozinha na sexta, 23/10, às 9h", "fica salva, sem enviar; você ativa na tela Campanhas", "fica agendada para sexta, 23/10, às 9h, mas desativada; ela só sai depois que você ativar na aba Agendadas", "fica salvo, sem enviar", "ativo: dispara para cada contato que chegar a esta etapa, 1 dia depois do anterior".

Para a conta toda, repita o número de contatos na pergunta final ("Posso criar a campanha para cerca de 3.200 contatos?"; com envio, "Posso enviar agora a campanha para cerca de 3.200 contatos?").

## Passo 6, criar

- Tipos 1 e 2: `create_email` com `type: broadcast`, `subject`, `body`, `enabled` conforme o passo 4 (no tipo 2, sempre `false`), no tipo 2 `machineId` e, se o cliente pediu para agendar, `settings.broadcast.sendAt` com a data e a hora no fuso `-03:00`.
- Tipo 3: `create_email` com `type: sequence`, `machineId`, `sequenceId`, `subject`, `body`, `enabled` conforme o passo 4 e `settings.sequence` do passo 2. Ou `update_email` no nível 1 de projeto novo, como descrito no passo 2.
- Não envie `editor`: a LeadLovers escolhe o editor do e-mail.
- Design com IA: envie também `json` (veja `references/conteudo-com-ia.md`).

## Resultado

Diga o que a resposta da tool confirmou, nunca o que foi pedido. Leia o `enabled` da resposta e, na campanha para todos, também o `status`, o `totalLeads` e o `sendAt`:

- "Pronto: a campanha vai sair agora para 3.180 contatos." (`status: scheduled`, sem `sendAt`)
- "Pronto: a campanha sai sozinha na sexta, 23/10, às 9h, para 3.180 contatos." (`status: scheduled`, com `sendAt`)
- "Criei o e-mail automático e ele está ativo: dispara para cada contato que chegar a esta etapa."
- "Criei o e-mail automático salvo, sem enviar. Quando quiser, é só pedir: ative o e-mail Boas-vindas."
- "Criei a campanha desativada. Para enviar, ative na tela Campanhas, aba Agendadas, da LeadLovers."
- "Criei a campanha para sexta, 23/10, às 9h, mas desativada: ela só sai depois que você ativar na tela Campanhas, aba Agendadas, da LeadLovers."

Se a resposta trouxer o link do editor (`editorUrl`), ofereça em seguida: "Quer ajustar textos e imagens você mesmo? Abra no editor: <link>", com o link como veio. Sem ele, não fale do editor.

## Ajustar, ativar ou pausar depois

1. Encontre o e-mail pelo que o cliente diz (projeto, sequência, posição ou assunto) com `get_machines`, `get_sequences` e `get_emails`, e leia-o com `get_email`, que traz também o `hash` do HTML. Confirme com o cliente qual é ("o e-mail Boas-vindas, primeiro da Sequência Inicial do projeto Curso de Confeitaria").
2. Envie em `update_email` só o que muda:
   - assunto: `subject`;
   - conteúdo: `body` inteiro, partindo do HTML que `get_email` trouxe, com só o trecho pedido alterado e as tags `*|...|*`, links e imagens intactos, mais `baseHash` com o `hash` que `get_email` trouxe (ou o da última gravação), para não apagar uma mudança que o cliente fez no editor enquanto isso;
   - ativar ou pausar (só e-mail automático): `enabled: true` ou `false`;
   - intervalo, data fixa, janela ou dias da semana (só e-mail automático): `sequence`, com só o que muda (`sendAfter` com o intervalo inteiro depois do e-mail anterior; ou `sendAt` para uma data fixa; `startTime`/`endTime`; `weekdays`). Leia antes o agendamento atual (`settings.sequence` no `get_email`, ou o `get_emails`, que traz o `sequenceDay` de cada nível) e mostre ao cliente o antes e o depois em palavras, com o dia da sequência ("hoje sai 0 dias depois do anterior, no dia 0; passa a sair 3 dias depois, no dia 3"), e quantos contatos esperam no nível (`waitingLeads`): eles têm a data do próximo envio refeita. Mudar um intervalo muda o dia dos e-mails seguintes também: diga isso.
3. Ativar ou pausar passa pela mesma confirmação do passo 5. Se o e-mail já está ativo e o conteúdo vai mudar, avise que a mudança vale para os próximos envios.
4. Campanha já enviada (`EMAIL_ALREADY_SENT`) ou agendada (`EMAIL_SCHEDULED`) não pode ser editada: explique e não insista. Para corrigir uma campanha enviada, ofereça criar outra. Ativar ou pausar campanha é feito na tela Campanhas da LeadLovers.
5. Filtros e posição não mudam por aqui; diga que isso é feito na LeadLovers. O agendamento de uma campanha também (`EMAIL_NOT_SEQUENCE`).

## Consultar campanhas

Para dizer quais campanhas estão agendadas, enviadas ou desativadas, use `get_broadcasts` (sem projeto, as campanhas para todos; com o projeto, as dele). Para o resultado de uma campanha (envios, aberturas, cliques, descadastros, erros) ou para quem ela foi, use `get_broadcast`. Fale em assunto, data e números, nunca em ids.

## Erros

Traduza e dê o próximo passo; nunca crie outro e-mail no lugar de um recusado.

| Código ou situação | O que dizer |
| --- | --- |
| `CAMPAIGN_SEND_NOT_ENABLED` | O envio de campanha por aqui ainda não foi liberado para a conta, e nada foi criado; pergunte se pode criar desativada (com a mesma data, se agendada) e explique que ela é ativada na tela Campanhas, aba Agendadas |
| `BROADCAST_SEND_NOT_SUPPORTED` | Campanha de projeto não pode ser ativada por aqui; pergunte se pode criar desativada e explique onde ativar |
| `SENDER_REQUIRED` | A conta não tem uma conta de disparo de e-mail ativa; ofereça salvar sem enviar e explique que a conta de disparo é configurada na LeadLovers |
| `ACCOUNT_SUSPENDED` | A conta está suspensa por pagamento em atraso, então nenhum e-mail pode ser ativado; ofereça salvar sem enviar |
| `EMAIL_BODY_REQUIRED` | E-mail sem conteúdo não pode ser ativado; escreva o conteúdo antes |
| `EMAIL_CUSTOM_FIELD_NOT_SUPPORTED` | O conteúdo usa campo personalizado que o envio automático não preenche; ofereça tirar o campo ou salvar e incluí-lo no editor da LeadLovers |
| `EMAIL_VARIANT_NOT_SUPPORTED` | O e-mail tem teste A/B ou reenvio e só pode ser editado no editor da LeadLovers |
| `EMAIL_NOT_SEQUENCE` | Só e-mail automático tem intervalo; a data de uma campanha muda na LeadLovers. Nada foi gravado |
| `EMAIL_SEND_WINDOW_INVALID` | A janela ficaria com o fim antes do início; pergunte o início e o fim juntos. Nada foi gravado |
| `CONTENT_CHANGED` | Nada foi gravado: "O e-mail mudou desde que eu li; posso ler de novo antes de alterar?". Com o sim, leia de novo com `get_email`, refaça a mudança sobre o HTML novo e grave com o `hash` novo; nunca grave sem `baseHash` para forçar |
| Sessão do design com IA expirada | O rascunho vale por 1 hora; ofereça criar o design de novo |
| Conexão sem permissão de escrita | A conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo |
| LeadLovers recusou a conexão (401) | A conexão expirou ou foi revogada; reconecte a LeadLovers no assistente |
