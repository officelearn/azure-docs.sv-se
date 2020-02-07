---
title: Använda en Jupyter Notebook för att analysera data i Azure Datautforskaren
description: Det här avsnittet visar hur du analyserar data i Azure Datautforskaren med hjälp av en Jupyter Notebook och Kqlmagic-tillägget.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064877"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Använd ett Jupyter Notebook-och Kqlmagic-tillägg för att analysera data i Azure Datautforskaren

Jupyter Notebook är ett webb program med öppen källkod som gör att du kan skapa och dela dokument som innehåller Live-kod, ekvationer, visualiseringar och text. Användningen omfattar data rensning och transformering, numerisk simulering, statistisk modellering, data visualisering och maskin inlärning.
[Jupyter Notebook](https://jupyter.org/) stöder Magic Functions som utökar funktionerna i kerneln genom att stödja ytterligare kommandon. KQL Magic är ett kommando som utökar funktionerna i python-kärnan i Jupyter Notebook så att du kan köra Kusto-språk frågor internt. Du kan enkelt kombinera python-och Kusto-frågespråket för att fråga och visualisera data med hjälp av Rich Plot.ly-bibliotek som är integrerat med `render`-kommandon. Data källor för att köra frågor stöds. Dessa data källor innehåller Azure Datautforskaren, en snabb och hög skalbar tjänst för data granskning för logg-och telemetridata, samt Azure Monitor loggar och Application Insights. KQL Magic fungerar också med Azure Notebooks, Jupyter Lab och Visual Studio Code Jupyter-tillägget.

## <a name="prerequisites"></a>Förutsättningar

- Organisationens e-postkonto som är medlem i Azure Active Directory (AAD).
- Jupyter Notebook installerat på den lokala datorn eller Använd Azure Notebooks och klona exempel [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Installera KQL Magic Library

1. Installera KQL Magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Det här steget krävs inte när du använder Azure Notebooks.

1. Läs in KQL Magic:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Ändra kernel-versionen till python 3,6 genom att klicka på kernel > ändra kernel > python 3,6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Anslut till Azure Datautforskaren hjälp kluster

Använd följande kommando för att ansluta till *exempel* databasen som finns i *Hjälp* klustret. För AAD-användare som inte kommer från Microsoft ersätter du klient namnet `Microsoft.com` med din AAD-klient.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Fråga och visualisera

Fråga data med hjälp av [operatorn Render](/azure/kusto/query/renderoperator) och visualisera data med Ploy.ly-biblioteket. Den här frågan och visualiseringen tillhandahåller en integrerad upplevelse som använder interna KQL. Kqlmagic stöder de flesta diagram utom `timepivot`, `pivotchart`och `ladderchart`. Rendering stöds med alla attribut utom `kind`, `ysplit`och `accumulate`. 

### <a name="query-and-render-piechart"></a>Fråga och rendera piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Fråga och rendera timechart

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> De här diagrammen är interaktiva. Välj ett tidsintervall för att zooma in en angiven tid.

### <a name="customize-the-chart-colors"></a>Anpassa diagrammets färger

Om du inte gillar standardfärgpaletten anpassar du diagrammen med alternativ för palett. Tillgängliga paletter finns här: [Välj färgpalett för ditt KQL Magic Query Chart-resultat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. För en lista över paletter:

    ```python
    %kql --palettes -popup_window
    ```

1. Välj `cool` färgpalett och återge frågan igen:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parameterisera en fråga med python

KQL Magic möjliggör enkel utbyte mellan Kusto-frågespråket och python. Mer information: [Parameterisera din KQL Magic-fråga med python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Använda en python-variabel i din KQL-fråga

Du kan använda värdet för en python-variabel i din fråga för att filtrera data:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Konvertera frågeresultat till Pandas DataFrame

Du kan komma åt resultatet av en KQL-fråga i Pandas DataFrame. Få åtkomst till de senast utförda frågeresultaten efter variabel `_kql_raw_result_` och enkelt konvertera resultaten till Pandas DataFrame på följande sätt:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exempel

I många analys scenarier kanske du vill skapa återanvändbara antecknings böcker som innehåller många frågor och mata in resultaten från en fråga till efterföljande frågor. I exemplet nedan används python-variabeln `statefilter` för att filtrera data.

1. Kör en fråga för att visa de 10 högsta tillstånden med maximalt `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Kör en fråga för att extrahera det översta läget och Ställ in det i en python-variabel:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Köra en fråga med hjälp av `let`-instruktionen och python-variabeln:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Kör hjälp kommandot:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> För att få information om alla tillgängliga konfigurationer använder `%config Kqlmagic`. Om du vill felsöka och avbilda Kusto-fel, till exempel anslutnings problem och felaktiga frågor, använder du `%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Nästa steg

Kör kommandot help för att utforska följande exempel på antecknings böcker som innehåller alla funktioner som stöds:
- [Kom igång med KQL Magic för Azure Datautforskaren](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Kom igång med KQL Magic för Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Kom igång med KQL Magic för Azure Monitor loggar](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize din KQL Magic-fråga med python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Resultat för att välja färgpalett för ditt KQL Magic-frågeuttryck](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
