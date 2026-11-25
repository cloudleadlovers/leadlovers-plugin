---
name: organizar-crm
description: Monta e organiza o CRM da LeadLovers com o cliente, com pipelines, etapas e negociações dos contatos. Use quando o cliente falar em CRM, pipeline, etapas de venda ou negociações e quiser criar um pipeline, criar, renomear, reordenar ou remover etapas, abrir uma negociação para um contato, mudar o valor de uma negociação, mover negociações de etapa, ou remover negociações ou um pipeline. Acha tudo pelo nome, mostra o que muda e quantas negociações, de qual etapa para qual, e só então muda.
metadata:
  version: "1.0"
---

# Organizar o CRM na LeadLovers

## Objetivo

Fazer no CRM o que o cliente pediu, no pipeline, na etapa e na negociação certos: achar pelo nome, mostrar o que muda e para quantas negociações, esperar o "sim" e só então mudar, relatando o que a resposta da tool confirmou.

## Quando não usar

- Marcar contatos com tag, importar uma lista, colocar contatos num projeto ou corrigir os dados de um contato: use `organizar-contatos`.
- Enviar e-mail para os contatos de uma etapa: use `criar-email`.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`: "pipeline", "etapa", "negociação", "contato". Se o cliente disser "quadro", "coluna" ou "card", entenda pipeline, etapa e negociação.
- "Funil" sozinho é ambíguo: pode ser acompanhar negociações por etapa (o pipeline daqui) ou captar contatos com projeto, página e e-mails (`montar-funil`). Se o pedido não deixar claro, pergunte qual dos dois antes de criar qualquer coisa.
- Nunca mostre id, nome de tool, nome de campo ou JSON. Pipeline e etapa são escolhidos pelo nome; negociação, pelo nome ou e-mail do contato.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## Encontrar

1. **Pipeline:** `get_pipelines` lista todos, com as etapas na ordem do board e quantas negociações cada etapa tem (`dealsCount`). Ache pelo nome que o cliente disse; com mais de um parecido, ofereça as opções. A conta sem pipeline: ofereça criar um.
2. **Etapa:** vem dentro do pipeline. Antes de mudar etapas, leia de novo com `get_pipeline`, porque outra pessoa pode ter mudado no app.
3. **Negociação de um contato:** `get_pipeline_deals` com `search` (parte do nome ou do e-mail). Num pipeline grande, filtre também pela etapa (`stageId`).
4. **Contato para uma negociação nova:** passe o e-mail dele direto, ou ache com `get_leads` (filtro de e-mail ou telefone) e use o `id` do registro. Contato que ainda não existe na conta é cadastrado antes, com `create_lead`, com o aceite do cliente.

## Criar um pipeline

1. Pergunte o nome e as etapas. Se o cliente não souber, sugira etapas do negócio dele, de 4 a 6, da esquerda para a direita (por exemplo "Novo contato, Primeira conversa, Proposta enviada, Negociando, Fechado"). São de 1 a 20 etapas, com nomes diferentes entre si.
2. **Confirme** o nome e as etapas em ordem: "Vou criar o pipeline Vendas com as etapas Novo contato, Proposta enviada e Fechado. Posso criar?". Espere o "sim".
3. `create_pipeline`. Diga que ele já aparece no CRM da LeadLovers, sem negociações.
4. Ações automáticas da etapa, meta e responsáveis são configurados no app; diga isso se o cliente pedir.

## Mudar as etapas

- **Criar:** `create_pipeline_stage` com o nome e a posição (1 é a primeira, à esquerda; sem posição, vai para o fim). Confirme onde ela entra.
- **Renomear:** `update_pipeline_stage`. As negociações da etapa não mudam.
- **Reordenar:** leia as etapas com `get_pipeline`, mostre a ordem nova, espere o "sim" e chame `reorder_pipeline_stages` com todas as etapas, cada uma uma vez. As negociações ficam nas etapas delas.
- **Remover:** a etapa sai **com todas as negociações dela**, sem aviso por e-mail. Leia `dealsCount` com `get_pipeline` e diga quantas vão junto: "A etapa Proposta tem 12 negociações, e elas são removidas junto. Quer mover para outra etapa antes?". Se quiser mover, faça como em "Mover negociações" (a etapa inteira) e só depois remova. Espere o "sim" para `delete_pipeline_stage`.

## Negociações

Quantas negociações um contato pode ter num pipeline é a regra de repetição do pipeline, configurada no CRM da LeadLovers: na maioria ele pode repetir; alguns pipelines aceitam só uma por etapa ou só uma no pipeline.

### Abrir uma negociação

1. Combine o contato, o pipeline, a etapa e o valor (sem valor, vale o padrão da etapa).
2. Procure as negociações do contato nesse pipeline com `get_pipeline_deals` (pelo e-mail dele). Se ele já tem uma, diga em qual etapa e pergunte se é uma venda nova para o mesmo contato (abrir outra negociação) ou se é a mesma, que deve mudar de etapa (siga em "Mover negociações"): "A Maria já tem uma negociação no Vendas, na etapa Proposta. É uma compra nova dela, ou quer mover essa para Fechado?".
3. Confirme: "Vou abrir uma negociação da Maria no pipeline Vendas, etapa Novo contato, valor R$ 1.500. Posso?". Avise que, se a etapa tiver uma ação automática no app (colocar o contato num projeto, pontuar, tags, mandar para outro pipeline), ela roda para esse contato.
4. `create_deal`. Se a regra do pipeline não deixar (`CRM_DEAL_DUPLICATED`), diga em qual etapa está a negociação que ele já tem e ofereça mover ou mudar o valor dela; a regra se muda no CRM da LeadLovers.

### Mudar o valor

Ache a negociação, confirme o valor novo e chame `update_deal`. Só o valor muda.

### Mover negociações

1. Ache a etapa de origem e a de destino, do mesmo pipeline. Mover para outro pipeline não é possível por aqui.
2. Decida o que vai:
   - **Algumas negociações:** junte os ids com `get_pipeline_deals` (até 1000 por vez) e mova com `move_deals` e `dealIds`. Saem os avisos por e-mail da etapa e da negociação, como ao arrastar no app.
   - **A etapa inteira:** `move_deals` com `fromStageId`, sem limite e sem aviso por e-mail.
3. **Confirme** quantas, de qual etapa para qual: "Vou mover as 40 negociações da etapa Proposta para Fechado. Se a etapa Fechado tiver ação automática, ela roda para cada contato. Posso?". Espere o "sim".
4. Relate pela resposta: quantas estão na etapa de destino, quais não foram movidas (`failed`: `CRM_DEAL_NOT_FOUND` quando não são negociações ativas desse pipeline, `CRM_DEAL_DUPLICATED` quando o pipeline aceita uma negociação por contato em cada etapa e a etapa de destino já tem esse contato) e, se sobrou alguma na origem, que vai continuar. Quando a resposta pedir para chamar de novo, chame `move_deals` com os mesmos dados, sem perguntar outra vez: é a continuação do que o cliente já aprovou.

### Remover negociações

Só a pedido explícito. Mostre quantas e de quais etapas (os nomes dos contatos, quando forem poucas), espere o "sim" e chame `delete_deals` com até 1000 por vez. Os contatos continuam na conta. Relate removidas e as que não foram (`failed`).

## Remover um pipeline

Só a pedido explícito. Leia com `get_pipeline`, some as negociações das etapas e confirme: "O pipeline Vendas tem 3 etapas e 57 negociações, e tudo isso sai junto. Os contatos continuam na conta. Posso remover?". Espere o "sim" para `delete_pipeline`.

## O que não dá por aqui

Marcar negociação como ganha ou perdida, responsáveis, lembretes, metas, grupos de pipelines e ações automáticas das etapas são feitos no CRM da LeadLovers. Diga isso em uma frase e siga com o que dá.

## Erros

- `CRM_PIPELINE_NOT_FOUND` ou `CRM_STAGE_NOT_FOUND`: o pipeline ou a etapa foi removido ou mudou; liste de novo e pergunte.
- `CRM_LEAD_NOT_FOUND`: o contato não existe na conta ou foi excluído; procure com `get_leads` ou ofereça cadastrar.
- `CRM_DEAL_DUPLICATED`: a regra do pipeline não deixa o contato ter outra negociação ali (uma por etapa ou uma no pipeline); ofereça mover ou mudar o valor da que existe.
- `CRM_DEAL_NOT_FOUND`: a negociação foi removida; liste de novo.
- `CRM_STAGES_INVALID`: nome de etapa repetido no pipeline, ou a ordem nova não trouxe todas as etapas; ajuste com o cliente e tente de novo.
- `TIMEOUT` ou `TRANSACTION_FAILED`: a LeadLovers não terminou; tentar de novo com os mesmos dados é seguro.
- Conexão sem permissão de escrita: a conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo.
- LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; reconecte a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase, diga o próximo passo e não repita uma remoção sem listar de novo o que sobrou.
