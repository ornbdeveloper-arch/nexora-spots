# Exemplos demonstrativos de Power Fx

Os exemplos abaixo foram escritos de forma independente, com nomes genéricos. Eles não são uma reprodução das fórmulas corporativas nem um pacote pronto para implantação. O uso público continua pendente de revisão humana e autorização.

> As fórmulas usam separadores da configuração regional em inglês. Em ambientes com regionalização diferente, o Power Apps pode converter vírgulas e separadores de instruções automaticamente.

## Converter um horário em minutos

**Objetivo:** transformar `HH:mm` em um número comparável.

```powerfx
With(
    { horario: ddInicio.Selected.Horario },
    Value(Left(horario, 2)) * 60 + Value(Mid(horario, 4, 2))
)
```

**Observações:** pressupõe valor com cinco caracteres, como `09:30`.

**Dependências:** dropdown `ddInicio` com coluna `Horario`.

## Verificar conflito de horários

**Objetivo:** detectar qualquer sobreposição entre a nova reserva e reservas ativas.

```powerfx
With(
    {
        novoInicio: Value(Left(ddInicio.Selected.Horario, 2)) * 60 +
            Value(Mid(ddInicio.Selected.Horario, 4, 2)),
        novoFim: Value(Left(ddFim.Selected.Horario, 2)) * 60 +
            Value(Mid(ddFim.Selected.Horario, 4, 2))
    },
    !IsEmpty(
        Filter(
            ReservasDemo,
            SalaId = galSalas.Selected.Id &&
            Data = dpData.SelectedDate &&
            Status = "Ativa" &&
            novoInicio < FimMin &&
            novoFim > InicioMin
        )
    )
)
```

**Observações:** intervalos adjacentes são aceitos. Para uma fonte remota, revise delegação e tipos das colunas.

**Dependências:** fonte demonstrativa `ReservasDemo` com `InicioMin` e `FimMin` numéricos.

## Filtrar próximas reservas do usuário

**Objetivo:** reunir reservas futuras em que o usuário é organizador ou participante.

```powerfx
With(
    { emailAtual: Lower(User().Email) },
    Filter(
        ReservasDemo,
        Status = "Ativa" &&
        InicioDataHora >= Now() &&
        (
            Lower(OrganizadorEmail) = emailAtual ||
            emailAtual in ForAll(
                Split(Lower(Coalesce(Participantes, "")), ";"),
                Trim(Value)
            )
        )
    )
)
```

**Observações:** texto delimitado é simples, mas não é o melhor modelo para integridade ou delegação.

**Dependências:** `InicioDataHora`, `OrganizadorEmail` e `Participantes`.

## Identificar se uma sala está ocupada

**Objetivo:** retornar um booleano para a ocupação no momento atual.

```powerfx
With(
    { agoraMin: Hour(Now()) * 60 + Minute(Now()) },
    !IsBlank(
        LookUp(
            colReservasHoje,
            SalaId = ThisItem.Id &&
            InicioMin <= agoraMin &&
            FimMin > agoraMin
        )
    )
)
```

**Observações:** usa intervalo semiaberto `[início, fim)`.

**Dependências:** coleção `colReservasHoje` normalizada em minutos.

## Calcular tempo restante

**Objetivo:** exibir horas e minutos até o fim de uma reserva.

```powerfx
With(
    { restante: Max(ThisItem.FimMin - (Hour(Now()) * 60 + Minute(Now())), 0) },
    If(
        restante >= 60,
        RoundDown(restante / 60, 0) & "h " & Mod(restante, 60) & "min",
        restante & "min"
    )
)
```

**Observações:** `Max` evita valores negativos após o término.

**Dependências:** campo numérico `FimMin`.

## Validar um formulário de reserva

**Objetivo:** impedir envio incompleto ou cronologicamente inválido.

```powerfx
With(
    {
        inicio: Value(Left(ddInicio.Selected.Horario, 2)) * 60 +
            Value(Mid(ddInicio.Selected.Horario, 4, 2)),
        fim: Value(Left(ddFim.Selected.Horario, 2)) * 60 +
            Value(Mid(ddFim.Selected.Horario, 4, 2))
    },
    If(
        IsBlank(galSalas.Selected) ||
        IsBlank(dpData.SelectedDate) ||
        IsBlank(Trim(txtAssunto.Text)) ||
        IsBlank(ddInicio.Selected.Horario) ||
        IsBlank(ddFim.Selected.Horario),
        Notify("Preencha os campos obrigatórios.", NotificationType.Warning),
        inicio >= fim,
        Notify("O término deve ser posterior ao início.", NotificationType.Warning),
        Notify("Formulário válido para a próxima etapa.", NotificationType.Information)
    )
)
```

**Observações:** a validação de conflito deve ocorrer novamente imediatamente antes da gravação.

**Dependências:** controles demonstrativos de sala, data, assunto e horários.

## Atualizar uma coleção local

**Objetivo:** recarregar reservas ativas do dia com colunas calculadas.

```powerfx
ClearCollect(
    colReservasHoje,
    AddColumns(
        Filter(
            ReservasDemo,
            Data = Today() && Status = "Ativa"
        ),
        InicioMin,
        Value(Left(HoraInicio, 2)) * 60 + Value(Mid(HoraInicio, 4, 2)),
        FimMin,
        Value(Left(HoraFim, 2)) * 60 + Value(Mid(HoraFim, 4, 2))
    )
)
```

**Observações:** `AddColumns` sobre fonte remota pode encontrar limites de delegação. Prefira colunas numéricas ou data/hora na origem quando o volume for relevante.

**Dependências:** fonte demonstrativa `ReservasDemo`.
