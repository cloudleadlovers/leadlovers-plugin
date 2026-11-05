---
name: organizar-contatos
description: Encontra, importa, marca, move e ajusta contatos (leads) na LeadLovers com o cliente, com confirmação antes de mudar muitos de uma vez. Use quando o cliente quiser achar contatos, saber quantos têm uma característica, importar uma lista de contatos colada, uma planilha ou um CSV, marcar ou etiquetar com tag, tirar uma tag, colocar contatos num projeto ou numa sequência, tirar de um projeto, cadastrar ou corrigir um contato, ou criar e preencher campos personalizados. Traduz o critério do cliente (quem comprou, quem se cadastrou em outubro, quem não tem tag) num filtro, mostra o total e alguns exemplos, confirma, executa em lotes e acompanha até o fim.
metadata:
  version: "1.0"
---

# Organizar contatos na LeadLovers

## Objetivo

Fazer com os contatos o que o cliente pediu, nos contatos certos: encontrar pelo critério dele, mostrar quantos são, confirmar e só então mudar, acompanhando cada mudança até terminar.

## Quando não usar

- Enviar e-mail para um grupo de contatos: use `criar-email`.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`: "contato", "tag", "projeto", "campo personalizado".
- Nunca mostre id, nome de tool, nome de campo ou JSON. Tags, projetos e campos são escolhidos pelo nome.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## Encontrar contatos

1. Entenda o critério. Se ele for vago ("meus melhores clientes"), pergunte o que define isso com opções (uma tag, um projeto, a data de cadastro).
2. Traduza para os filtros de `get_leads` com `references/filtros.md`. Resolva os nomes antes: tags com `get_tags`, projetos com `get_machines`, sequências com `get_sequences`, campos personalizados com `get_custom_fields`.
3. Chame `get_leads` com página 1 e `pageSize: "10"` e mostre o total e até 5 exemplos (nome e e-mail). Se o total parecer errado para o cliente, revise o filtro com ele antes de seguir.

O id de cada contato é o campo `id` do registro de `get_leads`; é ele que as outras tools recebem.

## Importar uma lista

Lista de contatos colada, CSV ou planilha: siga `references/importacao.md` do começo ao fim. Nunca cadastre uma lista um a um com `create_lead`.

## Mudar muitos contatos de uma vez

Valem para marcar com tag (`add_tags_to_leads`), tirar tag (`remove_tags_from_leads`), colocar num projeto (`add_leads_to_machine`) e tirar de projeto ou excluir (`delete_lead`).

1. **Confirme** com o total e a ação em palavras: "Vou marcar com a tag comprou os 230 contatos do projeto Curso de Confeitaria. Posso seguir?". Espere o "sim".
2. **Junte todos os ids antes de mudar qualquer um**, percorrendo as páginas de `get_leads` com `pageSize: "100"`. A ação muda o resultado da busca (quem ganhou a tag deixa de aparecer em "sem a tag"), então mudar enquanto pagina pula contatos. Acima de 2.000 contatos, avise que vai levar várias etapas e pergunte se quer seguir.
3. **Execute em lotes de até 500 ids.** Cada chamada devolve um `actionId`: a mudança entrou numa fila e ainda não terminou.
4. **Acompanhe** cada `actionId` com `get_bulk_action_status` até concluir.
5. **Relate** feitos, ignorados e falhas, somando os lotes: "Marquei 228 contatos. 2 já tinham a tag." Nunca diga que terminou antes de `get_bulk_action_status` confirmar.

## Tags

- Chame `get_tags` antes. Se o nome pedido parecer com uma tag existente (maiúsculas, plural, acento: "Comprou" e "compraram"), ofereça a existente.
- Tag nova só com o aceite do cliente. `add_tags_to_leads` e `create_lead` criam sozinhas a tag que recebem pelo nome, então confira o nome antes de passar. Na importação, a coluna de tags também cria as que faltam (`references/importacao.md`).
- Criar uma tag vazia, sem aplicar a ninguém: `create_tag`. Nome igual a uma existente (ignorando maiúsculas) responde `NAME_EXISTS`.
- Renomear ou excluir tag não é possível por aqui; diga que isso é feito na LeadLovers.

## Colocar em projeto

- `add_leads_to_machine` com o projeto, a sequência e a posição de entrada. Pergunte a posição: começar do primeiro e-mail (nível 1), de outra etapa, ou só entrar no projeto sem começar a sequência (posição -1, para segmentar sem disparar nada).
- Antes de confirmar, leia com `get_emails` e `get_email` o e-mail da posição de entrada. Se ele estiver ativo, avise: "esses contatos vão começar a receber os e-mails automáticos da sequência".
- Contatos que já estão no projeto, ou sem e-mail num projeto de e-mail, são ignorados; diga isso no resultado.
- `ACTIVE_COPY_BETWEEN_MACHINES`: uma mudança anterior para o mesmo projeto ainda está na fila; acompanhe a anterior até terminar e tente de novo.

## Tirar de projeto ou excluir

Só a pedido explícito. Explique a diferença antes de confirmar:

- **Tirar do projeto:** `delete_lead` com `scope: "machines"` e os projetos em `machineIds`. O contato sai de todas as sequências desses projetos e continua na conta. Não dá para tirar de uma sequência só.
- **Excluir da conta:** `delete_lead` com `scope: "account"`. O contato some da conta e de todos os projetos.

Confirme dizendo o efeito e o total, e acompanhe como nas mudanças em massa.

## Um contato só

- **Cadastrar:** `create_lead` com e-mail ou telefone (telefone só com números, com DDI e DDD, como 5511987654321; aniversário como DD/MM/AAAA). Pode já colocar num projeto passando projeto, sequência e posição juntos; essa parte roda em segundo plano e devolve um `actionId`. `EMAIL_EXISTS` ou `PHONE_EXISTS`: o contato já existe; encontre-o com `get_leads` e ofereça atualizar.
- **Corrigir dados:** encontre o id com `get_leads` (filtro de e-mail ou telefone) e chame `update_lead` só com os campos que mudam. Enviar vazio (null) apaga o dado: confirme antes de apagar.
- **Ver tags e campos de um contato:** `get_lead_tags` e `get_lead_dynamic_fields`.

## Campos personalizados

- Liste com `get_custom_fields` e reaproveite um existente quando servir.
- Para criar, pergunte o nome e o tipo, com estas opções: texto, texto grande, data, telefone, e-mail, CPF, CNPJ, CPF ou CNPJ, lista de seleção, múltipla escolha, escolha única. Chame `create_custom_field` com `name`, `label` (o nome que o cliente vê), `tag` (identificador sem espaço nem acento, único na conta) e `typeId` (1 texto, 2 texto grande, 3 data, 4 telefone, 5 seleção, 6 múltipla escolha, 7 escolha única, 8 e-mail, 9 CPF, 10 CNPJ, 11 CPF ou CNPJ). Tipos de escolha (5, 6 e 7) levam as opções em `values`, com `score: 0` se o cliente não falar em pontuação.
- `NAME_EXISTS` ou `CUSTOM_FIELD_TAG_EXISTS`: já existe um campo com esse nome ou identificador; ofereça usar o existente.
- Preencher para um contato: `update_lead` com `dynamicFields`. Um valor pode ser trocado, mas não apagado.
- Excluir campo (`delete_custom_field`): só a pedido explícito, confirmando que os valores guardados nesse campo deixam de aparecer.

## Pontuação

A pontuação aparece nos filtros e pode vir numa coluna da importação (`references/importacao.md`). Fora isso, não é definida por aqui; diga que ajustes de pontuação são feitos na LeadLovers.

## Erros

- Erros da importação: `references/importacao.md`, passos 7 a 9 e "Regras da LeadLovers".
- `LEAD_NOT_FOUND`: o contato foi excluído ou não é desta conta; busque de novo.
- `TAG_NOT_FOUND`, `MACHINE_NOT_FOUND`, `SEQUENCE_NOT_FOUND`: a tag, o projeto ou a sequência não existe mais; liste de novo e pergunte.
- Conexão sem permissão de escrita: a conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo.
- LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; reconecte a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase, diga o próximo passo e não repita uma ação em massa sem conferir com `get_bulk_action_status` o que já foi feito.
