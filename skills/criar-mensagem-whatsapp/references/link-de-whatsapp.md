# Link de WhatsApp

O link de WhatsApp é como o contato entra no projeto pela conversa. A página do projeto tem um botão "Falar no WhatsApp" que aponta para o endereço do link; o contato clica, o WhatsApp abre com a mensagem-chave já escrita e, quando ele envia, entra na sequência escolhida, com o nome e o telefone que vêm do próprio WhatsApp. Criar o link não envia nada e não custa nada.

## Criar

1. **Projeto e número:** passos 1 e 2 da skill. Com número (oficial ou por QR Code), o link já nasce funcionando. Sem número, ele nasce **pendente**: o endereço já existe e já pode ir no botão da página, mas quem clicar vê um aviso de que o contato ainda não está disponível; a partir do primeiro clique depois que o cliente conectar o número, a conversa abre normalmente, sem mudar nada.
2. **Sequência e posição:** `get_sequences`. Recomende a "Sequência Inicial" a partir da primeira mensagem; só pergunte outra posição se o cliente pedir.
3. **Mensagem-chave:** uma frase curta que o contato vai mandar, ligada à oferta, por exemplo "Quero o e-book Receitas Fit". Sugira uma e peça o ok. Regras:
   - única entre os links do projeto (maiúsculas e minúsculas contam como iguais); confira com `get_whatsapp_links`;
   - sem `&`, `#`, `+`, `%`, `[` e `]`, até 394 caracteres;
   - curta, porque o contato pode apagar ou mudar sem querer; se ele escrever algo antes ou depois, ainda entra.
4. **Tags e pontuação** (opcional): pergunte só se o cliente falar disso. Tags vêm de `get_tags`; tag nova é criada com `create_tag` antes.
5. **Título:** nome interno do link, sem emoji (por exemplo, o nome da oferta). O contato não vê.
6. **Resumo e "sim":** projeto, sequência, mensagem-chave e, sem número, que o link fica esperando o número.
7. `create_whatsapp_link` com `title`, `keyword`, `sequenceId` e, se combinados, `level`, `tags` e `score`.

**Resultado:** diga o que a resposta confirmou.

- Ativo: "Criei o link de WhatsApp. Quem clicar no botão abre o WhatsApp com 'Quero o e-book Receitas Fit' escrito e, ao enviar, entra no projeto."
- Pendente: "Criei o link, mas ele fica esperando o número do projeto: até você conectar um número de WhatsApp, quem clicar vê um aviso. Não divulgue a página antes disso."

## Colocar na página

O endereço do link (o `url` da resposta) é o destino do botão "Falar no WhatsApp". A página de captura de um projeto de WhatsApp não precisa de formulário: o contato entra pela conversa.

- **Página nova:** crie com `create_page` no mesmo projeto, com o botão apontando para o endereço do link, no lugar do formulário.
- **Página que já existe:** leia com `get_page_html`, troque só o formulário (ou o botão antigo) pelo botão do link, mostre o que muda e grave com `update_page`, mandando o `hash` lido como `baseHash`.

## Mudar

`update_whatsapp_link` com só o que muda: título, mensagem-chave, sequência, posição, tags ou pontuação. O endereço não muda, então o botão da página continua funcionando. As tags substituem as atuais: mande as que já existem junto com a mudança.

## Excluir

Mostre qual link sai (título e mensagem-chave), avise que o botão de qualquer página que aponte para ele para de funcionar e que não dá para desfazer, espere o "sim" e use `delete_whatsapp_link`.

## Recusas

| Código | O que dizer |
| --- | --- |
| `WHATSAPP_KEYWORD_INVALID` | A mensagem-chave tem um caractere que não pode (`&`, `#`, `+`, `%`, `[`, `]`), está vazia ou longa demais; sugira outra |
| `WHATSAPP_KEYWORD_IN_USE` | Outro link deste projeto já usa essa frase; sugira outra |
| `TAG_NOT_FOUND` | Uma das tags não existe na conta; confira com `get_tags` |
| `SEQUENCE_NOT_FOUND` | A sequência não é deste projeto; mostre as do projeto de novo |
