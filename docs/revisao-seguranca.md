# Revisão de segurança e confidencialidade

## Resultado

Nenhuma credencial, chave ou token utilizável foi confirmado. Foram encontrados vários identificadores de ambiente, referências de conexão, dados pessoais e informações organizacionais que impedem a publicação dos artefatos originais.

Este relatório é sanitizado. O relatório detalhado, com nomes de arquivos e localizações internas, está em `internal/revisao-seguranca.md` e é ignorado pelo Git.

## Achados

| Severidade | Artefato/local aproximado | Risco | Ação aplicada ou recomendada |
|---|---|---|---|
| Crítico | Pacotes e textos pesquisados | Nenhum segredo confirmado por inspeção estática | Ainda executar varredura humana e ferramenta corporativa antes da publicação |
| Alto | Aplicativo Canvas: propriedades, referências de dados e fórmula inicial | URLs internas, IDs de tenant/site/lista/grupo/aplicativo, referências de conexão e e-mails | `.msapp` e fontes extraídas excluídos do Git |
| Alto | Exportação do fluxo: manifesto e definição | Conta de conexão, IDs do fluxo/conexão, site e lista | ZIP e conteúdo extraído excluídos do Git; não publicar exportação bruta |
| Alto | HTML auxiliar: atributo `src` do `iframe` | tenant, aplicativo, hint e URL de execução | HTML excluído do Git; eventual exemplo deve usar placeholders |
| Alto | Capturas de configuração, início e criação | nome e endereço de e-mail visíveis | imagens retidas em `entrada/` |
| Alto | Captura do painel em tempo real | nomes de pessoas não confirmados como fictícios | imagem retida em `entrada/` |
| Médio | Captura de e-mail | conteúdo de reunião e endereço funcional | imagem retida até confirmação de dados sintéticos |
| Médio | Fontes Power Fx | marca anterior, nomes de salas, horários, regras e allowlists administrativas | apenas descrições e exemplos independentes foram publicados |
| Baixo | Captura “Minhas reservas” | dados demonstrativos sem identificador pessoal visível | cópia incluída, ainda sujeita à revisão humana final |
| Baixo | Metadados das imagens | manifesto C2PA com UUIDs de proveniência e indicação de mídia algorítmica | preservar a proveniência e divulgar que são representações visuais |

## Observações de segurança da solução

- Allowlist de e-mails e decisão de interface no cliente não substituem permissões de dados.
- As permissões de SharePoint, do aplicativo e dos conectores não puderam ser validadas.
- Participantes são armazenados em texto delimitado, ampliando o risco de inconsistência e exposição em consultas.
- A verificação de conflito não é comprovadamente atômica.
- A exportação do fluxo usa referências de conexão embutidas e deveria migrar para solução com connection references e variáveis de ambiente.

## Conteúdo proibido para publicação

- `.msapp` original;
- ZIP do Power Automate;
- HTML original;
- extrações em `internal/analysis/`;
- relatórios internos;
- capturas retidas;
- URLs, GUIDs, contas, domínios, nomes e e-mails do ambiente de origem.

## Parecer

O conteúdo publicável atual foi reduzido a documentação sanitizada, exemplos demonstrativos e uma única imagem de baixo risco. Isso não elimina a necessidade de autorização jurídica, de propriedade intelectual e de privacidade. A publicação deve permanecer bloqueada até revisão humana formal.
