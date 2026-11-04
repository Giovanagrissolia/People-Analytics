# Medidas DAX

## Medidas

### Contratações
Quantidade total de contratos registrados na base.
```DAX
Contratações = COUNTROWS(fContratos)
```

### Demissões
Quantidade de contratos com situação "Demitido", contados pela data de afastamento (e não pela de admissão).
```DAX
Demissões = 
CALCULATE(
    COUNTROWS(fContratos),
    fContratos[Situação] = "Demitido",
    USERELATIONSHIP(dCalendario[Data], fContratos[Data Afastamento])
)
```

### Headcount
Quantidade de pessoas ativas na data final do período selecionado: admitidas até essa data e que ainda não tinham sido demitidas.
```DAX
Headcount = 
VAR vDataFinalContexto = MAX(dCalendario[Data])
RETURN
CALCULATE(
    COUNTROWS(fContratos),
    fContratos[Data Admissão] <= vDataFinalContexto,
    fContratos[Data Afastamento] > vDataFinalContexto || fContratos[Situação] <> "Demitido",
    ALL(dCalendario)
)
```

### Headcount v2
Versão alternativa do headcount: contratações acumuladas menos demissões acumuladas até a data final do período. Serve para conferir o resultado do Headcount.
```DAX
Headcount v2 = 
CALCULATE(
    [Contratações] - [Demissões],
    FILTER(
        ALL(dCalendario),
        dCalendario[Data] <= MAX(dCalendario[Data])
    )
)
```

### Massa Salarial
Soma dos salários das pessoas ativas na data final do período selecionado.
```DAX
Massa Salarial = //Somente para ativos!!!
VAR vDataFinalContexto = MAX(dCalendario[Data])
RETURN
CALCULATE(
    SUM(fContratos[Valor Salário]),
    FILTER(
        fContratos,
        fContratos[Data Admissão] <= vDataFinalContexto &&
        (fContratos[Data Afastamento] > vDataFinalContexto || fContratos[Cód Situação] <> 7)
    )
)
```

### Má Contratações
Quantidade de pessoas demitidas com menos de 60 dias entre a admissão e o afastamento.
```DAX
Má Contratações = 
CALCULATE(
    [Contratações],
    FILTER(
        fContratos,
        DATEDIFF(fContratos[Data Admissão], fContratos[Data Afastamento], DAY) < 60 &&
        fContratos[Situação] = "Demitido"
    )
)
```

### Absenteísmo
Valor financeiro (R$) das faltas, atrasos e atestados.
```DAX
Absenteísmo = 
CALCULATE(
    SUM(fFinanceiro[Valor]),
    dEvento[Categoria] IN {"Faltas", "Atrasos", "Atestados"}
)
```

### Horas Extras
Valor financeiro (R$) das horas extras.
```DAX
Horas Extras = 
CALCULATE(
    SUM(fFinanceiro[Valor]),
    dEvento[Categoria] = "Extras"
)
```

### Horas Normais
Valor financeiro (R$) das horas normais trabalhadas. É a base de comparação dos percentuais de absenteísmo e horas extras. O `+0` faz a medida retornar 0 em vez de vazio quando não há registros.
```DAX
Horas Normais = 
CALCULATE(
    SUM(fFinanceiro[Valor]),
    dEvento[Categoria] = "Normais"
)+0
```

### % Absenteísmo
Absenteísmo dividido pelas horas normais. Mostra quanto do valor pago em horas normais é perdido com faltas, atrasos e atestados.
```DAX
% Absenteísmo = DIVIDE([Absenteísmo], [Horas Normais], BLANK())
```

### % Horas Extras
Horas extras divididas pelas horas normais.
```DAX
% Horas Extras = DIVIDE([Horas Extras], [Horas Normais])
```

### % Má Contratações
Percentual das contratações que terminaram em demissão em menos de 60 dias.
```DAX
% Má Contratações = 
DIVIDE(
    [Má Contratações],
    [Contratações]
)
```

### % Turnover
Demissões do período divididas pelo headcount do dia anterior ao início do período.
```DAX
% Turnover = 
VAR vHCAnterior = 
CALCULATE(
    [Headcount],
    PREVIOUSDAY(dCalendario[Data])
)
VAR vHCNonBlank = COALESCE(vHCAnterior, [Headcount])
RETURN
DIVIDE(
    [Demissões],
    vHCAnterior
)
```

### % Turnover Ano Max
Turnover do ano mais recente do contexto, usado nos cartões de KPI.
```DAX
% Turnover Ano Max = 
VAR vAnoMax = MAX(dCalendario[Ano])
RETURN
CALCULATE(
    [% Turnover],
    dCalendario[Ano] = vAnoMax
)
```
