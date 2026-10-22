# Do critério do cliente ao filtro de `get_leads`

Todos os filtros vão em `filters`. Ids de tag, projeto, sequência e campo personalizado vêm das tools de listagem; nunca peça ao cliente.

## Tags

| O cliente diz | Filtro |
| --- | --- |
| "com a tag X" ou "com X ou Y" | `tags.withSome: [X, Y]` (tem pelo menos uma) |
| "com X e Y" | `tags.withAll: [X, Y]` (tem todas) |
| "sem a tag X" ou "sem nenhuma de X e Y" | `tags.withoutAll: [X, Y]` (não tem nenhuma) |
| "sem ter as duas, X e Y" | `tags.withoutSome: [X, Y]` (falta pelo menos uma) |

"Quem comprou" quase sempre é uma tag ("comprou", "cliente"): procure em `get_tags` e confirme com o cliente qual é.

## Projeto, sequência e posição

| O cliente diz | Filtro |
| --- | --- |
| "do projeto X" | `machineIds: [X]` |
| "da sequência Y do projeto X" | `machineIds: [X]` e `sequenceId: Y` |
| "que estão no terceiro e-mail" | `machineIds`, `sequenceId` e `level: 3` |
| "que nunca estiveram num projeto de WhatsApp" | `advancedFilters.machines.notIn: "whatsapp"` |
| "que saíram de algum projeto de e-mail" | `advancedFilters.machines.removedFrom: "email"` |

`notIn` e `removedFrom` não podem ir juntos.

## Datas de cadastro

| O cliente diz | Filtro |
| --- | --- |
| "que se cadastraram em outubro" | `createdSince: "2026-10-01T00:00:00Z"` e `createdBefore: "2026-10-31T23:59:59Z"` (use o ano que o cliente quis) |
| "dos últimos 7 dias" | `createdSince` com a data de 7 dias atrás |

## Dados do contato

| O cliente diz | Filtro |
| --- | --- |
| "o contato maria@exemplo.com" | `staticFields.email: ["maria@exemplo.com"]` |
| "estes e-mails: ..." | `staticFields.email` com a lista |
| "pelo telefone" | `staticFields.phone` com os números |
| "chamados Maria" | `staticFields.name: "Maria"` (busca parcial) |
| "da cidade de Campinas", "de SP" | `staticFields.city`, `staticFields.state` |
| "que fazem aniversário em maio" | `staticFields.birthMonth: 5` |

## Entregabilidade

| O cliente diz | Filtro |
| --- | --- |
| "com e-mail válido" | `deliverability: "deliverable"` |
| "com e-mail inválido" | `deliverability: "undeliverable"` |
| "com e-mail arriscado" | `deliverability: "risky"` |

## Campos personalizados

Encontre o campo em `get_custom_fields` e use o `id` e o tipo dele:

- texto: `dynamicFields: [{ id, type, value: "texto procurado", optionsLogicalOp: "and" }]`;
- escolha: `dynamicFields: [{ id, type, options: ["Opção A", "Opção B"], optionsLogicalOp: "or" }]`, com `"or"` para qualquer uma e `"and"` para todas.

Vários campos ao mesmo tempo combinam com `advancedFilters.dynamicFields.logicalOperator` (`"and"` ou `"or"`).

## Origem (UTM)

"Quem veio do anúncio do Instagram": `parameters.utmSource`, `utmCampaign`, `utmMedium`, `utmContent` ou `utmTerm`, com o valor que o cliente usou nos links. Pergunte o valor se ele não souber.

## Limites

- Uma lista de e-mails ou telefones colada tem limite de itens; listas muito grandes são buscadas em partes.
- Pontuação (`score`) aparece como filtro (por exemplo `">=50"`), mas não é definida por aqui.
