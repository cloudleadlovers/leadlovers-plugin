# Formulário de captura

<!-- funil -->
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
- Ao editar uma página que já tem esse formulário, preserve-o.
<!-- /funil -->

Exemplo de página com esse formulário no ar: https://ogbog.com.br/
