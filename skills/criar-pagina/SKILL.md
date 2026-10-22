---
name: criar-pagina
description: Cria e edita páginas da LeadLovers com o cliente, como página de captura, landing page, página de obrigado, de vendas, de download ou de lançamento. Use quando o cliente quiser uma página para captar contatos, divulgar uma oferta, agradecer um cadastro ou entregar um material, ou quiser mudar o texto ou o visual de uma página que já existe. Escolhe o projeto pelo nome, traduz o tipo de página, sugere um nome de endereço válido e único, escreve a página completa com o formulário de captura padrão e confirma antes de criar. Ao editar, muda só o que foi pedido e preserva o formulário.
metadata:
  version: "1.0"
---

# Criar página na LeadLovers

## Objetivo

Criar uma página dentro de um projeto, com o conteúdo aprovado pelo cliente e, quando for de captura, com o formulário que coloca os contatos no projeto. Editar páginas existentes sem apagar o que não foi pedido.

## Quando não usar

- O cliente quer o funil inteiro (projeto, páginas e e-mails): use `montar-funil` quando ela estiver disponível.
- O projeto ainda não existe: crie antes com `criar-projeto` e volte.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`. Diga sempre "projeto".
- Nunca mostre id, nome de tool, nome de campo, JSON ou código HTML.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## Passo 1, projeto

Toda página pertence a um projeto, e quem preenche o formulário dela entra nesse projeto. Liste os projetos pelo nome com `get_machines` e pergunte em qual a página vai. Se a conta não tiver projeto, ofereça criar um com `criar-projeto`.

## Passo 2, tipo

Pergunte o tipo com as opções de `references/tipos-de-pagina.md`, em linguagem simples, recomendando "captura" quando o objetivo for juntar contatos.

## Passo 3, conteúdo

Pergunte, uma coisa por vez e só o que faltar: o objetivo da página, a oferta (o que a pessoa ganha), para quem é e a chamada principal do botão. Escreva o texto da página (título, subtítulo, benefícios, chamada) e mostre ao cliente como ele vai ler, nunca o código. Ajuste até a aprovação.

<!-- funil -->
### Montar a página

- HTML completo e responsivo, que funcione bem no celular, com o estilo no próprio HTML e sem script externo.
- Imagens só quando o cliente pedir ou o tipo pedir (foto da oferta, capa do material), com `search_images`, usando a URL da imagem que a tool devolve.
- Páginas de captura e de lançamento levam o formulário de captura padrão (veja "Formulário de captura padrão"). As demais só levam se o cliente pedir.
- Nome no endereço (`name`): sugira um curto, só com letras sem acento, números e hífen ("curso-confeitaria", "ebook-receitas"). Ele faz parte do endereço público e precisa ser único no projeto: confira com `get_pages` (com o `machineId` do projeto) antes de criar. Se já existir, sugira outro ("curso-confeitaria-2").
- Crie com `create_page`: `machineId`, `name`, `type` e `html`, sem `json` (a LeadLovers converte o HTML para o editor de páginas sozinha). Sem `html`, a página nasce como rascunho; faça isso só se o cliente quiser apenas reservar o nome.
- `NAME_EXISTS` em `create_page`: o nome já é usado no projeto; sugira outro e confirme antes de tentar de novo.
<!-- /funil -->

O formulário padrão, com as regras do que pode e do que não pode mudar nele, está em `references/formulario-de-captura.md`.

## Passo 4, confirmação

Resuma em uma linha e espere o "sim": "Vou criar a página de captura curso-confeitaria no projeto Curso de Confeitaria, com o formulário de nome e e-mail. Posso criar?".

## Resultado

Diga o que a tool confirmou: "Criei a página de captura curso-confeitaria no projeto Curso de Confeitaria. Quem preencher o formulário entra nesse projeto." O endereço completo e a publicação ficam na tela de páginas do projeto na LeadLovers; não invente um endereço. Ofereça o próximo passo: o e-mail de boas-vindas para quem se cadastrar (`criar-email`) ou a página de obrigado.

## Editar uma página

1. Encontre a página pelo nome com `get_pages` (pelo projeto, se o cliente disser) e confirme qual é.
2. `get_page_details` traz tipo, nome e projeto. Chame `get_page_html` só quando for mexer no conteúdo, porque o HTML pode ser grande.
3. Altere só o trecho pedido no HTML que veio de `get_page_html` e preserve o resto, em especial o formulário de captura (o endereço do `action` e os campos `formType`, `email` e `name`).
4. Mostre a mudança em palavras e confirme antes de gravar.
5. `update_page` com o `id` e só os campos que mudam (`html`, `name` ou `type`). Cada campo enviado substitui o atual; um campo omitido fica como está. Uma página não muda de projeto.

## Excluir

Só a pedido explícito do cliente. Confirme dizendo o efeito ("a página curso-confeitaria sai do ar e some da lista de páginas") e, com o "sim", chame `delete_page`.

## Erros

- `NAME_EXISTS`: nome de endereço já usado no projeto; sugira outro.
- Nome recusado por caractere inválido: use só letras sem acento, números e hífen.
- `PAGE_NOT_FOUND`: a página foi excluída ou não é desta conta; liste as páginas de novo com `get_pages`.
- Conexão sem permissão de escrita: a conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo.
- LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; reconecte a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase, diga o próximo passo e não tente de novo sem o cliente pedir.
