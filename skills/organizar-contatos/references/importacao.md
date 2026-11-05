# Importar uma lista de contatos

Para quando o cliente traz vários contatos de uma vez: lista colada, CSV anexado ou células copiadas de uma planilha. Cadastrar um a um com `create_lead` nunca substitui a importação: passa por fora das regras antifraude da LeadLovers.

## 1. Receber a lista

- **Colada, CSV ou células copiadas:** siga para o passo 2.
- **Planilha (.xlsx, .xls, Google Planilhas):** peça para copiar as células, com a linha de títulos, e colar na conversa, ou para salvar como CSV e anexar.
- **Mais de 500 contatos:** por aqui cabem até 500 por vez. Oriente a importação pela tela de importação da LeadLovers, que aceita o arquivo inteiro. Nunca divida a lista em partes: importações seguidas ficam presas para revisão e podem bloquear as importações da conta (seção "Regras da LeadLovers").

## 2. Ler a lista

Chame `preview_lead_import` com o texto. Mostre as colunas com 2 exemplos de cada, numeradas a partir de 1 ("coluna 1, exemplos maria@gmail.com, joao@hotmail.com").

O texto que vai para `import_leads` tem de ser **idêntico** ao do preview, com o mesmo `hasHeader` e `delimiter`: o mapeamento aponta para as colunas pelo número. A única mudança permitida é tirar linhas repetidas (passo 7).

## 3. Combinar o que é cada coluna

Parta da sugestão do preview (`suggestedMapping`) e mostre em palavras: "coluna 1: e-mail; coluna 2: nome; coluna 3: telefone". Peça confirmação. A lista precisa de uma coluna de e-mail ou de telefone.

Coluna sem par, ofereça nesta ordem:

1. um campo personalizado que já existe (`get_custom_fields`);
2. criar um campo novo, como em "Campos personalizados" do `SKILL.md` (pergunte o nome e o tipo);
3. usar como tags;
4. usar como pontuação, se forem números inteiros;
5. ignorar a coluna.

Antes de confirmar, confira:

- **Tags:** compare os valores da coluna com `get_tags` e mostre quais tags ainda não existem. A importação cria todas, então só siga com o aceite do cliente; se ele não quiser criar, ignore a coluna.
- **Campo de escolha** (lista de seleção, múltipla escolha, escolha única): a LeadLovers grava o valor como veio, sem conferir as opções. Compare os exemplos com as opções do campo e avise quando não baterem.
- **Telefone sem código do país:** pergunte o país, recomendando Brasil. Passe `ddi` com 55 (Brasil), 57 (Colômbia) ou 1 (Estados Unidos). De outro país, os telefones precisam vir na lista já com o código.
- **Aniversário:** os exemplos precisam estar como DD/MM/AAAA.

## 4. Destino

Pergunte se os contatos ficam só na conta ou entram também num projeto. No projeto, resolva o projeto e a sequência pelo nome (`get_machines_structure`) e pergunte a posição:

- começar do primeiro e-mail (nível 1) ou de outra etapa (nível N);
- só entrar no projeto, sem começar a sequência (`-1`, recomendado quando o cliente só quer segmentar);
- depois do último e-mail (`end-of-funnel`): explique que, se a sequência tiver uma ação de final de funil, ela dispara para esses contatos.

Leia com `get_emails` e `get_email` o e-mail da posição escolhida. Se ele estiver ativo, avise: "esses contatos vão começar a receber os e-mails automáticos da sequência".

## 5. Contatos que já estão na conta

A LeadLovers reconhece o contato pelo e-mail ou pelo telefone. Pergunte o que fazer com quem já existe:

- **Atualizar** (recomendado, `mergeExisting: true`): o que vier preenchido na lista substitui o dado atual, célula vazia mantém o que já existe e as tags se somam às que ele tem.
- **Manter como está** (`mergeExisting: false`): nada muda nos dados dele.

Nos dois casos, quem já existe também entra no destino.

## 6. Confirmar

Chame `list_lead_imports` (página 1) para conferir as importações recentes da conta (seção "Regras da LeadLovers"). Depois, antes de `import_leads`, mostre um resumo: quantos contatos, o que é cada coluna, o destino e o que acontece com quem já existe. No mesmo resumo, avise quando valer:

- contatos que o cliente tinha removido da conta voltam a ficar ativos e, com destino, voltam a receber os e-mails;
- contatos descadastrados, com e-mail que voltou, marcados como spam ou na lista de bloqueio não são importados;
- os avisos da seção "Regras da LeadLovers".

Espere o "sim". Se o cliente mudar qualquer coisa depois do resumo (destino, colunas, quem já existe), mostre o resumo de novo com a mudança e espere outro "sim".

O nome da importação é você quem dá, sem perguntar, no `name` de `import_leads`: "Lista colada em DD/MM/AAAA (N contatos)", com a data de hoje e quantos contatos a lista tem, ou o nome do arquivo no lugar de "Lista colada". É por ele que a importação é achada depois.

## 7. Respostas de `import_leads`

- **Na fila** (`queued`): os contatos ainda não foram gravados. Siga para o passo 8.
- **Aguardando aprovação** (`awaiting-approval`): seção "Regras da LeadLovers".
- **Linhas repetidas** (`IMPORT_DUPLICATE_KEYS`): nada foi gravado. Mostre os e-mails e telefones repetidos com o número das linhas (a linha de títulos conta como 1) e pergunte quais ficam, recomendando a primeira de cada. Tire as outras linhas do texto e chame `import_leads` de novo, sem outro preview.
- **Plano sem vaga** (`PLAN_LEADS_LIMIT_REACHED`): nada foi gravado. Explique que o plano chegou ao limite de contatos e que é preciso remover contatos ou mudar de plano na LeadLovers.
- **Sem confirmação** (a resposta diz que a importação pode ter sido criada): chame `list_lead_imports` e procure, entre as mais recentes, uma com esse nome criada há instantes, antes de qualquer outra coisa. Achou: acompanhe aquela. Não achou: importe de novo. Nunca repita sem procurar, ou a lista entra duas vezes.

## 8. Acompanhar

Chame `get_lead_import` com o `importId` até o status ser `done`, `done-with-errors`, `cancelled` ou `rejected`, **no máximo 5 vezes**. Uma lista de 500 contatos costuma terminar em segundos. Se na quinta ainda estiver em andamento, diga que a importação segue sozinha e que o cliente pode perguntar depois como ela terminou. Nunca diga que terminou antes de `get_lead_import` confirmar.

## 9. Relatar

Resuma em palavras, só com o que aconteceu: criados, atualizados, reativados, sem mudança, ignorados, inválidos, com falha, no limite do plano (gravados, mas sem receber e-mails até o plano ter vaga) e não colocados no destino. Para as linhas com problema (`problemRows`), dê o motivo em linguagem simples e o número da linha: "linha 5: e-mail incompleto (ana@)".

- **Falha temporária** (linhas com `TRANSACTION_FAILED`): ofereça tentar de novo só essas linhas. Com o "sim", chame `retry_lead_import` e acompanhe como no passo 8.
- **Valor recusado** (`ROW_WRITE_FAILED`): a LeadLovers não aceita aquele valor; tentar de novo não adianta. Mostre a linha para o cliente corrigir.

## Importações anteriores

- **"Como foi minha importação?"**: `list_lead_imports` para achar pelo nome ou pela data, `get_lead_import` para o detalhe. Fale da importação pelo nome e pela data, nunca pelo número dela, nem ao avisar o que vai consultar. As linhas com problema ficam guardadas por 30 dias depois do fim; os totais ficam sempre.
- **Cancelar:** só a pedido, enquanto estiver aguardando aprovação, na fila ou em andamento (`cancel_lead_import`). Avise antes que o que já foi gravado fica na conta.
- **Desfazer uma importação que já terminou:** não dá. Diga isso e não ofereça excluir os contatos como forma de desfazer: a lista pode ter atualizado contatos que já existiam, e excluí-los apagaria quem estava na conta antes. Só exclua se o cliente pedir, por conta própria, contatos específicos, como em "Tirar de projeto ou excluir" do `SKILL.md`.

## Regras da LeadLovers

Valem para a conta inteira, pela conversa e pela tela, e a LeadLovers não abre exceção para a conversa.

- **Bloqueio por excesso de importações pequenas** (`IMPORT_BLOCKED`): mais de 2 importações de 50 a 100 contatos terminadas em 48 horas pausam as importações da conta até a LeadLovers revisar. Numa lista de 50 a 100 contatos, se `list_lead_imports` mostrar 2 nessa faixa terminadas nas últimas 48 horas, avise que esta pode pausar as importações da conta e pergunte se ele prefere esperar ou seguir. O histórico não mostra o que foi importado pelo importador antigo, então o aviso é uma estimativa. Se o bloqueio vier: explique que as importações da conta estão em revisão pela LeadLovers, também pela tela, que só o suporte libera (https://www.leadlovers.com/suporte), e não tente de novo.
- **Aguardando aprovação** (`awaiting-approval`): contas novas passam por revisão quando importam com menos de 5 minutos da importação anterior, mais de 10 vezes na mesma hora ou mais de 20 mil contatos. Não é erro. Diga o motivo que a resposta traz, que a equipe da LeadLovers revisa, que o cliente recebe aviso e e-mail com a decisão e que dá para cancelar enquanto espera. Se `list_lead_imports` mostrar uma importação de menos de 5 minutos atrás, avise no resumo que esta pode ficar aguardando e sugira esperar alguns minutos.
- **Importação desligada** (`IMPORT_NOT_ENABLED`): a importação por aqui não está disponível para esta conta agora. Oriente pela tela de importação da LeadLovers.
