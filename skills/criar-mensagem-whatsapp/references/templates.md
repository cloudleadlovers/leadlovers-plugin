# Template no número oficial

O número oficial (API oficial do WhatsApp, da Meta) só envia modelos aprovados pela Meta, os templates, em toda mensagem automática, inclusive a primeira. Os templates são criados e enviados para aprovação no app da LeadLovers; nada aqui cria, edita ou envia template para aprovação.

## Escolher o modelo

1. Chame `get_whatsapp_templates`. Vem só o que está aprovado para o número do projeto.
2. **Nenhum modelo aprovado:** explique que, com o número oficial, toda mensagem precisa de um modelo aprovado, que ele é criado no app e que a Meta leva um tempo para aprovar. Ofereça o artigo do app (`search_help`, "template whatsapp") e pare aqui: não crie a mensagem em texto.
3. **Com modelos:** mostre cada um pelo nome, com o texto do corpo como o contato vai ler (os espaços como "[espaço 1]"), o topo e os botões, se houver. Recomende o que mais combina com o pedido e pergunte qual usar.

## Preencher os espaços

Cada variável do modelo é um espaço que precisa de valor, todos eles, nenhum a mais. Pergunte um por vez, em palavras:

| Espaço | Como perguntar | O que mandar |
| --- | --- | --- |
| No texto ("{{1}}", "{{2}}") | "O que vai no primeiro espaço: 'Oi [espaço 1], seu material chegou'? Normalmente é o nome; posso usar o primeiro nome de cada contato." | Texto de até 250 caracteres; `*\|PNOME\|*` para o primeiro nome e `*\|NOME\|*` para o nome todo |
| No título de texto do topo | "O que vai no título do topo?" | Texto de até 250 caracteres |
| Num botão com link | "Para qual endereço o botão leva? Complete o final do link." | O pedaço do link que o modelo deixa em aberto |
| Imagem, vídeo ou documento do topo | "Qual imagem vai no topo? Me mande o link dela (começando com https)." | Um link https público do arquivo |

Nas tools, cada valor vai em `variables`, com a chave que `get_whatsapp_templates` mostra para aquele espaço, e `content` fica `type: template` com o `templateId` escolhido. Nunca mostre as chaves ao cliente.

## Mostrar e confirmar

Monte a mensagem final com os espaços preenchidos e mostre como o contato vai ler. O cliente aprova o texto final antes do resumo da mensagem.

## Recusas

- `WHATSAPP_TEMPLATE_VARIABLES`: a resposta diz quais espaços faltaram, sobraram ou estão inválidos; pergunte de novo só esses.
- `WHATSAPP_TEMPLATE_NOT_APPROVED`: o modelo deixou de estar aprovado ou não é deste número; chame `get_whatsapp_templates` de novo e mostre os aprovados.
- Modelo que o cliente viu recusado no app: ele corrige e envia de novo para aprovação no app; aqui só entram os aprovados.
