# Funcionalidades

## Dashboard inicial

1. **Objetivo:** apresentar situação das salas e próximas reservas do usuário.
2. **Comportamento observado:** lista espaços ativos, calcula manutenção, indisponibilidade, ocupação atual, reserva integral do expediente e disponibilidade. Exibe capacidade e estado de câmera, ar-condicionado e TV.
3. **Componentes:** galeria de salas, galeria de próximas reservas, modal de recursos, botão de atualização e temporizador.
4. **Tecnologia:** Power Apps, Power Fx e SharePoint.
5. **Regras verificadas:** somente reservas ativas; ocupação quando início ≤ agora < fim; expediente considerado entre 08:30 e 18:30; reservas do usuário quando ele é organizador ou convidado.
6. **Limitações:** cálculo depende de dados carregados no cliente e dos limites de delegação; o relógio atualiza por temporizador, mas a frequência de atualização integral da fonte varia por tela.

## Painel de disponibilidade para monitor

1. **Objetivo:** exibir ocupação atual, tempo restante e próxima reserva em tela de acompanhamento.
2. **Comportamento observado:** cartões informam sala ocupada ou disponível, pessoa associada, tempo até liberação e próxima reserva.
3. **Componentes:** tela dedicada, três controles HTML Text, imagens incorporadas e temporizador.
4. **Tecnologia:** Power Apps, Power Fx e HTML/CSS incorporados; um HTML externo oferece tela cheia e recarga a cada 60 segundos.
5. **Regras verificadas:** consulta reservas ativas do dia, converte horários em minutos e escolhe a reserva atual ou a próxima por ordenação.
6. **Limitações:** três salas e alguns indicadores superiores aparecem definidos diretamente na fórmula; a recarga de dados em monitor depende do wrapper externo. A captura fornecida não foi publicada por conter nomes não validados como fictícios.

## Nova reserva

1. **Objetivo:** registrar uma reunião em uma sala disponível.
2. **Comportamento observado:** usuário escolhe sala, data, início, fim, título/finalidade e participantes; um resumo antecede a confirmação.
3. **Componentes:** galeria de salas, seletor de data, seletores de horário, campo de texto, combos de finalidade e usuários, resumo e confirmação.
4. **Tecnologia:** Power Apps, Power Fx, SharePoint e Microsoft 365 Users.
5. **Regras verificadas:** intervalos de 15 minutos; sala ativa e disponível; campos obrigatórios; fim posterior ao início; bloqueio de passado; atualização da fonte; bloqueio de qualquer sobreposição; persistência com situação ativa.
6. **Limitações:** não há transação confirmada para concorrência; participantes são persistidos em texto delimitado; a finalidade usa o primeiro item selecionado.

## Minhas reservas

1. **Objetivo:** acompanhar compromissos futuros e históricos relacionados ao usuário.
2. **Comportamento observado:** abas de próximas e histórico, papel de organizador/convidado, situação, detalhes e ações contextuais.
3. **Componentes:** galerias, filtros mensais, modal de detalhes, modal de confirmação e editor de convidados.
4. **Tecnologia:** Power Apps, Power Fx, SharePoint e Microsoft 365 Users.
5. **Regras verificadas:** futuras exigem situação ativa e fim posterior ao momento atual; histórico inclui encerradas ou não ativas no mês; organizador cancela e edita convidados; convidado remove a própria participação.
6. **Limitações:** edição de convidados altera a lista do SharePoint; não foi confirmado sincronismo com calendário do Outlook.

## Todas as reservas

1. **Objetivo:** permitir visão administrativa das reservas recentes.
2. **Comportamento observado:** carrega até 90 dias anteriores, classifica reservas e permite filtros, detalhes, convidados e cancelamento.
3. **Componentes:** galeria administrativa, botões de filtro, modal de detalhes e confirmação.
4. **Tecnologia:** Power Apps, Power Fx, SharePoint e Microsoft 365 Users.
5. **Regras verificadas:** tela e painel são condicionados por lista de e-mails configurada no aplicativo; modo de simulação bloqueia gravações nas fórmulas verificadas.
6. **Limitações:** a lista de e-mails no cliente não substitui autorização no SharePoint; o acesso deve ser validado por permissões externas.

## Configurações

1. **Objetivo:** administrar a oferta de salas e seus recursos.
2. **Comportamento observado:** edição de capacidade, estado da sala e disponibilidade de câmera, ar-condicionado e TV; descarte ou salvamento em lote.
3. **Componentes:** galeria editável, campo numérico, seletor de estado, toggles, botões de salvar/descartar e combo de simulação.
4. **Tecnologia:** Power Apps, Power Fx, SharePoint e Microsoft 365 Users.
5. **Regras verificadas:** estados mapeados para ativo, manutenção e indisponível; recursos são convertidos em flags; erros de `Patch` geram mensagem.
6. **Limitações:** controle de acesso da interface é uma allowlist embutida; governança e auditoria das mudanças não foram verificadas.

## Notificações automatizadas

1. **Objetivo:** informar participantes e organizador sobre mudanças de reserva.
2. **Comportamento observado:** trata criação, mudança de participantes, edição e cancelamento.
3. **Componentes:** gatilho do SharePoint, consulta de mudanças, condições, composição/filtragem de participantes e ações de envio.
4. **Tecnologia:** Power Automate, SharePoint e Office 365 Outlook.
5. **Regras verificadas:** nova versão `1.0` aciona mensagem de criação; alteração da coluna de participantes calcula adicionados/removidos; cancelamento exige mudança da situação para cancelado.
6. **Limitações:** a condição exportada para edição compara campos do item com o booleano `true`, o que aparenta erro e precisa de teste; não há ações de calendário confirmadas; tratamento explícito de falhas e retentativas customizadas não foi identificado.
