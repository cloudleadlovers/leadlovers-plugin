---
name: criar-pagina
description: Cria e edita páginas da LeadLovers com o cliente, como página de captura, landing page, página de obrigado, de vendas, de download ou de lançamento. Use quando o cliente quiser uma página para captar contatos, divulgar uma oferta, agradecer um cadastro ou entregar um material, ou quiser mudar o texto ou o visual de uma página que já existe. Escolhe o projeto pelo nome, traduz o tipo de página, sugere um nome de endereço válido e único, escreve a página completa com o formulário de captura padrão, o título e a descrição de busca, liga a captura à página de obrigado e confirma antes de criar. Ao editar, muda só o que foi pedido e preserva o formulário.
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

Toda página pertence a um projeto, e quem preenche o formulário dela entra nesse projeto. Liste os projetos pelo nome com `get_machines`, fora os de automação (veja o glossário), e pergunte em qual a página vai. Se a conta não tiver projeto, ofereça criar um com `criar-projeto`.

Página de captura só em projeto de e-mail (tipo 1 no glossário): o formulário padrão pede o e-mail da pessoa. Se o cliente escolher um projeto de outro canal (WhatsApp, SMS e os demais), explique isso em uma frase e ofereça um projeto de e-mail no lugar.

## Passo 2, tipo

Pergunte o tipo com as opções de `references/tipos-de-pagina.md`, em linguagem simples, recomendando "captura" quando o objetivo for juntar contatos.

Captura e lançamento levam quem se cadastra a uma página de obrigado. Veja com `get_pages` (com o `machineId` do projeto) se o projeto já tem página de obrigado (tipo `next-steps`) e pergunte uma vez para onde a pessoa vai depois do cadastro:

- **Criar a página de obrigado agora** (recomendado quando o projeto não tem uma): ela é escrita e aprovada junto com a captura e criada antes dela.
- **Usar a página de obrigado que já existe**, pelo nome (recomendado quando há uma).
- **Um endereço de fora da LeadLovers** que o cliente informar, começando com `http://` ou `https://`.

## Passo 3, conteúdo

Pergunte, uma coisa por vez e só o que faltar: o objetivo da página, a oferta (o que a pessoa ganha), para quem é e a chamada principal do botão. Escreva o texto da página (título, subtítulo, benefícios, chamada) e mostre ao cliente como ele vai ler, nunca o código. Ajuste até a aprovação.

<!-- funil -->
### Montar a página

- HTML completo e responsivo, que funcione bem no celular, escrito para o editor visual da LeadLovers, onde o cliente muda textos, cores e fontes com um clique:
  - cores e fontes da marca como variáveis no `:root` (`--cor-principal`, `--cor-escura`, `--cor-clara`, `--cor-fundo`, `--cor-texto`, `--fonte-titulos` e `--fonte-textos`), com o resto do CSS usando `var()`;
  - cada seção é uma `<section>` (ou `<header>` e `<footer>`) filha direta do `<body>`, sem um contêiner em volta de todas;
  - `<head>` sem atributos, sempre escrito assim, porque a LeadLovers coloca o SEO e os scripts da página procurando esse texto exato; o idioma vai em `<html lang="pt-BR">`;
  - estilo num `<style>` dentro do `<head>`, fontes do Google por `<link>` e nenhum `<script>`;
  - toda imagem com `alt` que descreve a imagem.
- Imagens só quando o cliente pedir ou o tipo pedir (foto da oferta, capa do material), com `search_images`, usando a URL da imagem que a tool devolve.
- Páginas de captura e de lançamento levam o formulário de captura padrão (veja "Formulário de captura padrão"). As demais só levam se o cliente pedir.
- Nome no endereço (`name`): sugira um curto, só com letras sem acento, números e hífen ("curso-confeitaria", "ebook-receitas"). Quando o cliente der um nome com acento, espaço ou maiúscula, diga em uma frase que o endereço não aceita esses caracteres e mostre a sugestão ("Promoção de Natal" fica "promocao-de-natal"). Ele faz parte do endereço público e precisa ser único no projeto: confira com `get_pages` (com o `machineId` do projeto) antes de criar. Se já existir, sugira outro ("curso-confeitaria-2").
- Título e descrição de busca: escreva `title` (o título da página, até 255 caracteres) e `description` (uma ou duas frases sobre a oferta, até 1000) a partir do texto aprovado, sem `<`, `>` nem aspas duplas, e mostre junto com o texto. Eles aparecem na aba do navegador, na busca do Google e quando o link é compartilhado.
- Captura e lançamento levam quem se cadastra à página de obrigado: crie primeiro a página de obrigado (tipo `next-steps`) e mande a `url` que a resposta dela trouxe em `nextUrl` e `existsUrl` da captura (`existsUrl` é para quem já estava no projeto). Com uma página de obrigado que já existe, use a `url` que `get_pages` traz dela. Se a `url` vier vazia, crie a captura sem os dois e diga ao cliente que o envio para a página de obrigado é configurado na LeadLovers. Nunca monte um endereço por conta própria.
- Crie com `create_page`: `machineId`, `name`, `type`, `html`, `title` e `description`, sem `json`; na captura, também `nextUrl` e `existsUrl`. Sem `html`, a página nasce como rascunho; faça isso só se o cliente quiser apenas reservar o nome.
- `NAME_EXISTS` em `create_page`: o nome já é usado no projeto; sugira outro e confirme antes de tentar de novo.
<!-- /funil -->

O formulário padrão, com as regras do que pode e do que não pode mudar nele, está em `references/formulario-de-captura.md`.

## Passo 4, confirmação

Resuma em uma linha e espere o "sim": "Vou criar a página de obrigado obrigado-confeitaria e a página de captura curso-confeitaria no projeto Curso de Confeitaria, com o formulário de nome e e-mail, levando quem se cadastrar para a de obrigado. Posso criar?".

## Resultado

Diga o que a tool confirmou: "Criei a página de captura curso-confeitaria no projeto Curso de Confeitaria. Quem preencher o formulário entra nesse projeto e vai para a página de obrigado." Mostre o endereço de cada página como a resposta trouxe (`url`). Se veio vazio, diga que o endereço aparece na tela de páginas do projeto na LeadLovers. Nunca monte um endereço por conta própria.

Se a resposta trouxer o link do editor (`editorUrl`), ofereça: "Quer ajustar textos e imagens você mesmo? Abra no editor: <link>", com o link como veio. Sem ele, não fale do editor.

Ofereça o próximo passo: o e-mail de boas-vindas para quem se cadastrar (`criar-email`).

## Editar uma página

1. Encontre a página pelo nome com `get_pages` (pelo projeto, se o cliente disser) e confirme qual é.
2. `get_page_details` traz tipo, nome, projeto, endereço, título e descrição de busca e para onde vai quem se cadastra. Chame `get_page_html` só quando for mexer no conteúdo, porque o HTML pode ser grande; ela traz também o `hash` desse HTML.
3. Altere só o trecho pedido no HTML que veio de `get_page_html` e preserve o resto, em especial o formulário de captura (o endereço do `action` e os campos `formType`, `email` e `name`) e o `<head>` sem atributos.
   - "Título", sem mais nada, é o título grande que aparece na página, no HTML. O título de busca (`title`) só muda quando o cliente falar em aba, navegador, Google ou busca. Não pergunte qual dos dois.
4. Mostre a mudança em palavras e confirme antes de gravar.
5. `update_page` com o `id` e só os campos que mudam (`html`, `name`, `type`, `nextUrl`, `existsUrl`, `title` ou `description`). Cada campo enviado substitui o atual; um campo omitido fica como está. Uma página não muda de projeto. Quando mandar `html`, mande também `baseHash` com o `hash` que `get_page_html` trouxe (ou o da última gravação), para não apagar uma mudança que o cliente fez no editor enquanto isso.
   - Para onde vai quem se cadastra, título ou descrição de busca: `update_page` só com `id` e esses campos, sem `html` nem `baseHash`, e sem ler o HTML. Ligar uma captura à página de obrigado é `nextUrl` e `existsUrl` com a `url` da página de obrigado, que `get_pages` traz.
6. Se a resposta for `CONTENT_CHANGED`, nada foi gravado: diga "A página mudou desde que eu li; posso ler de novo antes de alterar?" e, com o sim, leia de novo com `get_page_html`, refaça a mudança sobre o HTML novo e grave com o `hash` novo. Nunca grave sem `baseHash` para forçar.
7. Depois de gravar, diga o que mudou e pare: não sugira outras mudanças no texto nem em outras páginas ou e-mails que o cliente não pediu.

## Excluir

Só a pedido explícito do cliente. Confirme dizendo o efeito ("a página curso-confeitaria sai do ar e some da lista de páginas") e, com o "sim", chame `delete_page`.

## Erros

- `NAME_EXISTS`: nome de endereço já usado no projeto; sugira outro.
- Nome recusado por caractere inválido: use só letras sem acento, números e hífen.
- Endereço recusado em `nextUrl` ou `existsUrl`: precisa ser completo, com `http://` ou `https://` e um domínio; peça ao cliente o link inteiro.
- Título ou descrição recusados: tire `<`, `>` e aspas duplas, e respeite 255 caracteres no título e 1000 na descrição.
- `PAGE_NOT_FOUND`: a página foi excluída ou não é desta conta; liste as páginas de novo com `get_pages`.
- `CONTENT_CHANGED`: a página mudou desde a leitura e nada foi gravado; siga o item 6 de "Editar uma página".
- Conexão sem permissão de escrita: a conexão só permite consultar; reconecte a LeadLovers no assistente e aprove o acesso completo.
- LeadLovers recusou a conexão (401): a conexão expirou ou foi revogada; reconecte a LeadLovers no assistente.
- Qualquer outro erro: traduza em uma frase, diga o próximo passo e não tente de novo sem o cliente pedir.
