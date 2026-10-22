---
name: primeiros-passos
description: Apresenta a conta LeadLovers ao cliente em linguagem simples e leva ao próximo passo certo. Use no começo da conversa, quando o cliente perguntar o que dá para fazer, como funciona a LeadLovers ou por onde começar, ou quando trouxer um objetivo vago como vender um curso, captar contatos, divulgar um e-book ou fazer um funil. Mostra quantos projetos (máquinas) a conta tem, de que tipo, com quantas sequências e e-mails, explica só os termos que aparecem e oferece criar projeto, criar e-mail ou campanha, criar página de captura, organizar contatos ou montar um funil. Só consulta a conta, nunca cria nem altera nada.
metadata:
  version: "1.0"
---

# Primeiros passos na LeadLovers

## Objetivo

Dar ao cliente um raio-x curto da conta e encaminhá-lo para a ação que resolve o que ele quer, sem jargão. Esta skill só lê: nenhuma tool de escrita é chamada aqui.

## Quando não usar

- O cliente já pediu algo concreto: criar um projeto (`criar-projeto`), um e-mail ou campanha (`criar-email`), uma página (`criar-pagina`), ou encontrar, marcar ou mover contatos (`organizar-contatos`). Vá direto para a skill da tarefa.
- O cliente quer o funil inteiro pronto (projeto, página e e-mails): use `montar-funil` quando ela estiver disponível.

## Como falar com o cliente

- Use os termos da primeira coluna de `references/glossario.md`. Diga sempre "projeto"; se o cliente disser "máquina", explique uma vez que é o mesmo (o nome antigo) e siga com "projeto".
- Nunca mostre id, nome de tool, nome de campo ou JSON.
- Uma pergunta por vez, com opções fechadas e uma recomendada.

## Passo 1, raio-x da conta

1. Chame `get_machines_structure`. Ela traz cada projeto com o tipo (número, veja o glossário), as sequências e os e-mails de cada sequência.
2. Se o cliente perguntar por contatos, chame também `get_machines` com `includeLeadCount: true` para ter quantos contatos há em cada projeto. Um contato pode estar em vários projetos, então nunca some os números dos projetos como se fosse o total da conta.
3. Resuma em até 6 linhas no total, contando tudo o que vem antes da pergunta do passo 2, por exemplo:

   > Sua conta tem 4 projetos: 3 de e-mail e 1 de WhatsApp. O maior é "Curso de Confeitaria", com 2 sequências e 7 e-mails automáticos. "Lançamento Novembro" ainda não tem nenhum e-mail.

   Com mais de 5 projetos, cite os 3 com mais e-mails e diga quantos outros existem. Não liste ids nem todos os níveis, e não acrescente observações ou alertas sobre os outros projetos: se o cliente quiser detalhes, ele pede.
4. Explique só os termos que apareceram no resumo, numa frase cada (projeto, sequência, e-mail automático), usando o glossário.

## Passo 2, objetivo

Pergunte o que o cliente quer fazer agora, com estas opções:

1. Criar um projeto novo.
2. Criar um e-mail ou uma campanha.
3. Criar uma página (captura, obrigado, vendas, download).
4. Organizar contatos: encontrar, marcar com tag, colocar num projeto.
5. Montar um funil completo: projeto, página de captura e e-mails de boas-vindas.

Recomende uma pela situação:

- Objetivo de negócio vago ("quero vender meu curso", "quero captar contatos"): recomende a 5.
- Conta sem nenhum projeto: recomende a 5 e explique o caminho mínimo em uma frase: um projeto para guardar os contatos, uma página de captura para eles entrarem e um e-mail de boas-vindas que chega sozinho a quem se cadastrar.
- Pedido sobre contatos que já existem: recomende a 4.

## Passo 3, encaminhar

Siga a skill da opção escolhida: `criar-projeto`, `criar-email`, `criar-pagina`, `organizar-contatos` ou `montar-funil`.

Se `montar-funil` não estiver disponível no seu ambiente, monte o funil encadeando as outras, nesta ordem e com o cliente aprovando cada etapa: `criar-projeto` (tipo e-mail), `criar-pagina` (página de captura no projeto criado) e `criar-email` (e-mail automático de boas-vindas no nível 1 da "Sequência Inicial" do projeto, perguntando se ele fica ativo ou só salvo).

## Erros

- Resposta dizendo que a LeadLovers recusou a conexão (401): explique que a conexão com a LeadLovers expirou ou foi revogada e que basta reconectar a LeadLovers no assistente.
- Qualquer outra falha: diga em uma frase que não foi possível ler a conta agora e ofereça tentar de novo. Não invente números.

## Resultado

Esta skill termina com o cliente escolhendo o próximo passo. Nada foi criado nem alterado; não diga o contrário.
