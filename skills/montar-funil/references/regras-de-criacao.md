<!-- Gerado por `pnpm skills:build` a partir de skills/criar-projeto/SKILL.md, skills/criar-pagina/SKILL.md, skills/criar-pagina/references/formulario-de-captura.md, skills/criar-email/SKILL.md, skills/criar-email/references/estado-de-envio.md. Não edite à mão. -->

# Regras de criação usadas pelo funil

Cópia das regras de criar projeto, página e e-mail automático, para montar o funil mesmo quando as outras skills não estão carregadas.

### Criar o projeto

- O nome não pode repetir o de um projeto da conta: a LeadLovers recusa com `NAME_EXISTS`. Confira com `get_machines` antes de propor o nome. Se o cliente insistir num nome que já existe, explique que não dá e ofereça usar o projeto existente ou outro nome; nunca ofereça criar com o nome repetido nem chame `create_machine` com ele.
- Chame `create_machine` com `name` e `type` (`email`, `sms`, `whatsapp`, `instagram`, `telegram` ou `voice`).
- Resposta com `sequenceId`: o projeto já veio com a sequência "Sequência Inicial". Num projeto de e-mail, `emailId` é o e-mail do nível 1 dessa sequência, ainda sem conteúdo e salvo, sem enviar. Guarde os dois ids.
- Nunca chame `create_sequence` com o nome "Sequência Inicial": a sequência já existe e a resposta seria `NAME_EXISTS`.
- Nunca crie outro e-mail para o nível 1. O primeiro e-mail automático do projeto preenche esse nível com `update_email`; um e-mail novo viraria o nível 2 e os contatos ficariam parados no nível 1, que está sem enviar.
- `emailId` nulo num projeto de e-mail: o primeiro e-mail não pôde ser criado junto. O primeiro e-mail automático é então criado com `create_email` na "Sequência Inicial".
- Resposta sem `sequenceId`: crie a sequência com `create_sequence`, nome "Sequência Inicial".
- `NAME_EXISTS` em `create_machine`: já existe um projeto com esse nome. Ofereça usar o existente ou escolher outro nome; não repita a chamada com o mesmo nome.

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

### Formulário de captura padrão

A página sempre pertence a um projeto, e os contatos enviados pelo formulário entram nesse projeto. Por isso o formulário não leva id de projeto: é sempre este, com um POST para `https://leadlovers.com/capture`.

```html
<form action="https://leadlovers.com/capture" method="post">
  <input type="hidden" name="formType" value="static">
  <input type="email" name="email" placeholder="Seu e-mail" required>
  <input type="text" name="name" placeholder="Seu nome">
  <button type="submit">ENVIAR</button>
</form>
```

- `email` é obrigatório e `name` é recomendado. Os textos (placeholder, botão) e o estilo podem mudar; o `action`, o `method` e os nomes dos campos não.
- Telefone e campos personalizados ficam fora por enquanto: não acrescente outros campos ao formulário, mesmo que o cliente peça; diga que, por ora, a página capta nome e e-mail.
- O formulário fica dentro de uma das seções da página, sem `<script>` junto.
- Ao editar uma página que já tem esse formulário, preserve-o.

### E-mail automático: intervalo e nível 1

- **Intervalo.** Pergunte quanto tempo depois do e-mail anterior o novo deve sair, em dias, horas e minutos. Sugira 1 dia; para o primeiro e-mail de boas-vindas, sugira "assim que o contato entrar" (zero). Envie em `settings.sequence` com `sendAfter` (`days`, `hours`, `minutes`), `startTime: "00:00"` e `endTime: "23:59"`. Janela de horário (por exemplo, só das 8h às 18h) e dias da semana (`weekdays`, 1 = segunda a 7 = domingo) só se o cliente pedir.
- **Nível 1 de projeto novo.** Se a sequência tem só o e-mail do nível 1 que veio com o projeto (o `emailId` que `create_machine` devolveu, ou um nível 1 que `get_email` mostra sem conteúdo), o primeiro e-mail automático **preenche esse nível** com `update_email` (`id` desse e-mail, `subject`, `body` e `enabled` conforme o estado de envio) em vez de criar outro. Criar um novo faria dele o nível 2, e os contatos ficariam parados no nível 1, que está sem enviar. Nesse caso o intervalo fica como veio do projeto, porque `update_email` não muda intervalo; se o cliente quiser outro, explique que isso é ajustado na LeadLovers.

### Montar o HTML do e-mail

Escreva o `body` no formato que o editor visual da LeadLovers entende, para o cliente poder ajustar depois com um clique e o e-mail abrir bem em qualquer caixa de entrada. Vale para `create_email` e para o `update_email` que preenche o nível 1:

- uma tabela de 600px de largura, centralizada, com uma linha (`<tr>`) por seção (topo, título, texto, botão, rodapé);
- todo o estilo inline, em cada elemento, sem `<style>`, e nenhum `<script>`;
- só fontes seguras: Arial, Georgia, Verdana, Trebuchet MS, Tahoma ou Times New Roman;
- toda imagem com `alt` que descreve a imagem.

Não envie `editor`: a LeadLovers escolhe o editor do e-mail.

### E-mail automático: ativo ou salvo

Pergunte sempre, antes de criar: "Quer deixar o e-mail ativo ou apenas salvar? Ativo: ele será disparado assim que um contato chegar a esta etapa. Salvo: ele fica no lugar dele, sem enviar, e os contatos que chegarem aqui esperam até você ativar."

- **Ativo:** `enabled: true`. A LeadLovers envia o e-mail a cada contato que chega ao nível, seguindo o intervalo. Contatos reais recebem.
- **Salvo:** `enabled: false`. Nada é enviado. Os contatos que chegam ao nível ficam esperando e passam a receber quando o e-mail for ativado.
- **Recomendação:** salvar quando o cliente ainda quer revisar o texto ou o projeto já recebe contatos de verdade; ativo quando ele já aprovou o conteúdo e quer que o funil rode sozinho.

Para ficar ativo, a LeadLovers exige:

- conteúdo não vazio (`EMAIL_BODY_REQUIRED`);
- uma conta de disparo de e-mail ativa (`SENDER_REQUIRED`), configurada na LeadLovers;
- conta sem suspensão por pagamento em atraso (`ACCOUNT_SUSPENDED`);
- conteúdo sem tag de campo personalizado que o e-mail ainda não usava (`EMAIL_CUSTOM_FIELD_NOT_SUPPORTED`). As tags `*|NOME|*`, `*|EMAIL|*` e `*|TELEFONE|*` são da própria LeadLovers e não contam.

Se o conteúdo usar campo personalizado, avise antes de criar. Em qualquer dessas recusas, explique o motivo em uma frase e ofereça apenas salvar; nunca diga que ficou ativo.
