---
title: Azure Monitor-arbetsböcker skapar parametrar
description: Förenkla komplex rapportering med färdiga och anpassade parameteriserade arbetsböcker
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 3e7dda85f1f890d5ae0eb4722c3e028b373fdcab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658225"
---
# <a name="workbook-parameters"></a>Arbetsboksparametrar

Parametrar tillåter arbetsboksförfattare att samla in indata från konsumenterna och referera till dem i andra delar av arbetsboken – vanligtvis för att begränsa resultatuppsättningen eller ställa in rätt visuellt objekt. Det är en viktig funktion som gör det möjligt för författare att bygga interaktiva rapporter och upplevelser. 

Arbetsböcker kan du styra hur dina parameterkontroller presenteras för konsumenter - textruta kontra listruta ner, enkel-kontra multi-select, värden från text, JSON, KQL eller Azure Resource Graph, etc.  

Parametertyper som stöds är:
* [Tid](workbooks-time.md) - tillåter en användare att välja mellan förifyllda tidsintervall eller välja ett anpassat intervall
* [Listruta](workbooks-dropdowns.md) - tillåter en användare att välja från ett värde eller en uppsättning värden
* [Text](workbooks-text.md) - tillåter en användare att skriva godtycklig text
* [Resurs](workbooks-resources.md) - tillåter en användare att välja en eller flera Azure-resurser
* [Prenumeration](workbooks-resources.md) - tillåter en användare att välja en eller flera Azure-prenumerationsresurser
* Resurstyp - tillåter en användare att välja ett eller flera Azure-resurstypvärden
* Plats - tillåter en användare att välja ett eller flera Azure-platsvärden

Dessa parametervärden kan refereras i andra delar av arbetsböcker antingen via bindningar eller värdeexpansioner.

## <a name="creating-a-parameter"></a>Skapa en parameter
1. Börja med en tom arbetsbok i redigeringsläge.
2. Välj _Lägg till parametrar_ i länkarna i arbetsboken.
3. Klicka på knappen _Lägg till parameter._
4. I det nya parameterfönstret som dyker upp anger du:
    1. Parameternamn: `TimeRange` *(observera att __parameternamn__ **inte kan** innehålla blanksteg eller specialtecken)*
    2. Visningsnamn: `Time Range` *(dock kan __visningsnamn__ innehålla blanksteg, specialtecken, emoji, etc.)*  
    2. Parametertyp:`Time range picker`
    3. Krävs:`checked`
    4. Tillgängliga tidsintervall: Senaste timmen, Senaste 12 timmarna, Senaste 24 timmarna, Senaste 48 timmarna, Senaste 3 dagarna, Senaste 7 dagarna och Tillåt val av anpassat tidsintervall
5. Välj Spara i verktygsfältet för att skapa parametern.

   ![Bild som visar skapandet av en parameter för tidsintervall](./media/workbooks-parameters/time-settings.png)

Så här kommer arbetsboken att se ut i läsläge, i stil med "piller".

   ![Bild som visar en parameter för tidsintervall i läsläge](./media/workbooks-parameters/parameters-time.png)

## <a name="referencing-a-parameter"></a>Referera till en parameter
### <a name="via-bindings"></a>Via bindningar
1. Lägg till en frågekontroll i arbetsboken och välj en Application Insights-resurs.
2. Öppna _listrutan Tidsintervall_ `Time Range` och välj alternativet i avsnittet Parametrar längst ned.
3. Detta binder parametern tidsintervall till diagrammets tidsintervall. Tidsomfånget för exempelfrågan är nu senaste 24 timmarna.
4. Kör fråga för att se resultaten

    ![Bild som visar en tidsintervallparameter som refereras via bindningar](./media/workbooks-parameters/time-binding.png)

### <a name="in-kql"></a>I KQL
1. Lägg till en frågekontroll i arbetsboken och välj en Application Insights-resurs.
2. Ange ett tidsomfattningsfilter i KQL med parametern:`| where timestamp {TimeRange}`
3. Detta utökar frågeutvärderingstiden `| where timestamp > ago(1d)`till , vilket är parameterns tidsintervallvärde.
4. Kör fråga för att se resultaten

    ![Bild som visar ett tidsintervall som refereras i KQL](./media/workbooks-parameters/time-in-code.png)

### <a name="in-text"></a>I text 
1. Lägg till en textkontroll i arbetsboken.
2. I markeringsgränsen anger du`The chosen time range is {TimeRange:label}`
3. Välj _Klar redigering_
4. Textkontrollen visar text: _Det valda tidsintervallet är Senaste 24 timmarna_

## <a name="parameter-options"></a>Alternativ för parameter
I avsnittet _I_ `label` text används parameterns i stället för dess värde. Parametrar exponerar olika sådana alternativ beroende på dess typ - t.ex.

Använd `Previews` avsnittet i fönstret _Redigera parameter_ för att se expansionsalternativen för parametern:

![Bild som visar ett alternativ för parameteralternativ för tidsintervall](./media/workbooks-parameters/time-previews.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](workbooks-visualizations.md) med att lära dig mer om arbetsböcker många avancerade visualiseringar alternativ.
* [Kontrollera](workbooks-access-control.md) och dela åtkomst till arbetsboksresurserna.
