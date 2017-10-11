---
title: "Panelen referens för Vydesigner i OMS Log Analytics | Microsoft Docs"
description: "Vydesigner i logganalys kan du skapa anpassade vyer i OMS-konsol som innehåller olika visuella data i OMS-databasen. Den här artikeln innehåller en referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 2bce5c63b4c6edd3753f1b234cc4f493dcf53dea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="log-analytics-view-designer-tile-reference"></a>Log Analytics Vydesigner panelen referens
Vydesigner i logganalys kan du skapa anpassade vyer i OMS-konsol som innehåller olika visuella data i OMS-databasen. Den här artikeln innehåller en referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.

Andra artiklar som är tillgängliga för Vydesigner är:

* [Visa Designer](log-analytics-view-designer.md) -översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Referens för visualisering del](log-analytics-view-designer-parts.md) -referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), och sedan frågor i alla visningslägen måste skrivas den [nya frågespråket](https://go.microsoft.com/fwlink/?linkid=856078).  Alla vyer som har skapats innan arbetsytan har uppgraderats kommer att automtically konverteras.

I följande tabell visas de olika typerna av paneler som är tillgängliga i View Designer.  I avsnitten nedan beskrivs varje typ av panelen i detalj och deras egenskaper.

| sida vid sida | Beskrivning |
|:--- |:--- |
| [Antal](#number-tile) |Tal visar antalet poster från en fråga. |
| [Två tal](#two-numbers-tile) |Två enda tal visar antalet poster från två olika frågor. |
| [Ring](#donut-tile) |Ringdiagram baserat på en fråga med ett summary-värde i mitten. |
| [Linjediagram och callout](#line-chart-amp-callout-tile) |Linjediagram baserat på en fråga och en kommentar med ett summary-värde. |
| [Linjediagram](#line-chart-tile) |Linjediagram baserat på en fråga. |
| [Två tidslinjer](#two-timelines-tile) |Stapeldiagram med två serier varje baserat på en separat fråga. |

## <a name="number-tile"></a>Antalet sida vid sida
Den **nummer** visas ett tal visar antalet poster från en fråga i loggen och en etikett.

![Antalet sida vid sida](media/log-analytics-view-designer/tile-number.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Text som visas överst i panelen. |
| Beskrivning |Text som visas under namnet på sida vid sida. |
| **Sida vid sida** | |
| Förklaring |Text som visas under värdet. |
| Fråga |Frågan ska köras.  Antalet poster som returneras av frågan ska visas. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj om dataflöde kontroll ska aktiveras för panelen.  Detta ger ett alternativ visas om data inte är tillgänglig för panelen.  Detta används vanligtvis för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data är tillgängliga. |
| Fråga |Fråga om du vill köra för att kontrollera om data är tillgängliga för vyn.  Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet från den huvudsakliga frågan. |
| Meddelande |Meddelande som ska visas om dataflöde verifiering frågan returnerar inga data.  Om du anger inget meddelande *utför Assessment* visas. |


## <a name="two-numbers-tile"></a>Panelen två tal
Den **två tal** visas två tal visar antalet poster från två olika loggen frågor och en etikett för varje.

![Panelen två tal](media/log-analytics-view-designer/tile-two-numbers.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Text som visas överst i panelen. |
| Beskrivning |Text som visas under namnet på sida vid sida. |
| **Första sida vid sida** | |
| Förklaring |Text som visas under värdet. |
| Fråga |Frågan ska köras.  Antalet poster som returneras av frågan ska visas. |
| **Andra sida vid sida** | |
| Förklaring |Text som visas under värdet. |
| Fråga |Frågan ska köras.  Antalet poster som returneras av frågan ska visas. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj om dataflöde kontroll ska aktiveras för panelen.  Detta ger ett alternativ visas om data inte är tillgänglig för panelen.  Detta används vanligtvis för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data är tillgängliga. |
| Fråga |Fråga om du vill köra för att kontrollera om data är tillgängliga för vyn.  Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet från den huvudsakliga frågan. |
| Meddelande |Meddelande som ska visas om dataflöde verifiering frågan returnerar inga data.  Om du anger inget meddelande *utför Assessment* visas. |


## <a name="donut-tile"></a>Ring sida vid sida
Den **ringen** visas ett tal sammanfattats från en med en kolumn i en fråga i loggen.  Ringen visar grafiskt resultaten av de tre översta posterna.

![Ring sida vid sida](media/log-analytics-view-designer/tile-donut.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Text som visas överst i panelen. |
| Beskrivning |Text som visas under namnet på sida vid sida. |
| **Ring** | |
| Fråga |Frågan ska köras för ringen.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Detta är vanligtvis en fråga som använder den **mått** nyckelord för att sammanfatta resultat. |
| **Ring** |**> Center** |
| Text |Text som visas under värde i ringen. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde.<br><br>-Summan: Lägga till värden för alla poster med egenskapens värde.<br>-Procent: Procentandel av samlade värden från poster med egenskapsvärdet jämfört med de samlade värdena i alla poster. |
| Resultatvärden som används i center åtgärd |Du kan också klicka på plustecknet för att lägga till ett eller flera värden.  Resultatet av frågan kommer att begränsas till poster med egenskapsvärden som du anger.  Om inga värden har lagts till med än alla poster i frågan. |
| **Ring** |**> Ytterligare alternativ** |
| Färger |Färgen som visas för var och en av de tre översta egenskaperna.  Använd avancerade färg mappning om du vill ange alternativa färger för egenskapsvärden. |
| Avancerade färgmappning |Visar en färg för egenskapsvärden.  Om det angivna värdet i de övre tre visas alternativa färgen i stället för färgen som standard.  Om egenskapen inte är i de övre tre visas inte färg. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj om dataflöde kontroll ska aktiveras för panelen.  Detta ger ett alternativ visas om data inte är tillgänglig för panelen.  Detta används vanligtvis för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data är tillgängliga. |
| Fråga |Fråga om du vill köra för att kontrollera om data är tillgängliga för vyn.  Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet från den huvudsakliga frågan. |
| Meddelande |Meddelande som ska visas om dataflöde verifiering frågan returnerar inga data.  Om du anger inget meddelande *utför Assessment* visas. |


## <a name="line-chart-tile"></a>Raden diagram sida vid sida
Den **linjediagram** panelen visar ett linjediagram med flera serier från en fråga logg över tid.  

![Linjediagram och Callout sida vid sida](media/log-analytics-view-designer/tile-line-chart.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Text som visas överst i panelen. |
| Beskrivning |Text som visas under namnet på sida vid sida. |
| **Linjediagram** | |
| Fråga |Frågan ska köras för linjediagrammet.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Detta är vanligtvis en fråga som använder den **mått** nyckelord för att sammanfatta resultat.  Om frågan använder den **intervall** nyckelordet sedan x-axeln i diagrammet används för detta tidsintervall.  Om frågan inte innehåller den **intervall** nyckelord och varje timme sedan intervall används för x-axeln. |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj det här alternativet om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan.  Den här informationen används för att visa etiketter i diagram som visar värdetyperna och eventuellt för konvertering av värdena.  Den **enhetstyp** anger kategorin för enheten och definierar de **aktuella enhetstypen** värden som är tillgängliga.  Om du väljer ett värde i **omvandla till** sedan numeriska värden konverteras från den **aktuell enhet** typ till den **konvertera till** typen. |
| Anpassad etikett |Text som ska visas för Y-axeln bredvid etiketten för enhetstypen.  Om ingen etikett anges visas endast enhetstyp. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj om dataflöde kontroll ska aktiveras för panelen.  Detta ger ett alternativ visas om data inte är tillgänglig för panelen.  Detta används vanligtvis för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data är tillgängliga. |
| Fråga |Fråga om du vill köra för att kontrollera om data är tillgängliga för vyn.  Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet från den huvudsakliga frågan. |
| Meddelande |Meddelande som ska visas om dataflöde verifiering frågan returnerar inga data.  Om du anger inget meddelande *utför Assessment* visas. |


## <a name="line-chart--callout-tile"></a>Raden diagram & callout panelen
Den **rad diagram & callout** panelen visar ett linjediagram med flera serier från en fråga logg över tid och en kommentar med ett summerat värde.  

![Linjediagram och Callout sida vid sida](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Text som visas överst i panelen. |
| Beskrivning |Text som visas under namnet på sida vid sida. |
| **Linjediagram** | |
| Fråga |Frågan ska köras för linjediagrammet.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Detta är vanligtvis en fråga som använder den **mått** nyckelord för att sammanfatta resultat.  Om frågan använder den **intervall** nyckelordet sedan x-axeln i diagrammet används för detta tidsintervall.  Om frågan inte innehåller den **intervall** nyckelord och varje timme sedan intervall används för x-axeln. |
| **Linjediagram** |**> Callout** |
| Kommentar |Rubriktexten ska visas ovanför callout-värdet. |
| Serienamn |Egenskapsvärdet för serien ska användas för callout-värdet.  Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde för bildtexten.<br>-Genomsnittlig: Medelvärdet av värdet från alla poster.<br><br>-Antal: Antalet poster som returneras av frågan.<br>-Sista: Exempelvärde från det sista intervallet som ingår i diagrammet.<br>-Max: Maximalt värde från de intervall som ingår i diagrammet.<br>-Min: Minsta värde från de intervall som ingår i diagrammet.<br>-Summan: Summan av värdet från alla poster. |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj det här alternativet om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan.  Den här informationen används för att visa etiketter i diagram som visar värdetyperna och eventuellt för konvertering av värdena.  Den **enhetstyp** anger kategorin för enheten och definierar de **aktuella enhetstypen** värden som är tillgängliga.  Om du väljer ett värde i **omvandla till** sedan numeriska värden konverteras från den **aktuell enhet** typ till den **konvertera till** typen. |
| Anpassad etikett |Text som ska visas för Y-axeln bredvid etiketten för enhetstypen.  Om ingen etikett anges visas endast enhetstyp. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj om dataflöde kontroll ska aktiveras för panelen.  Detta ger ett alternativ visas om data inte är tillgänglig för panelen.  Detta används vanligtvis för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data är tillgängliga. |
| Fråga |Fråga om du vill köra för att kontrollera om data är tillgängliga för vyn.  Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet från den huvudsakliga frågan. |
| Meddelande |Meddelande som ska visas om dataflöde verifiering frågan returnerar inga data.  Om du anger inget meddelande *utför Assessment* visas. |


## <a name="two-timelines-tile"></a>Två tidslinjer sida vid sida
Den **två tidslinjer** panelen visar resultaten av de två loggen frågor när kolumndiagram.  En uppmaning visas för varje serie.  

![Två tidslinjer sida vid sida](media/log-analytics-view-designer/tile-two-timelines.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Text som visas överst i panelen. |
| Beskrivning |Text som visas under namnet på sida vid sida. |
| Första diagrammet | |
| Förklaring |Text som visas under callout för den första serien. |
| Färg |Färg som ska användas för kolumner i den första serien. |
| Diagram-fråga |Frågan ska köras under den första serien.  Antalet poster under varje tidsintervall et representeras av diagrammet kolumner. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde för bildtexten.<br><br>-Genomsnittlig: Medelvärdet av värdet från alla poster.<br>-Antal: Antalet poster som returneras av frågan.<br>-Sista: Exempelvärde från det sista intervallet som ingår i diagrammet.<br>-Max: Maximalt värde från de intervall som ingår i diagrammet. |
| **Det andra diagrammet** | |
| Förklaring |Text som visas under callout för den andra serien. |
| Färg |Färg som ska användas för kolumner i den andra serien. |
| Diagram-fråga |Frågan ska köras för den andra serien.  Antalet poster under varje tidsintervall et representeras av diagrammet kolumner. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde för bildtexten.<br><br>-Genomsnittlig: Medelvärdet av värdet från alla poster.<br>-Antal: Antalet poster som returneras av frågan.<br>-Sista: Exempelvärde från det sista intervallet som ingår i diagrammet.<br>-Max: Maximalt värde från de intervall som ingår i diagrammet. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj om dataflöde kontroll ska aktiveras för panelen.  Detta ger ett alternativ visas om data inte är tillgänglig för panelen.  Detta används vanligtvis för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data är tillgängliga. |
| Fråga |Fråga om du vill köra för att kontrollera om data är tillgängliga för vyn.  Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet från den huvudsakliga frågan. |
| Meddelande |Meddelande som ska visas om dataflöde verifiering frågan returnerar inga data.  Om du anger inget meddelande *utför Assessment* visas. |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) till stöd för frågor i panelerna.
* Lägg till [visualiseringen delar](log-analytics-view-designer-parts.md) till den anpassade vyn.
