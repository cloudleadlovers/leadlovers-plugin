---
name: criar-automacao
description: Monta com o cliente uma automação da LeadLovers, o fluxo que envia mensagens em canais intercalados, um depois do outro e com esperas entre eles (por exemplo um e-mail na entrada, um WhatsApp no dia seguinte, um SMS dois dias depois e outro e-mail). Use quando o cliente quiser criar, ajustar, publicar, pausar ou entender uma automação, falar em jornada, régua de relacionamento, cadência ou follow-up em vários canais, ou pedir algo como "manda um e-mail, depois um WhatsApp e depois um SMS". Desenha o fluxo em palavras simples, confirma, monta e só publica com o "sim".
metadata:
  version: "1.3"
---

# Criar uma automação na LeadLovers

## Objetivo

Sair do pedido do cliente com uma automação publicada (ou salva, se ele preferir) que envia as mensagens certas, nos canais certos e na hora combinada: desenhar o fluxo com ele em palavras simples, montar tudo de uma vez, conferir o que falta e só publicar depois do "sim", relatando o que a resposta da tool confirmou.

## Quando não usar

- Só e-mails numa sequência de um projeto de e-mail que já existe, ou uma campanha avulsa: use `criar-email`.
- Mensagens automáticas, disparo ou link num projeto de WhatsApp: use `criar-mensagem-whatsapp`.
- CRM, pipeline ou negociações: use `organizar-crm`. Colocar contatos numa automação já publicada: use `organizar-contatos`.
- Projeto do editor de automação antigo (`isAutomation: true`): ele não é editado por aqui; explique em uma frase e ofereça criar uma automação nova.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`: "automação", "passo" (cada mensagem do fluxo; no editor do app é a "caixinha"), "entrada", "publicar", "rascunho", "contato". Se o cliente disser "caixinha", "bloco" ou "etapa" da automação, entenda passo.
- Fale do fluxo como ele acontece para o contato: "assim que entrar", "1 dia depois", "às 10h". Nunca mostre id, chave de passo, nome de tool, nome de campo ou JSON.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## O que uma automação faz

Uma automação é um projeto (no app, "Automação") com um fluxo de passos que o contato percorre em ordem. Cada passo envia uma mensagem num canal: **e-mail, SMS, WhatsApp, Instagram ou ligação de voz**. Cada passo espera um tempo contado a partir do passo anterior (o primeiro conta da entrada), e pode ter horário, dias da semana ou data fixa. Assim os canais se intercalam no mesmo fluxo.

Além das mensagens, ela faz:

- **Entradas:** o que coloca o contato no primeiro passo (ou num passo escolhido): uma página de captura, formulário ou pop-up do próprio projeto; uma palavra-chave que o contato manda no WhatsApp ou no Instagram da automação; ou um link de WhatsApp que abre a conversa com um texto pronto. Contatos também entram quando são colocados ou importados no projeto, ou vindos de outra automação.
- **Eventos:** quando o contato abre ou clica num e-mail, ou atende, dá ocupado ou cai na caixa postal numa ligação, aplicar ou tirar tag, pontuar, copiar para outro projeto, abrir negociação numa etapa do CRM e, se quiser, levar o contato para outro projeto.
- **Ao terminar a automação:** ações para todo contato que chega ao fim (tag, pontuação, cópia, CRM), na hora ou depois de uma espera, e se quiser só para quem não abriu nenhum e-mail.
- **Mandar para outro projeto:** um passo final que leva o contato para outro projeto, sequência e nível.

Tudo que se muda fica num **rascunho**: os contatos só recebem o que mudou depois de **publicar**.

## Desenhar o fluxo

1. Entenda o objetivo, quem entra e o que deve acontecer: "Quem vai entrar nessa automação: quem se cadastrar numa página, contatos que você já tem, ou os dois?".
2. Proponha o fluxo numa lista numerada, um passo por linha, com canal, quando e o que diz em uma frase. Exemplo para boas-vindas de um curso:
   1. E-mail assim que o contato entra: boas-vindas e o link da primeira aula.
   2. WhatsApp 1 dia depois, às 10h: "Oi, Ana! Já viu a primeira aula?".
   3. SMS 2 dias depois: lembrete curto da aula ao vivo.
   4. E-mail 3 dias depois: o convite para a turma.
3. Recomendações ao propor: intercale os canais, um passo por dia no máximo para não cansar o contato, WhatsApp e SMS em horário comercial (por exemplo das 9h às 18h, de segunda a sexta), SMS curto e direto. Os detalhes e limites de cada canal estão em `references/canais.md`.
4. Pergunte o que as mensagens precisam e você não sabe (link da aula, produto, preço, link de compra, data do evento). Nunca escreva texto entre colchetes nem link de exemplo: sem o dado, escreva a mensagem sem ele ou deixe a automação salva como rascunho até o cliente mandar, e diga isso.
5. Pergunte o nome da automação, sugerindo um ("Que nome damos a ela? Sugiro Boas-vindas Curso."). Use o nome que o cliente der.
6. **Confirme** o fluxo inteiro e o nome e espere o "sim" antes de criar.

## Montar

1. `create_automation` com o nome. Ela nasce como rascunho com um passo vazio, que o primeiro passo preenche. Se a resposta for `AUTOMATION_NOT_ENABLED`, a automação nova ainda não está liberada para essa conta: diga isso em uma frase e ofereça o caminho de hoje (projeto de e-mail com sequência, em `criar-email`, ou de WhatsApp, em `criar-mensagem-whatsapp`).
2. Canais: `get_automation` mostra em `availableChannels` o que o plano da conta envia. Passo num canal que não está liberado não entra; ajuste o fluxo com o cliente. Liberado não quer dizer conectado: só diga que o WhatsApp ou o Instagram vão enviar depois de conferir o número ou a conta (passo 3).
3. **WhatsApp ou Instagram no fluxo:** logo depois de criar, chame `get_automation_channel` com o canal antes de montar esse passo; não pergunte ao cliente se ele tem número ou conta, a tool diz. Ela mostra o número ou a conta ligados e os que dá para escolher. Sem nenhum para escolher, diga em uma frase que a conta ainda não tem número de WhatsApp (ou conta de Instagram) para a automação e que ele é conectado no app, e ofereça seguir sem esse passo ou deixar salva. Com opções, ofereça as da conta pelo nome ou número e ligue com `set_automation_channel_account`. Se o número estiver em outro projeto, a resposta diz qual e quantos contatos ele tem: explique que esse projeto fica sem WhatsApp, espere o "sim" e chame de novo com `confirmMove`. Número oficial envia só template aprovado para quem não escreveu nas últimas 24 horas (veja `references/canais.md`).
4. **E-mail:** o remetente padrão vale se o cliente não escolher; `get_email_senders` lista os da conta. Escreva o assunto e o HTML seguindo `references/canais.md`. Anexo e convite com data (aula ao vivo, live, reunião) também vão no passo, como diz `references/canais.md`; peça o arquivo, o dia e o horário ao cliente, nunca invente.
5. `add_automation_boxes` com todos os passos na ordem, de uma vez (até 20 por chamada). Para levar o contato a outro projeto no fim, o último passo é a saída para outro projeto.
6. **Entradas** (`add_automation_entry`; todas só funcionam depois de publicar):
   - Página, formulário ou pop-up: `get_automation_captures` lista os do projeto da automação. Para uma página nova, crie no projeto da automação (`create_page` com o `machineId` dela, seguindo `criar-pagina` se puder carregá-la) e ligue.
   - Palavra-chave: quem manda a mensagem no WhatsApp ou no Instagram ligados à automação entra. Pergunte a palavra e explique a regra em uma frase: do jeito que for escrita, a mensagem tem de ser igual (maiúscula não importa); com `[*]` antes e depois, vale qualquer mensagem que tenha a palavra. Pode dar tags a quem entra e valer só num horário. Precisa do número ou da conta ligados (passo 3).
   - Link de WhatsApp: um link que abre o WhatsApp da automação com um texto pronto; quem envia o texto entra. Peça o nome do link e o texto (sem `[ ] & # + %`). O endereço sai na publicação: entregue ao cliente para ele divulgar. Sem número de WhatsApp na automação, o link fica esperando um.
   - Cada palavra ou texto de link vale para uma entrada só no projeto.
7. **Eventos e "Ao terminar", se o cliente pedir:** diga o que acontece, em qual evento e em qual passo ("Quem clicar no e-mail Oferta ganha a tag clicou-oferta. Posso salvar?"), espere o "sim" e só então chame `set_automation_event` (só em passos de e-mail e de voz) ou `set_automation_end_actions`. Tags vêm de `get_tags` (ou `create_tag`), etapas do CRM de `get_pipelines`, projetos de `get_machines_structure`.

## Conferir e publicar

1. `check_automation_publish`. Cada erro vem com o passo e o que fazer: corrija (`update_automation_box`, `set_automation_channel_account`, `add_automation_entry`) e confira de novo. Avisos não impedem; conte ao cliente os que mudam algo para ele, em palavras simples.
2. **Confirme** antes de publicar: o que vai ser enviado, em quais canais e quando, quem recebe (quem entrar pelas páginas, os contatos que já estão nela) e que a automação começa a enviar ao publicar. "Vou publicar a automação Boas-vindas: e-mail na entrada, WhatsApp 1 dia depois e SMS 2 dias depois, para quem se cadastrar na página Aula grátis. Posso publicar?". Espere o "sim".
3. `publish_automation` com a `revision` do `get_automation` que embasou a confirmação, para não publicar um rascunho que mudou depois do "sim". Se a resposta for `AUTOMATION_DRAFT_CONFLICT`, nada foi publicado: leia de novo, conte ao cliente o que mudou e confirme outra vez. Relate pela resposta: publicada, com quantos passos enviando, e os avisos. Com link de WhatsApp, entregue o endereço que a resposta traz (ou diga que ele espera um número). Se o cliente preferir só deixar pronta, não publique e diga que está salva como rascunho, sem enviar nada.
4. Depois de publicar, contatos que o cliente já tem entram pelo `organizar-contatos`, no projeto da automação, na sequência dela e no nível do primeiro passo (o nível publicado que `get_automation` mostra no passo).

## Ajustar uma automação que existe

1. Ache pelo nome com `get_automations` e leia com `get_automation`. Para mudar um e-mail, leia aquele passo inteiro (`boxKey`) e devolva o HTML todo.
2. Mostre a mudança e espere o "sim":
   - Mudar texto, assunto, espera, horário ou canal de um passo: `update_automation_box`.
   - Incluir passos no meio: `add_automation_boxes` depois do passo escolhido; os seguintes descem.
   - Trocar a ordem: `move_automation_box`. Avise que a espera de cada passo conta do anterior, então muda quando o passo movido e o seguinte saem.
   - Tirar um passo: `remove_automation_box`. Se ele já estava publicado e tem contatos esperando nele, a publicação pergunta para onde eles vão: ofereça o passo seguinte ou o fim e passe em `leadDestinations`.
   - Ação num evento (abriu, clicou, atendeu) ou no "Ao terminar": `set_automation_event` ou `set_automation_end_actions`, também só depois do "sim".
3. Tudo vai para o rascunho. Diga isso e ofereça conferir e publicar como em "Conferir e publicar".
4. Desfazer: `get_automation_versions` mostra as publicações; `restore_automation_version` volta uma delas como rascunho, perdendo o que não foi publicado. Confirme antes.

## Pausar, retomar e arquivar

Mostre o que para ou volta a ser enviado e para quantos contatos (`get_automation_metrics`, contatos na automação agora) e espere o "sim" para `set_automation_status`. Pausar segura todo envio e os contatos esperam onde estão; os eventos de e-mails já enviados continuam rodando. Arquivar pausa e tira da lista de projetos. Publicar uma automação pausada não a retoma.

Arquivar libera o nome: outra automação pode passar a usá-lo, e aí desarquivar responde `NAME_EXISTS` e a arquivada continua arquivada. Uma automação arquivada não é renomeada, então ofereça renomear a outra (a que está ativa) e desarquivar depois.

## Renomear

Confirme o nome novo com o cliente e use `rename_automation`. O nome muda na lista de projetos e no editor na hora, sem publicar, e nada mais muda: passos, contatos e o que é enviado continuam iguais. Para renomear uma arquivada, desarquive antes.

## Resultados

`get_automation_metrics` traz, por passo, quantos contatos estão nele, envios, entregues (WhatsApp), aberturas, cliques, descadastros e erros, e quantos terminaram. Fale por passo, pelo nome e pela posição no fluxo, nunca pela chave.

## O que não dá por aqui, ainda

Botões, anexos e cartão de contato no WhatsApp; eventos de SMS, WhatsApp e Instagram; evento que pula para outro passo da mesma automação ou para o fim; entrada por comentário do Instagram; envio de teste. Diga isso em uma frase e siga com o que dá, sem prometer data.

## Erros

- `AUTOMATION_NOT_ENABLED`: a automação nova não está liberada para a conta; ofereça projeto de e-mail ou de WhatsApp.
- `NAME_EXISTS`: já existe uma automação ativa com esse nome (as arquivadas não contam); sugira outro ou ofereça ajustar a que existe. Ao desarquivar, quer dizer que outra pegou o nome enquanto esta estava arquivada: ofereça renomear a outra com `rename_automation` e desarquivar depois.
- `AUTOMATION_PUBLISH_BLOCKED`: nada foi publicado; a resposta lista o que falta por passo. Corrija e confira de novo.
- `ENTRY_KEYWORD_IN_USE`: a palavra ou o texto do link já é de outra entrada do projeto; peça outro.
- `AUTOMATION_DRAFT_CONFLICT`: alguém mudou a automação no app ao mesmo tempo; leia de novo e refaça a mudança.
- `AUTOMATION_ARCHIVED`: a automação está arquivada; ofereça desarquivar antes de mudar.
- `WHATSAPP_ACCOUNT_IN_USE`: o número está em outro projeto; explique e só mova com o "sim".
- `WHATSAPP_ACCOUNT_TEMPLATES_CONFLICT` ou `INSTAGRAM_ACCOUNT_CARDS_CONFLICT`: o número ou a conta nova não envia o que está publicado; troque esses passos e publique antes.
- Resposta que começa com "Nothing was saved": a mudança foi recusada antes de salvar e diz o motivo; ajuste com o cliente.
- Conexão sem permissão de escrita: a conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo.
- LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; reconecte a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase, diga o próximo passo e não diga que publicou ou enviou sem a resposta confirmar.
