# Inventário sanitizado

Este inventário descreve os nove artefatos de entrada encontrados em 16 de novembro de 2026. Nomes corporativos, identificadores e detalhes sensíveis foram omitidos. O inventário integral está em `internal/inventario.md`, fora do Git.

| Artefato | Tipo | Tamanho | Finalidade provável | Analisável | Risco |
|---|---:|---:|---|---|---|
| Aplicativo Canvas original | `.msapp` | 32.250.747 bytes | Aplicativo Power Apps | Sim; contêiner ZIP com fontes `.pa.yaml` | Alto |
| Exportação de automação | `.zip` | 6.524 bytes | Fluxo do Power Automate | Sim; manifesto e definição JSON | Alto |
| Wrapper de painel | `.html` | 4.209 bytes | Exibição em tela cheia e recarga periódica | Sim | Alto |
| Configuração de salas | PNG | 1.534.890 bytes | Representação visual da administração | Sim, visualmente | Alto |
| Notificação de cancelamento | PNG | 1.541.572 bytes | Representação visual de e-mail | Sim, visualmente | Médio |
| Minhas reservas | PNG | 1.211.324 bytes | Representação visual de reservas do usuário | Sim, visualmente | Baixo, ainda sujeito a revisão |
| Painel em tempo real | PNG | 1.524.567 bytes | Representação visual do painel de disponibilidade | Sim, visualmente | Alto |
| Tela inicial | PNG | 1.318.199 bytes | Representação visual da visão geral | Sim, visualmente | Alto |
| Criação de reserva | PNG | 1.666.679 bytes | Representação visual do formulário | Sim, visualmente | Alto |

## Resultado da triagem

- O `.msapp`, o ZIP e o HTML não são publicáveis em sua forma original.
- Todas as imagens possuem metadados C2PA que indicam mídia algorítmica da OpenAI; elas não constituem prova direta de execução do app.
- Cinco das seis imagens foram retidas da área pública por conterem identificadores ou dados não comprovadamente fictícios.
- `screenshots/minhas-reservas.png` é a única cópia visual selecionada, sem identificadores pessoais visíveis, mas ainda depende de aprovação humana.
- Não foram encontradas exportações YAML ou JSON soltas na entrada; elas existiam dentro dos pacotes.
- Não havia documentos técnicos prévios nem arquivos Power Fx separados.

## Método e limitações

O aplicativo foi analisado por cópia local de seu contêiner, sem instalação de ferramentas. O Power Platform CLI não estava disponível. As fontes internas do próprio pacote permitiram revisar telas e fórmulas, mas não comprovam permissões reais do SharePoint, configuração do tenant, execução em produção ou estado atual das conexões.
