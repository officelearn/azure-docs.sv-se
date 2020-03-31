---
title: Tidsparametrar för Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med färdiga och anpassade parameteriserade arbetsböcker
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658021"
---
# <a name="workbook-time-parameters"></a>Tidsparametrar för arbetsbok

Tidsparametrar tillåter användare att ställa in tidskontexten för analys och används av nästan alla rapporter. Det är relativt enkelt att ställa in och använda - så att författarna kan ange de tidsintervall som ska visas i listrutan, inklusive alternativet för anpassade tidsintervall. 

## <a name="creating-a-time-parameter"></a>Skapa en tidsparameter
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`TimeRange`
    2. Parametertyp:`Time range picker`
    3. Krävs:`checked`
    4. Tillgängliga tidsintervall: Senaste timmen, Senaste 12 timmarna, Senaste 24 timmarna, Senaste 48 timmarna, Senaste 3 dagarna, Senaste 7 dagarna och Tillåt val av anpassat tidsintervall
5. Välj Spara i verktygsfältet för att skapa parametern.

    ![Bild som visar skapandet av en parameter för tidsintervall](./media/workbooks-time/time-settings.png)

Så här kommer arbetsboken att se ut i läsläge.

![Bild som visar en parameter för tidsintervall i läsläge](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Referera till en tidsparameter
### <a name="via-bindings"></a>Via bindningar
1. Lägg till en frågekontroll i arbetsboken och välj en Application Insights-resurs.
2. De flesta arbetsbokskontroller stöder en _tidsintervallväljare._ Öppna listrutan _Tidsintervall_ och `{TimeRange}` välj gruppen parametrar för tids ringde längst ned.
3. Detta binder parametern tidsintervall till diagrammets tidsintervall. Tidsomfånget för exempelfrågan är nu senaste 24 timmarna.
4. Kör fråga för att se resultaten

    ![Bild som visar en tidsintervallparameter som refereras via bindningar](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>I KQL
1. Lägg till en frågekontroll i arbetsboken och välj en Application Insights-resurs.
2. Ange ett tidsomfattningsfilter i KQL med parametern:`| where timestamp {TimeRange}`
3. Detta utökar frågeutvärderingstiden `| where timestamp > ago(1d)`till , vilket är parameterns tidsintervallvärde.
4. Kör fråga för att se resultaten

    ![Bild som visar ett tidsintervall som refereras i KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>I text 
1. Lägg till en textkontroll i arbetsboken.
2. I markeringsgränsen anger du`The chosen time range is {TimeRange:label}`
3. Välj _Klar redigering_
4. Textkontrollen visar text: _Det valda tidsintervallet är Senaste 24 timmarna_

## <a name="time-parameter-options"></a>Alternativ för tidsparameter
| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etikett för tidsintervall | Senaste 24 timmarna |
| `{TimeRange:label}` | Etikett för tidsintervall | Senaste 24 timmarna |
| `{TimeRange:value}` | Värde för tidsintervall | > sedan(1d) |
| `{TimeRange:query}` | Fråga om tidsintervall | > sedan(1d) |
| `{TimeRange:start}` | Starttid för tidsintervall | 2019-03-20 16:18 |
| `{TimeRange:end}` | Sluttid för tidsintervall | 2019-03-21 16:18 |
| `{TimeRange:grain}` | Tidsintervall korn | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Använda parameteralternativ i en fråga
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
