---
name: criar-mensagem-whatsapp
description: Cria, ajusta, ativa, pausa e exclui mensagens automáticas de WhatsApp de um projeto de WhatsApp na LeadLovers, agenda e cancela disparos e cria o link de WhatsApp que leva o contato da página para a conversa. Use quando o cliente falar em WhatsApp, mensagem automática ou sequência no WhatsApp, disparo ou transmissão pelo WhatsApp, template da Meta, botão ou link de WhatsApp na página, ou quiser mudar, ativar, pausar ou apagar uma mensagem de WhatsApp. Lê antes o número do projeto (oficial, por QR Code ou nenhum), usa template ou texto conforme o número, sempre pergunta se a mensagem fica ativa ou só salva e confirma um resumo antes de criar, disparar ou excluir.
metadata:
  version: "1.0"
---

# Mensagens de WhatsApp na LeadLovers

## Regras que não podem falhar

1. Antes de criar ou mudar qualquer coisa, leia o número do projeto com `get_whatsapp_number`: ele decide o formato de tudo (passo 2).
2. `create_whatsapp_message` só é chamado depois que o cliente respondeu se a mensagem fica **ativa** ou **só salva** e disse "sim" ao resumo. Isso vale mesmo quando o pedido parece completo.
3. Disparo, exclusão e ativar uma mensagem que sai na hora para contatos já parados no nível dela só depois de mostrar o que muda, para quem e para quantos contatos, e de um "sim" explícito.
4. Nunca diga que algo foi ativado, agendado, enviado ou excluído sem a resposta da tool confirmar.

## Quando não usar

- E-mail ou campanha de e-mail: use `criar-email`. Um projeto é de e-mail ou de WhatsApp, nunca dos dois.
- Ainda não existe projeto de WhatsApp: crie antes com `criar-projeto` (tipo WhatsApp) e volte aqui.
- Conectar o número, ler o QR Code, criar ou enviar template para aprovação da Meta, caixa de entrada e grupos: são feitos no app da LeadLovers. Busque o passo a passo com `search_help` (por exemplo, "conectar número whatsapp") e cite o link do artigo.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`: "projeto", "mensagem automática", "ativa", "salva", "disparo", "número oficial", "número por QR Code", "template", "link de WhatsApp", "mensagem-chave".
- "Ativa" é sempre o sentido do cliente: a mensagem é enviada. Nunca use "ativa" para dizer que ela só ocupa o nível.
- Nunca mostre id, nome de tool, nome de campo, JSON nem as chaves das variáveis do template ("1", "HEADER_IMAGE"); fale "o primeiro espaço do modelo", "a imagem do topo".
- Uma pergunta por vez, com opções fechadas e uma recomendada. Nunca peça id: busque pelo nome e ofereça as opções.

## Passo 1, projeto

1. Chame `get_machines` e fique só com os projetos de WhatsApp (tipo 5 no glossário). Deixe de fora os projetos de automação (`isAutomation: true`).
2. Um só: confirme com o cliente. Vários: pergunte qual, pelo nome. Nenhum: explique que mensagens de WhatsApp moram num projeto de WhatsApp e ofereça criar um.

## Passo 2, número do projeto

Chame `get_whatsapp_number` e diga ao cliente, em uma frase, o que isso significa:

| Número | O que dizer | O que dá para fazer |
| --- | --- | --- |
| Oficial (API oficial do WhatsApp) | "Seu projeto usa o número oficial do WhatsApp. Pela regra da Meta, toda mensagem automática usa um modelo aprovado (template)." | Mensagens só com template aprovado, em todos os níveis, inclusive o primeiro. Sem disparo. Link ativo |
| Por QR Code | "Seu projeto usa um número conectado por QR Code: as mensagens são texto livre, com links." | Mensagens em texto, disparo e link ativo |
| Por QR Code, desconectado | Some à frase: "Ele está desconectado agora: nada sai até você ler o QR Code de novo no app." | O mesmo, mas nada é enviado até reconectar |
| Nenhum | "Este projeto ainda não tem número de WhatsApp. Posso deixar tudo pronto: as mensagens ficam salvas, sem enviar, e o link fica esperando o número." | Mensagens em texto, sempre salvas. Link pendente. Sem disparo |

Sem número, ofereça o artigo de como conectar (`search_help`, "conectar número whatsapp") e siga.

## Passo 3, o que fazer

Se o pedido não disser, pergunte:

1. Mensagem automática na sequência (recomendada para boas-vindas e acompanhamento).
2. Disparo: uma mensagem avulsa para os contatos do projeto numa data e hora (só número por QR Code).
3. Link de WhatsApp para a página: o contato clica no botão, manda a mensagem-chave e entra no projeto.
4. Mudar, ativar, pausar ou excluir o que já existe.

## Mensagem automática

1. **Sequência.** `get_sequences`; com mais de uma, pergunte qual. Chame `get_whatsapp_messages` para saber onde a nova entra (depois da última) e diga isso ao cliente ("vai ser a 3ª mensagem, depois de Boas-vindas").
2. **Projeto recém-criado.** Todo projeto de WhatsApp novo já nasce com a "Primeira Mensagem" no nível 1 ("Oi *|PNOME|* tudo bem?"), salva. Para a primeira mensagem do cliente, preencha essa com `update_whatsapp_message` (nome, conteúdo e o estado de envio) em vez de criar outra: criar faria um nível 2, e os contatos parariam no nível 1 salvo.
3. **Intervalo.** Pergunte quanto tempo depois da mensagem anterior (ou da entrada do contato, no nível 1) ela sai: na hora, minutos, horas ou dias.
4. **Conteúdo,** conforme o número do passo 2:
   - **Por QR Code ou sem número: texto.** Escreva a partir do que o cliente trouxer, em até 4.096 caracteres, com a formatação do WhatsApp (`*negrito*`, `_itálico_`) e links completos. `*|PNOME|*` vira o primeiro nome do contato e `*|NOME|*` o nome todo. Mostre o texto como o contato vai ler e ajuste até ele aprovar; a mensagem leva exatamente o texto aprovado.
   - **Oficial: template.** Siga `references/templates.md`: escolher um modelo aprovado, preencher cada espaço e mostrar a mensagem final. Sem modelo aprovado, nada pode ser criado agora; explique e não insista.
   - Botões não são criados por aqui: no número oficial, os botões são os do template.
5. **Estado de envio. Pergunte sempre:** "Quer deixar a mensagem ativa ou apenas salvar? Ativa: ela será enviada a cada contato que chegar a esta etapa. Salva: ela fica no lugar dela, sem enviar, e os contatos que chegarem aqui esperam até você ativar." Sem número, só dá para salvar: diga isso e peça o aceite em vez de perguntar.
6. **Quem já está esperando.** Os contatos que já terminaram a sequência ficam parados no nível seguinte ao da última mensagem, que é onde a nova entra; ativa, ela sai **na hora** para todos eles. Antes do resumo de uma mensagem ativa, conte esses contatos com `get_leads` filtrando o projeto (`machineIds`), a sequência (`sequenceId`) e esse nível (`level`, um a mais que o da última mensagem), página 1, e use o total. Com muitos contatos num número por QR Code, avise que um envio grande de uma vez pode fazer o WhatsApp bloquear o número e ofereça salvar.
7. **Resumo e "sim".** Projeto, sequência e posição; o texto (ou o template com os espaços preenchidos); o intervalo em palavras ("1 dia depois da anterior"); ativa ou salva, em palavras ("ativa: sai agora para os 230 contatos que já terminaram a sequência e, depois, para cada contato que chegar a esta etapa").
8. **Criar.** `create_whatsapp_message` com `sequenceId`, `name` (curto, para o cliente achar depois), `delay`, `content` e `enabled` (`true` para ativa, `false` para salva). Ou `update_whatsapp_message` na Primeira Mensagem, como no item 2 (no projeto novo, ninguém terminou a sequência ainda).

**Resultado.** Diga o que a resposta confirmou: "Criei a mensagem e ela está ativa: será enviada a cada contato que chegar a esta etapa." ou "Criei a mensagem salva, sem enviar. Quando quiser, é só pedir para ativar." Se a resposta disser que ela não pode ser ativada (falta número, falta template), explique o porquê em uma frase.

## Disparo

Só com número por QR Code. Número oficial: explique que, na LeadLovers, o número oficial não faz disparo e ofereça uma mensagem automática com template. Sem número: explique que é preciso conectar um número por QR Code antes.

1. **Texto,** como na mensagem automática (só texto, sem template).
2. **Data e hora,** no horário de Brasília, pelo menos 30 minutos à frente. Não existe "só salvar": o disparo sai sozinho na hora marcada. Diga isso.
3. **Público:** todos os contatos do projeto que estão numa sequência. Para o número, chame `get_machines` com `includeLeadCount: true` e diga "cerca de N contatos". Com muitos contatos, avise que disparos grandes por QR Code podem fazer o WhatsApp bloquear o número.
4. **Resumo e "sim":** texto, dia da semana, data e hora ("sexta, 20/11, às 9h") e "para cerca de N contatos". Pergunte "Posso agendar o disparo para cerca de N contatos?".
5. **Agendar.** `create_whatsapp_broadcast` com `name`, `content` de texto e `sendAt` com o fuso `-03:00` (por exemplo, `2026-11-20T09:00:00-03:00`).
6. **Resultado:** "Agendei o disparo para sexta, 20/11, às 9h, para 1.230 contatos. Até lá, dá para mudar ou cancelar." O número de contatos é contado de novo na hora do envio.

**Mudar ou cancelar.** Encontre com `get_whatsapp_broadcasts`, confirme qual (nome e data) e, depois do "sim", use `update_whatsapp_broadcast`: só o que muda (nome, texto ou data), ou o cancelamento sozinho. Disparo que já saiu ou está saindo não muda mais; para corrigir, ofereça um novo.

## Link de WhatsApp

Siga `references/link-de-whatsapp.md`. Em resumo: a página do projeto ganha um botão "Falar no WhatsApp" que aponta para o link; o contato clica, o WhatsApp abre com a mensagem-chave pronta e, quando ele envia, entra na sequência com nome e telefone vindos do próprio WhatsApp. Sem número, o link nasce pendente: o endereço já vale para o botão, mas quem clicar vê um aviso até o número ser conectado.

## Mudar, ativar, pausar ou excluir

1. Encontre a mensagem pelo que o cliente diz (projeto, sequência, posição ou nome) com `get_machines`, `get_sequences` e `get_whatsapp_messages`, e confirme qual é ("a mensagem Boas-vindas, primeira da Sequência Inicial do projeto Atendimento").
2. Envie em `update_whatsapp_message` só o que muda: `name`, `delay`, `content` (inteiro: ele substitui o atual) ou `enabled` (`true` ativa, `false` pausa).
3. Ativar ou pausar passa pelo mesmo resumo e "sim". Ativar uma mensagem salva a manda na hora para os contatos que já esperam no nível dela: conte-os com `get_leads` (projeto, sequência e o nível da mensagem) e diga o número no resumo. Se a mensagem está ativa e o conteúdo vai mudar, avise que a mudança vale para os próximos envios.
4. Número oficial e mensagem em texto (salva antes de o número ser conectado): ela só pode ser ativada depois de trocar o texto por um template. Ofereça a troca (`references/templates.md`) e mande `content` e `enabled: true` juntos.
5. **Excluir:** mostre qual mensagem sai e que as seguintes sobem uma posição, espere o "sim" e use `delete_whatsapp_message`. Não dá para desfazer por aqui.
6. Posição e sequência de uma mensagem não mudam por aqui.

## Erros

Traduza e dê o próximo passo; nunca crie outra mensagem, disparo ou link no lugar de um recusado sem o cliente pedir. Toda recusa abaixo acontece antes de gravar: nada foi criado nem mudado.

| Código ou situação | O que dizer |
| --- | --- |
| `WHATSAPP_NUMBER_REQUIRED` | O projeto não tem número conectado, então nada pode ser ativado nem disparado; ofereça salvar e o artigo de como conectar |
| `WHATSAPP_TEMPLATE_REQUIRED` | O número é oficial e só envia template; ofereça escolher um modelo aprovado |
| `WHATSAPP_TEXT_REQUIRED` | O número é por QR Code (ou não há número) e a mensagem tem que ser texto |
| `WHATSAPP_TEMPLATE_NOT_APPROVED` | Esse modelo não está aprovado para este número; mostre os aprovados de novo |
| `WHATSAPP_TEMPLATE_VARIABLES` | Faltou ou sobrou um espaço do modelo, ou a imagem não é um link https; pergunte o que falta |
| `WHATSAPP_BROADCAST_NOT_SUPPORTED` | O número oficial não faz disparo; ofereça uma mensagem automática com template |
| `WHATSAPP_SEND_DATE_INVALID` | A data precisa estar pelo menos 30 minutos à frente; peça outro horário |
| `WHATSAPP_BROADCAST_ALREADY_SENT` | O disparo já saiu ou está saindo e não muda mais |
| `WHATSAPP_KEYWORD_INVALID` ou `WHATSAPP_KEYWORD_IN_USE` | Veja `references/link-de-whatsapp.md` |
| `WHATSAPP_MACHINE_NOT_SUPPORTED` | É um projeto de WhatsApp Premium, que só pode ser mudado no app |
| `AUTOMATION_MACHINE_NOT_SUPPORTED` | O projeto é do editor de automação antigo; ofereça outro projeto |
| Resposta dizendo que pode ter sido gravado mesmo assim | Consulte a lista antes de tentar de novo, para não criar em dobro |
| Conexão sem permissão de escrita | A conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo |
| LeadLovers recusou a conexão (401) | A conexão expirou ou foi revogada; reconecte a LeadLovers no assistente |
