---
title: Använda en Jupyter-anteckningsbok för att analysera data i Azure Data Explorer
description: Det här avsnittet visar hur du analyserar data i Azure Data Explorer med hjälp av en Jupyter Notebook och Kqlmagic-tillägget.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 83902ea5a3e73603311a0c469126ed603d0ebd16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064877"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Använda ett Jupyter-anteckningsbok- och Kqlmagic-tillägg för att analysera data i Azure Data Explorer

Jupyter Notebook är ett webbprogram med öppen källkod som låter dig skapa och dela dokument som innehåller live-kod, ekvationer, visualiseringar och berättande text. Användningen omfattar datarengöring och omvandling, numerisk simulering, statistisk modellering, datavisualisering och maskininlärning.
[Jupyter Notebook](https://jupyter.org/) stöder magiska funktioner som utökar kärnans funktioner genom att stödja ytterligare kommandon. KQL magi är ett kommando som utökar funktionerna i Python-kärnan i Jupyter Notebook så att du kan köra Kusto språkfrågor inbyggt. Du kan enkelt kombinera Python och Kusto frågespråk för att fråga och `render` visualisera data med hjälp av omfattande Plot.ly bibliotek integrerat med kommandon. Datakällor för att köra frågor stöds. Dessa datakällor inkluderar Azure Data Explorer, en snabb och mycket skalbar datautforskningstjänst för logg- och telemetridata samt Azure Monitor-loggar och Application Insights. KQL magi fungerar också med Azure Notebooks, Jupyter Lab och Visual Studio Code Jupyter förlängning.

## <a name="prerequisites"></a>Krav

- Organisations-e-postkonto som är medlem i Azure Active Directory (AAD).
- Jupyter Notebook installeras på din lokala dator eller använder Azure-anteckningsböcker och klonar exemplet [på Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Installera KQL magiska bibliotek

1. Installera KQL magi:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > När du använder Azure-anteckningsböcker krävs inte det här steget.

1. Ladda KQL magi:

    ```python
    %reload_ext Kqlmagic
    ```
    > [!NOTE]
    > Ändra kernelversionen till Python 3.6 genom att klicka på Kernel > Change Kernel > Python 3.6
    
## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Ansluta till hjälpklustret för Azure Data Explorer

Använd följande kommando för att ansluta till *exempeldatabasen* som finns i hjälpklustret. *Help* För användare som inte är Microsoft `Microsoft.com` AAD ersätter du klientnamnet med din AAD-klientorganisation.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Fråga och visualisera

Fråga data med hjälp av [renderingsoperatorn](/azure/kusto/query/renderoperator) och visualisera data med hjälp av ploy.ly-biblioteket. Den här frågan och visualiseringen innehåller en integrerad upplevelse som använder inbyggd KQL. Kqlmagic stöder de `timepivot` `pivotchart`flesta `ladderchart`diagram utom , och . Render stöds med alla `kind`attribut `ysplit`utom `accumulate`, och . 

### <a name="query-and-render-piechart"></a>Fråga och återge cirkeldiagram

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Tidsschema för fråga och återge

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Dessa diagram är interaktiva. Välj ett tidsintervall för att zooma in i en viss tid.

### <a name="customize-the-chart-colors"></a>Anpassa diagramfärgerna

Om du inte gillar standardfärgpaletten anpassar du diagrammen med palettalternativ. De tillgängliga paletterna hittar du här: [Välj färgpalett för ditt KQL-magiska frågediagramresultat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)

1. För en lista över paletter:

    ```python
    %kql --palettes -popup_window
    ```

1. Markera `cool` färgpaletten och återge frågan igen:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parameterisera en fråga med Python

KQL magi möjliggör enkel utbyte mellan Kusto frågespråk och Python. Om du vill veta mer: [Parameterisera din KQL-magifråga med Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb)

### <a name="use-a-python-variable-in-your-kql-query"></a>Använda en Python-variabel i KQL-frågan

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

### <a name="convert-query-results-to-pandas-dataframe"></a>Konvertera frågeresultat till Pandas DataFrame

Du kan komma åt resultatet av en KQL-fråga i Pandas DataFrame. Få tillgång till de senast `_kql_raw_result_` utförda frågeresultaten efter variabel och konvertera resultaten enkelt till Pandas DataFrame enligt följande:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Exempel

I många analysscenarier kanske du vill skapa återanvändbara anteckningsböcker som innehåller många frågor och mata in resultaten från en fråga till efterföljande frågor. I exemplet nedan används `statefilter` Python-variabeln för att filtrera data.

1. Kör en fråga om du vill visa `DamageProperty`de 10 översta lägena med högst:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Kör en fråga för att extrahera det översta tillståndet och ange den i en Python-variabel:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Kör en fråga `let` med hjälp av uttrycket och Python-variabeln:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Kör hjälpkommandot:

    ```python
    %kql --help "help"
    ```

> [!TIP]
> För att få information om `%config Kqlmagic`alla tillgängliga konfigurationer använd . Om du vill felsöka och fånga Upp Kusto-fel, till exempel anslutningsproblem och felaktiga frågor, använder du`%config Kqlmagic.short_errors=False`

## <a name="next-steps"></a>Nästa steg

Kör hjälpkommandot för att utforska följande exempelanteckningsböcker som innehåller alla funktioner som stöds:
- [Komma igång med KQL-magi för Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) 
- [Kom igång med KQL magi för Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) 
- [Komma igång med KQL-magi för Azure Monitor-loggar](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) 
- [Parametrize din KQL magiska fråga med Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) 
- [Välj färgpalett för ditt KQL-magiska frågediagramresultat](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb)
