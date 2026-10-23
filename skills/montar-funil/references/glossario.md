<!-- Gerado por `pnpm skills:build` a partir de skills/_compartilhado/glossario.md. Não edite à mão. -->

# Glossário da LeadLovers

Use a primeira coluna com o cliente. A última é só para você: nunca mostre ao cliente id, nome de tool, nome de campo ou JSON.

| Com o cliente | O que é | Nas tools |
| --- | --- | --- |
| Projeto | Onde ficam os contatos, as sequências e as páginas de um objetivo (um curso, um e-book, um lançamento). "Máquina" é o nome antigo da mesma coisa: se o cliente disser máquina, entenda projeto e continue dizendo projeto. | `machine`, `machineId` |
| Tipo de comunicação | Canal do projeto, escolhido na criação: e-mail, SMS, WhatsApp, Instagram, Telegram ou voz. | `type` de `create_machine`; nas listagens vem como número: 1 e-mail, 3 SMS, 5 WhatsApp, 6 Instagram, 7 Telegram, 8 voz |
| Projeto de automação | Projeto do editor de automação antigo, que a LeadLovers descontinuou. Continua com os contatos dele e recebe contatos novos, mas sequência, e-mail e página novos nele só pelo editor de automação, no app. Não o ofereça como destino do que for criar; se o cliente pedir por ele, explique isso em uma frase e ofereça outro projeto. | `isAutomation: true` nas listagens de projeto; as tools de criação recusam com `AUTOMATION_MACHINE_NOT_SUPPORTED` |
| Sequência | Caminho de mensagens que o contato percorre dentro do projeto, em ordem. Todo projeto novo já nasce com a "Sequência Inicial". | `sequence`, `sequenceId` |
| Nível | Posição de uma mensagem na sequência: o nível 1 é a primeira. Contato no nível 3 está na terceira mensagem. | `level`: 1, 2, 3...; -1 deixa o contato no projeto sem começar a sequência |
| Campanha para a conta toda | E-mail avulso para todos os contatos da conta. | broadcast geral: `type: broadcast` sem `machineId` |
| Campanha do projeto | E-mail avulso para os contatos de um projeto. | broadcast do projeto: `type: broadcast` com `machineId` |
| E-mail automático | E-mail de uma sequência, enviado a cada contato que chega ao nível dele, depois do intervalo combinado. | e-mail de sequência: `type: sequence` com `machineId` e `sequenceId` |
| E-mail ativo | Para o cliente, e-mail que dispara. | `enabled: true`. O campo `active` da API só diz que o e-mail ocupa o nível e nunca é usado com o cliente |
| E-mail salvo | E-mail guardado sem enviar. Numa sequência ele continua no lugar dele, e os contatos que chegam ao nível esperam até ele ser ativado. | `enabled: false` |
| Contato | Pessoa cadastrada na conta, reconhecida pelo e-mail ou pelo telefone. | `lead`, `leadId` |
| Tag | Etiqueta livre para separar contatos ("comprou", "webinar outubro"). | `tags`, por id ou nome |
| Campo personalizado | Informação extra do contato que a conta criou (CPF, curso de interesse). | campo dinâmico: `dynamicFields`, `get_custom_fields` |
| Página | Página publicada de um projeto: captura, obrigado, vendas, download e outras. Quem preenche o formulário de uma página de captura entra no projeto dela. | `page`, `get_pages` |
| Ação em segundo plano | Mudança em massa (marcar com tag, colocar em projeto, excluir) que entra numa fila da LeadLovers e termina alguns instantes depois. Só diga que terminou depois de acompanhar o andamento. | `actionId`, `get_bulk_action_status` |
