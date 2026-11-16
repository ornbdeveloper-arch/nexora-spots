# Análise do Power Apps

## Escopo da análise

O arquivo original foi copiado antes da inspeção. Como o Power Platform CLI não estava instalado, o pacote foi aberto localmente como ZIP e analisado a partir de suas fontes `.pa.yaml`, metadados JSON, referências de dados e resultado do App Checker. Nenhuma fonte corporativa foi copiada para a área publicável.

## Estrutura confirmada

| Tela | Responsabilidade observada |
|---|---|
| `screenStartAppDesktop` | visão inicial de salas e próximas reservas do usuário |
| `screenNewScheduleDesktop` | seleção de sala, data, horários, finalidade e participantes |
| `screenMySchedulesDesktop` | reservas futuras, histórico, detalhes, cancelamento, saída e convidados |
| `screenDashboardDesktop2` | painel de disponibilidade por sala |
| `screenConfigDesktop` | capacidade, situação, recursos e simulação de usuário |
| `screenUserDashboardDesktop` | consulta administrativa de todas as reservas |
| `screenAcessDeniedDesktop` | mensagem de acesso negado |
| `screenDashboardDesktopNew` | painel de monitor com cartões em HTML Text |

O componente `asideSidebar` concentra a navegação, a identidade visual, dados do usuário e o encerramento do modo de simulação.

## Estado e coleções

### Variáveis globais observadas

- usuário atual e e-mail efetivo, incluindo modo de simulação;
- sala selecionada;
- horário atual em minutos e instante do painel;
- imagens serializadas para o painel;
- reserva a abrir por ID.

### Variáveis de contexto observadas

Controlam abas, filtros, confirmação, detalhes, edição de convidados, mês do histórico, modal de indisponibilidade e reset da tela de configuração.

### Coleções principais

- espaços e configuração de salas;
- finalidades;
- intervalos de início e fim;
- reservas ativas, reservas do dia e reservas do usuário;
- histórico mensal;
- horários ocupados, inícios livres e finais livres;
- convidados em edição;
- visão administrativa de reservas.

## Regras Power Fx confirmadas

### Intervalos de horário

O `App.OnStart` monta horários em passos de 15 minutos. Os inícios vão de 08:30 a 18:15, e os finais de 08:45 a 18:30. A comparação é feita convertendo `HH:mm` em minutos desde meia-noite.

### Disponibilidade e conflito

Antes de gravar, o aplicativo atualiza a lista de reservas, filtra a mesma sala e data e considera somente situação ativa. Há conflito quando:

```text
novoInicio < fimExistente E novoFim > inicioExistente
```

Intervalos apenas adjacentes não conflitam. A validação também bloqueia sala inativa, em manutenção ou indisponível, campos obrigatórios vazios, fim não posterior ao início e horário passado.

### Criação

Sem conflito, `Patch` cria um item com título, sala, data, horários, duração, finalidade, participantes, situação ativa e solicitante. A operação usa `IfError` para informar falha ou sucesso.

### Reservas do usuário

O usuário é considerado relacionado à reserva se for solicitante ou se seu e-mail estiver na lista separada por ponto e vírgula. Reservas ativas ainda não encerradas formam a lista futura; reservas encerradas ou não ativas formam o histórico do mês.

O organizador pode cancelar e editar convidados. Um convidado pode sair; nesse caso seu e-mail é removido da lista de participantes.

### Administração

A tela de configuração altera capacidade, estado ativo/manutenção/indisponível e disponibilidade de câmera, ar-condicionado e TV. As linhas editadas são reunidas em coleção e persistidas por `ForAll` + `Patch`.

Existe uma visão de todas as reservas dos últimos 90 dias, com classificação calculada como ativa, realizada ou cancelada. Também há modo de simulação; fórmulas de gravação verificadas bloqueiam alterações enquanto ele está ativo.

## Integrações

- **SharePoint:** espaços, reservas, finalidades e outras listas presentes na conexão, embora nem todas tenham uso funcional confirmado nas telas analisadas.
- **Microsoft 365 Users:** pesquisa, perfil e foto.
- **Microsoft 365 Groups:** associação a grupo na seleção da tela inicial.
- **Power Automate:** acionado indiretamente por alterações na lista de reservas.

## Interface e atualização

- Layout alvo de 1366 × 768, orientação desktop/tablet.
- Galerias para salas e reservas; modais para detalhes, cancelamento e indisponibilidades.
- Controle HTML Text no painel de monitor.
- `Refresh` antes de cargas e da checagem final de conflito.
- Temporizadores atualizam o relógio; o wrapper externo recarrega o painel a cada 60 segundos.

## Qualidade observada

O `AppCheckerResult.sarif` empacotado contém 223 diagnósticos. Os grupos mais frequentes são:

- 81 ocorrências relacionadas a `TabIndex`;
- 42 controles sem rótulo acessível;
- 38 ocorrências relacionadas à visibilidade do foco;
- 22 recursos de mídia não usados;
- alertas de delegação e execução remota;
- uma ocorrência de `ForAll` com mutação.

Esses números refletem o resultado embarcado no pacote e precisam ser reexecutados no Power Apps Studio após qualquer correção.

## Limitações e riscos técnicos

- Permissões administrativas são determinadas por e-mails mantidos no cliente; a segurança real deve estar nas fontes e no compartilhamento.
- O limite padrão de obtenção de dados é 500 linhas e há alertas de delegação; filtros locais podem omitir registros em listas maiores.
- O armazenamento de participantes em texto delimitado dificulta integridade referencial e consultas delegáveis.
- A checagem de conflito é uma consulta seguida de gravação, sem transação atômica confirmada; duas gravações concorrentes ainda precisam ser testadas.
- Uma versão do painel usa números fixos para alguns indicadores superiores.
- O pacote registra um erro de parser nos metadados, embora as fontes tenham sido legíveis; a causa não foi confirmada.
- Não foi possível executar o aplicativo nem validar conexões, permissões ou comportamento em produção.
