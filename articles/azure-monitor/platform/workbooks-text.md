---
title: Textparametrar för Azure Monitor-arbetsböcker
description: Förenkla komplex rapportering med fördefinierade och anpassade parameteriserade arbetsböcker. Läs mer om arbetsbokstextparametrar.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c5fb585d0eb6aeb7866c2ab04b324ee31fe903ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658057"
---
# <a name="workbook-text-parameters"></a>Parametrar för arbetsbokstext

Textbox parametrar ger ett enkelt sätt att samla in textinmatning från arbetsboksanvändare. De används när det inte är praktiskt möjligt att använda en rullgardinsmenyn för att samla in indata (till exempel en godtycklig tröskel eller generiska filter). Arbetsböcker gör det möjligt för författare att hämta standardvärdet för textrutan från en fråga. Detta gör det möjligt för intressanta scenarier som att ange standardtröskeln baserat på p95 i måttet.

En vanlig användning av textrutor är som interna variabler som används av andra arbetsbokskontroller. Detta görs genom att använda en fråga för standardvärden och göra indatakontrollen osynlig i läsläge. En användare kanske till exempel vill att ett tröskelvärde ska komma från en formel (inte en användare) och sedan använda tröskelvärdet i efterföljande frågor.

## <a name="creating-a-text-parameter"></a>Skapa en textparameter
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`SlowRequestThreshold`
    2. Parametertyp:`Text`
    3. Krävs:`checked`
    4. Hämta standardvärde från frågan:`unchecked`
5. Välj Spara i verktygsfältet för att skapa parametern.

    ![Bild som visar skapandet av en textparameter](./media/workbooks-text/text-create.png)

Så här kommer arbetsboken att se ut i läsläge.

![Bild som visar en textparameter i läsläge](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Referera till en textparameter
1. Lägg till en frågekontroll i arbetsboken `Add query` genom att välja den blå länken och välja en Application Insights-resurs.
2. Lägg till det här kodavsnittet i rutan KQL:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Genom att använda textparametern med värdet 500 i kombination med frågekontrollen kör du frågan nedan:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Kör fråga för att se resultaten

    ![Bild som visar en textparameter som refereras i KQL](./media/workbooks-text/text-reference.png)


## <a name="setting-default-values"></a>Ange standardvärden
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn:`SlowRequestThreshold`
    2. Parametertyp:`Text`
    3. Krävs:`checked`
    4. Hämta standardvärde från frågan:`checked`
5. Lägg till det här kodavsnittet i rutan KQL:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Den här frågan anger standardvärdet för textrutan till den 95:e percentilen för alla begäranden i appen.
6. Kör fråga för att se resultatet
7. Välj Spara i verktygsfältet för att skapa parametern.

    ![Bild som visar en textparameter med standardvärde från KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Medan det här exemplet frågar Application Insights-data kan metoden användas för alla loggbaserade datakällor - Log Analytics, Azure Resource Graph, etc.

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
