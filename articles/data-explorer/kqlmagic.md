---
title: Använd en Jupyter-anteckningsbok för att analysera data i Datautforskaren i Azure
description: Det här avsnittet visar vi hur du analyserar data i Datautforskaren i Azure med en Jupyter-anteckningsbok och Kqlmagic-tillägget.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 896a5d13279c15f0035f214da3d5a7d7e6f1861f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758423"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Använda ett tillägg för Jupyter Notebook och Kqlmagic för att analysera data i Datautforskaren i Azure

Jupyter Notebook är ett webbprogram med öppen källkod som gör det möjligt att skapa och dela dokument med live-koden, formler, visualiseringar och löpande text. Användning innehåller Datarensning och transformering, numeriska simulering, statistisk modellering, datavisualisering och maskininlärning.
[Jupyter Notebook](https://jupyter.org/) stöder magic funktioner som utökar funktionerna i kernel genom att stödja fler kommandon. KQL magic är ett kommando som utökar funktionerna i Python-kerneln i Jupyter-anteckningsbok så att du kan köra frågor med Kusto språk internt. Du enkelt kombinera Python och Kusto frågespråk för att fråga och visualisera data med hjälp av omfattande Plot.ly bibliotek som är integrerad med `render` kommandon. Datakällor för att köra frågor som stöds. Dessa datakällor är bland annat Azure Data Explorer, en tjänst för Kunskapsutveckling av snabba och skalbara data för log och telemetridata, samt Azure Monitor-loggar och Application Insights. KQL magic fungerar även med Azure anteckningsböcker, Jupyter labb och Visual Studio Code Jupyter-tillägget.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Organisationens e-postkonto som är medlem av Azure Active Directory (AAD).
- Jupyter Notebook installerad på den lokala datorn eller använda Azure-anteckningsböcker och klona exemplet [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Installera KQL magic biblioteket

1. Installera KQL magic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > När du använder Azure anteckningsböcker, krävs inte det här steget.

1. Läs in KQL magic:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Ansluta till Azure Data Explorer hjälp-kluster

Använd följande kommando för att ansluta till den *exempel* databasen finns på den *hjälpa* kluster. För icke - Microsoft AAD-användare, ersätter du klientnamnet `Microsoft.com` med AAD-klient.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Fråga och visualisera

Fråga data med hjälp av den [rendera operatorn](/azure/kusto/query/renderoperator) och visualisera data med hjälp av ploy.ly-biblioteket. Tillhandahåller en integrerad upplevelse som använder interna KQL frågan och visualisering. Kqlmagic stöder de flesta diagram utom `timepivot`, `pivotchart`, och `ladderchart`. Rendering stöds med alla attribut utom `kind`, `ysplit`, och `accumulate`. 

### <a name="query-and-render-piechart"></a>Fråga efter och rendera piechart

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Fråga efter och visa timechart

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Dessa diagram är interaktiva. Välj ett tidsintervall för att zooma in en viss tid.

### <a name="customize-the-chart-colors"></a>Anpassa Diagramfärger som

Om du inte gillar standardfärgpaletten, anpassa de diagram med hjälp av paletten alternativ. Tillgängliga färgpalett finns här: [Välj färgpalett för KQL magic diagrammet frågeresultatet](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. En lista över färgpalett:

    ```python
    %kql --palettes -popup_window
    ```

1. Välj den `cool` färgpalett och rendera frågan igen:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parameterisera en fråga med Python

KQL magic möjliggör enkel utbytet mellan Kusto-frågespråk och Python. Mer information: [Parameterisera KQL magic frågan med Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Använd en Python-variabel i KQL frågan

Du kan använda värdet för en Python-variabel i frågan för att filtrera data:

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Konvertera frågeresultaten till Pandas-DataFrame

Du kan komma åt resultaten av en KQL fråga i Pandas-DataFrame. Få åtkomst till de senast utförda frågeresultat av variabeln `_kql_raw_result_` och enkelt konvertera resultatet till Pandas-DataFrame på följande sätt:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exempel

I många scenarier för dataanalys, kanske du vill skapa återanvändbara anteckningsböcker som innehåller många frågor och skicka resultaten från en fråga till efterföljande frågor. I exemplet nedan används Python-variabeln `statefilter` att filtrera data.

1. Köra en fråga om du vill visa de översta 10 delstaterna med maximal `DamageProperty`:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Kör en fråga för att extrahera det översta läget och ställa in den i en Python-variabel:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Kör en fråga med hjälp av den `let` utdrags- och Python-variabeln:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Köra hjälpkommandot:

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Nästa steg

Kör hjälpkommandot för att utforska följande exempelanteckningsböcker som innehåller alla funktioner som stöds:
- [Kom igång med KQL magic för Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Kom igång med KQL magic för Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Kom igång med KQL magic för Azure Monitor-loggar](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize KQL magic frågan med Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Välj färgpalett för KQL magic diagrammet frågeresultatet](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
