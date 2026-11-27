# Canais da automação

O que cada canal aceita num passo da automação, para propor o fluxo certo e montar sem a publicação recusar. Use os limites daqui ao escrever e diga ao cliente só o que muda a decisão dele (por exemplo "SMS tem até 160 caracteres, sem acento").

## Espera, horário e dias

- **Espera:** dias, horas e minutos depois do passo anterior; no primeiro passo, depois da entrada. Tudo zero envia na hora. WhatsApp e Instagram aceitam também segundos.
- **Horário:** só envia entre duas horas (e-mail no fuso da conta, os outros canais no horário de Brasília). No SMS a janela tem de ter pelo menos 59 minutos. Fora do horário o contato espera o próximo horário aberto; no WhatsApp dá para pular o passo em vez de esperar.
- **Dias da semana:** de domingo a sábado; só envia nos dias marcados.
- **Data fixa:** e-mail, WhatsApp e Instagram podem sair numa data (por exemplo o dia de um evento) em vez de depois da espera. No e-mail dá para pular o passo quando a data já passou.
- **Sem o dado:** contato sem e-mail, telefone ou Instagram para o canal do passo pode pular para o próximo passo, esperar alguns dias pelo dado ou sair da automação. Recomende pular: o fluxo continua pelos outros canais.

## E-mail

- Assunto (aceita `*|PNOME|*`, o primeiro nome) e, se quiser, o texto de prévia que aparece depois do assunto na caixa de entrada.
- HTML completo, que funciona no celular: uma tabela de 600px que encolhe (`width="600"` com `width:100%;max-width:600px`, nunca `width:600px` fixo), estilos inline, fontes seguras (Arial, Helvetica, Georgia), imagens com `alt`, sem script. Campos do contato: `*|PNOME|*`, `*|NOME|*`, `*|EMAIL|*`. Escreva o e-mail inteiro; nunca deixe texto de exemplo.
- Remetente: a conta de envio padrão vale quando o cliente não escolhe. Sem nenhuma conta de envio ativa, a publicação recusa; o cliente cadastra uma no app.
- Opções: envio inteligente (cada contato recebe na hora em que mais abre), rastrear as páginas visitadas depois do clique e a pesquisa de satisfação. Só ligue se o cliente pedir.
- **Anexos:** até 20 arquivos, 16 MB cada e 30 MB juntos: imagem (jpeg, png), PDF, Word, Excel, PowerPoint, txt, csv, áudio ou vídeo. Passe o link https que o cliente der; o arquivo é copiado para "Meus arquivos" da conta e anexado de lá. Link de pasta (Google Drive, Dropbox) ou que pede login não serve: peça o link direto do arquivo.
- **Convite com data:** para aula ao vivo, live ou reunião, o e-mail pode levar os botões de agendar no Google e no Outlook, com título, dia, início e fim (no mesmo dia), local ou link e descrição. O horário aparece no relógio de cada contato.
- Eventos: abriu ou clicou.

## SMS

- Até 160 caracteres, contados depois de tirar acentos e emojis, que não são enviados. Escreva já sem acento para o cliente ver o que vai sair: "Oi *|PNOME|*, a aula ao vivo comeca hoje as 20h. Link: ...".
- O SMS é liberado por conta; se a conta não tem, o canal não aparece entre os disponíveis.
- Sem eventos por enquanto.

## WhatsApp

- A automação envia pelo número ligado a ela (um por automação). Um número fica em um projeto por vez.
- **Número por QR Code:** envia texto livre de até 1024 caracteres, com `*|PNOME|*`, `*|NOME|*`, `*|EMAIL|*`, `*|TELEFONE|*` e campos personalizados. Não envia template.
- **Número oficial:** para quem não escreveu nas últimas 24 horas, só envia **template aprovado** pela Meta, preenchendo cada variável (nome, primeiro nome, e-mail, telefone, campo personalizado ou um texto fixo). Texto livre só sai para quem escreveu nas últimas 24 horas; os outros ficam parados no passo. Para o primeiro contato, use template. Templates são criados e aprovados no app, nunca por aqui; `get_automation_channel` lista os aprovados com as variáveis.
- Template com imagem, vídeo ou documento no cabeçalho pede o link https do arquivo.
- Ainda não: botões, anexos e cartão de contato.
- Sem eventos por enquanto.

## Instagram

- Envia pela conta do Instagram ligada à automação.
- Tipos: texto (até 1000 caracteres), imagem (link https) ou cartão com imagem, título, subtítulo e até 3 botões com links https. Cartão só por conta oficial.
- Sem eventos por enquanto.

## Ligação de voz

- Texto falado de até 160 caracteres, ou um áudio da biblioteca da conta (`get_automation_channel` com voz lista os áudios).
- `*|UNSUB|*` no texto fala a opção de descadastro; a tecla que descadastra é configurada em `update_automation_settings` (de 0 a 9, `*` ou `#`).
- Eventos: atendeu, ocupado ou caixa postal.

## Ações dos eventos e do "Ao terminar"

| Ação | O que faz | De onde vem |
| --- | --- | --- |
| Aplicar tag | Marca o contato com a tag | `get_tags`, ou `create_tag` com o aceite do cliente |
| Tirar tag | Tira a tag do contato | `get_tags` |
| Pontuar | Soma (ou subtrai) de -1000 a 1000 na pontuação do contato, nunca 0 | número combinado com o cliente |
| Copiar para outro projeto | Coloca o contato também em outro projeto, sequência e nível | `get_machines_structure` |
| CRM | Abre a negociação do contato numa etapa de um pipeline | `get_pipelines` |

Cada evento roda uma vez por contato. "Levar para outro projeto" num evento tira o contato do fluxo e o coloca no projeto escolhido; sem isso ele segue o fluxo normalmente.
