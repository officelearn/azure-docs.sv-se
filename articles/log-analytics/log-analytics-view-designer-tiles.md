---
title: En referens guide till Vydesigner paneler i Azure Log Analytics | Microsoft Docs
description: "Du kan skapa anpassade vyer i Azure portal som visar olika datavisualiseringar i logganalys-arbetsytan med hjälp av Vydesigner i logganalys. Den här artikeln är en referenshandbok inställningar för rutor som är tillgängliga i din anpassade vyer."
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
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: f341cb9430c7750909c1fc1f50c15f0620e74366
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Referenshandboken till Vydesigner paneler i logganalys
Du kan skapa anpassade vyer i Azure-portalen som presenterar en mängd olika datavisualiseringar i logganalys-arbetsytan med hjälp av Vydesigner i Azure logganalys. Den här artikeln är en referenshandbok inställningar för rutor som är tillgängliga i din anpassade vyer.

Mer information om Vydesigner finns:

* [Visa Designer](log-analytics-view-designer.md): ger en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Referens för visualisering del](log-analytics-view-designer-parts.md): innehåller en referens guide till inställningarna för visualisering delar som är tillgängliga i din anpassade vyer.


I följande tabell beskrivs tillgängliga Vydesigner paneler:  

| sida vid sida | Beskrivning |
|:--- |:--- |
| [Antal](#number-tile) |Antal poster från en fråga. |
| [Två tal](#two-numbers-tile) |Antal poster från två olika frågor. |
| [Ring](#donut-tile) | Ett diagram som baseras på en fråga med ett summary-värde i mitten. |
| [Linjediagram och callout](#line-chart-amp-callout-tile) | Ett linjediagram som baseras på en fråga och en kommentar med ett summary-värde. |
| [Linjediagram](#line-chart-tile) |Ett linjediagram som baseras på en fråga. |
| [Två tidslinjer](#two-timelines-tile) | Ett stapeldiagram med två serier varje baserat på en separat fråga. |

I nästa avsnitt beskrivs sida vid sida-typer och deras egenskaper i detalj.

## <a name="number-tile"></a>Antalet sida vid sida
Den **nummer** panelen visar både antalet poster från en fråga i loggen och en etikett.

![Antalet sida vid sida](media/log-analytics-view-designer/tile-number.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på sida vid sida. |
| **sida vid sida** | |
| Förklaring |Den text som visas under värdet. |
| Fråga |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj den här länken om dataflöde kontroll ska aktiveras för panelen. Den här metoden ger ett alternativ visas om data inte är tillgänglig. Normalt använder du metoden för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data blir tillgängliga. |
| Fråga |Den fråga som körs för att avgöra om data är tillgängliga för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för den viktigaste frågan. |
| Meddelande |Det meddelande som visas om dataflöde verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utför Assessment* statusmeddelande visas. |


## <a name="two-numbers-tile"></a>Panelen två tal
Den här panelen visar antal poster från två olika loggen frågor och en etikett för varje.

![Panelen två tal](media/log-analytics-view-designer/tile-two-numbers.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på sida vid sida. |
| **Första sida vid sida** | |
| Förklaring |Den text som visas under värdet. |
| Fråga |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Andra sida vid sida** | |
| Förklaring |Den text som visas under värdet. |
| Fråga |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj den här länken om dataflöde kontroll ska aktiveras för panelen. Den här metoden ger ett alternativ visas om data inte är tillgänglig. Normalt använder du metoden för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data blir tillgängliga. |
| Fråga |Den fråga som körs för att avgöra om data är tillgängliga för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för den viktigaste frågan. |
| Meddelande |Det meddelande som visas om dataflöde verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utför Assessment* statusmeddelande visas. |


## <a name="donut-tile"></a>Ring sida vid sida
Den **ringen** panelen visar ett tal som sammanfattar en med en kolumn i en fråga i loggen. Ringen visar grafiskt resultaten av de tre översta posterna.

![Ring sida vid sida](media/log-analytics-view-designer/tile-donut.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på sida vid sida. |
| **Ring** | |
| Fråga |Den fråga som körs för ringen. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord för att sammanfatta resultat. |
| **Ring** |**> Center** |
| Text |Den text som visas under värdet i ringen. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta som ett enskilt värde.<ul><li>Sum: Lägga till värden för alla poster med egenskapens värde.</li><li>Procent: Procentandel av samlade värden från poster med egenskapsvärdet jämfört med de samlade värdena i alla poster.</li></ul> |
| Resultatvärden som används i center åtgärd |Alternativt, Välj plustecken (+) för att lägga till en eller flera värden. Resultatet av frågan är begränsade till poster med egenskapsvärden som du anger. Om inga värden läggs till, inkluderas alla poster i frågan. |
| **Ring** |**> Ytterligare alternativ** |
| Färger |Färgen som visas för var och en av de tre översta egenskaperna. Använd för att ange alternativa färger för egenskapsvärden *avancerade färg mappning*. |
| Avancerade färgmappning |Visar en färg som representerar egenskapsvärden. Om det angivna värdet i de tre översta visas alternativa färgen i stället för färgen som standard. Om egenskapen inte är i de tre översta visas färgen inte. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj den här länken om dataflöde kontroll ska aktiveras för panelen. Den här metoden ger ett alternativ visas om data inte är tillgänglig. Normalt använder du metoden för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data blir tillgängliga. |
| Fråga |Den fråga som körs för att avgöra om data är tillgängliga för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för den viktigaste frågan. |
| Meddelande |Det meddelande som visas om dataflöde verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utför Assessment* statusmeddelande visas. |


## <a name="line-chart-tile"></a>Raden diagram sida vid sida
Den här panelen är ett linjediagram som visar flera serier från en fråga logg över tid. 

![Raden diagram och callout-panelen](media/log-analytics-view-designer/tile-line-chart.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på sida vid sida. |
| **Linjediagram** | |
| Fråga |Den fråga som körs för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord för att sammanfatta resultat. Om frågan använder den *intervall* nyckelordet x-axeln använder detta tidsintervall. Om frågan inte använder den *intervall* nyckelord, x-axeln använder timvis intervall. |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan. Den här informationen används för att visa etiketter i diagram som visar värdetyperna och eventuellt för konvertering av värdena. Den **enhetstyp** anger kategorin för enheten och definierar de **aktuella enhetstypen** värden som är tillgängliga. Om du väljer ett värde i **omvandla till** sedan numeriska värden konverteras från den **aktuell enhet** typ till den **konvertera till** typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast i *enhet* typen visas. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj den här länken om dataflöde kontroll ska aktiveras för panelen. Den här metoden ger ett alternativ visas om data inte är tillgänglig. Normalt använder du metoden för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data blir tillgängliga. |
| Fråga |Den fråga som körs för att avgöra om data är tillgängliga för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för den viktigaste frågan. |
| Meddelande |Det meddelande som visas om dataflöde verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utför Assessment* statusmeddelande visas. |


## <a name="line-chart-and-callout-tile"></a>Raden diagram och callout-panelen
Den här panelen har både en rad diagram som visar flera serier från en fråga med tiden och en kommentar med ett summerat värde. 

![Raden diagram och callout-panelen](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på sida vid sida. |
| **Linjediagram** | |
| Fråga |Den fråga som körs för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord för att sammanfatta resultat. Om frågan använder den *intervall* nyckelordet x-axeln använder detta tidsintervall. Om frågan inte använder den *intervall* nyckelord, x-axeln använder timvis intervall. |
| **Linjediagram** |**> Callout** |
| Callout-rubrik | Den text som visas ovanför callout-värdet. |
| Serienamn |Egenskapsvärdet serie som ska användas som värde för bildtexter. Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta som ett enstaka värde för bildtexten.<ul><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet alla poster som returneras av frågan.</li><li>Senaste exempel: värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet av intervall som ingår i diagrammet.</li><li>Min: Det lägsta värdet för de intervall som ingår i diagrammet.</li><li>Sum: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa dataetiketter som visar värdetyperna och du kan också konvertera värden. Den *enhet* typ anger kategorin för enheten och definierar de tillgängliga *aktuell enhet* Skriv värden. Om du väljer ett värde i *omvandla till*, numeriska värden konverteras från den *aktuell enhet* typ till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast i *enhet* typen visas. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj den här länken om dataflöde kontroll ska aktiveras för panelen. Den här metoden ger ett alternativ visas om data inte är tillgänglig. Normalt använder du metoden för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data blir tillgängliga. |
| Fråga |Den fråga som körs för att avgöra om data är tillgängliga för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för den viktigaste frågan. |
| Meddelande |Det meddelande som visas om dataflöde verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utför Assessment* statusmeddelande visas. |


## <a name="two-timelines-tile"></a>Två tidslinjer sida vid sida
Den **två tidslinjer** panelen visar resultaten av de två loggen frågor när kolumndiagram. En uppmaning visas för varje serie. 

![Två tidslinjer sida vid sida](media/log-analytics-view-designer/tile-two-timelines.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på sida vid sida. |
| Första diagrammet | |
| Förklaring |Den text som visas under callout för den första serien. |
| Färg |Färgen som används för kolumnerna i den första serien. |
| Diagram-fråga |Den fråga som körs för den första serien. Antal poster under varje tidsintervall representeras av diagrammet kolumner. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta som ett enstaka värde för bildtexten.<ul><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet alla poster som returneras av frågan.</li><li>Senaste exempel: värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet av intervall som ingår i diagrammet.</li></ul> |
| **Det andra diagrammet** | |
| Förklaring |Den text som visas under callout för den andra serien. |
| Färg |Färgen som används för kolumnerna i den andra serien. |
| Diagram-fråga |Den fråga som körs för den andra serien. Antal poster under varje tidsintervall representeras av diagrammet kolumner. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta som ett enstaka värde för bildtexten.<ul><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet alla poster som returneras av frågan.</li><li>Senaste exempel: värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet av intervall som ingår i diagrammet. |
| **Avancerade** |**> Verifiering av-dataflöde** |
| Enabled |Välj den här länken om dataflöde kontroll ska aktiveras för panelen. Den här metoden ger ett alternativ visas om data inte är tillgänglig. Normalt använder du metoden för att tillhandahålla ett meddelande under tillfälliga när vyn är installerad och data blir tillgängliga. |
| Fråga |Den fråga som körs för att avgöra om data är tillgängliga för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för den viktigaste frågan. |
| Meddelande |Det meddelande som visas om dataflöde verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utför Assessment* statusmeddelande visas. |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) till stöd för frågor i panelerna.
* Lägg till [visualiseringen delar](log-analytics-view-designer-parts.md) till den anpassade vyn.
