---
title: En referensguide till Vydesigner panelerna i Azure Log Analytics | Microsoft Docs
description: Du kan skapa anpassade vyer i Azure-portalen som visar olika datavisualiseringar i Log Analytics-arbetsytan med hjälp av Vydesigner i Log Analytics. Den här artikeln är en referensguide i inställningarna för panelerna som är tillgängliga i dina anpassade vyer.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 5b01b55c8b2367861459069cc255840e883b9fc5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817402"
---
# <a name="reference-guide-to-view-designer-tiles-in-log-analytics"></a>Referensguide till Vydesigner paneler i Log Analytics
Du kan skapa anpassade vyer i Azure-portalen som presenterar en mängd olika visualiseringar i Log Analytics-arbetsytan med hjälp av Vydesigner i Azure Log Analytics. Den här artikeln är en referensguide i inställningarna för panelerna som är tillgängliga i dina anpassade vyer.

Mer information om Vydesigner finns:

* [Visa Designer](view-designer.md): Översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Referens för visualiseringsdel](view-designer-parts.md): Innehåller en referensguide till inställningarna för visualisering delarna som är tillgängliga i dina anpassade vyer.


Tillgängliga Vydesigner paneler beskrivs i följande tabell:  

| Upprepa | Beskrivning |
|:--- |:--- |
| [Nummer](#number-tile) |Totalt antal poster från en fråga. |
| [Två tal](#two-numbers-tile) |Antalet poster från två olika frågor. |
| [Ringdiagram](#donut-tile) | Ett diagram som baseras på en fråga med ett summary-värde i mitten. |
| Linjediagram och bildtext | Ett linjediagram som baseras på en fråga och en bildtext med ett summary-värde. |
| [Linjediagram](#line-chart-tile) |Ett linjediagram som baseras på en fråga. |
| [Två tidslinjer](#two-timelines-tile) | Ett stapeldiagram med två serier, var och en baserat på en separat fråga. |

I nästa avsnitt beskrivs typerna av paneler och deras egenskaper i detalj.

## <a name="number-tile"></a>Sifferpanel
Den **nummer** panelen visar både antalet poster från en loggfråga och en etikett.

![Sifferpanel](media/view-designer-tiles/tile-number.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på panelen. |
| **panelen** | |
| Teckenförklaring |Den text som visas under värdet. |
| Söka i data |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Avancerade** |**> Verifiering av dataflöde** |
| Enabled |Välj den här länken om verifiering av dataflöde ska aktiveras för panelen. Den här metoden ger ett alternativ meddelande om data inte är tillgänglig. Du kan normalt använda metoden för att tillhandahålla ett meddelande under den period när vyn har installerats och data blir tillgängliga. |
| Söka i data |Den fråga som körs för att avgöra om data är tillgänglig för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om dataflödet verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utvärdering körs* statusmeddelande visas. |


## <a name="two-numbers-tile"></a>Två tal panelen
Den här panelen visar antal poster från två olika loggfrågor och en etikett för varje.

![Två tal panelen](media/view-designer-tiles/tile-two-numbers.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på panelen. |
| **Panel 1** | |
| Teckenförklaring |Den text som visas under värdet. |
| Söka i data |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Panel 2** | |
| Teckenförklaring |Den text som visas under värdet. |
| Söka i data |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Avancerade** |**> Verifiering av dataflöde** |
| Enabled |Välj den här länken om verifiering av dataflöde ska aktiveras för panelen. Den här metoden ger ett alternativ meddelande om data inte är tillgänglig. Du kan normalt använda metoden för att tillhandahålla ett meddelande under den period när vyn har installerats och data blir tillgängliga. |
| Söka i data |Den fråga som körs för att avgöra om data är tillgänglig för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om dataflödet verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utvärdering körs* statusmeddelande visas. |


## <a name="donut-tile"></a>Ringdiagram panel
Den **ringdiagram** panelen visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Ringen visar grafiskt resultat för de tre översta posterna.

![Ringdiagram panel](media/view-designer-tiles/tile-donut.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på panelen. |
| **Ringdiagram** | |
| Söka i data |Den fråga som körs för ringen. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. |
| **Ringdiagram** |**> Center** |
| Text |Den text som visas under värdet mellan ringen. |
| Åtgärd |Den åtgärd som utförs på värdeegenskapen och sammanfatta som ett enda värde.<ul><li>Sum: Lägg till värdena för alla poster med egenskapens värde.</li><li>Procent: Procentandel av summerade värden från poster med egenskapsvärdet jämfört med de samlade värdena i alla poster.</li></ul> |
| Resultatvärden som används i mittåtgärd |Du kan också klicka på plustecknet (+) att lägga till ett eller flera värden. Frågans resultat är begränsade till poster med egenskapsvärden som du anger. Om inga värden har lagts till, ingår alla poster i frågan. |
| **Ringdiagram** |**> Ytterligare alternativ** |
| Färger |Färgen som visas för var och en av de tre översta egenskaperna. Om du vill ange alternativa färger för speciella egenskapsvärden, använda *avancerade färg mappning*. |
| Avancerad färgmappning |Visar en färg som representerar speciella egenskapsvärden. Om det angivna värdet finns i tre översta, visas alternativa färg istället för standard färg. Om egenskapen inte är i övre tre, visas inte färg. |
| **Avancerade** |**> Verifiering av dataflöde** |
| Enabled |Välj den här länken om verifiering av dataflöde ska aktiveras för panelen. Den här metoden ger ett alternativ meddelande om data inte är tillgänglig. Du kan normalt använda metoden för att tillhandahålla ett meddelande under den period när vyn har installerats och data blir tillgängliga. |
| Söka i data |Den fråga som körs för att avgöra om data är tillgänglig för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om dataflödet verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utvärdering körs* statusmeddelande visas. |


## <a name="line-chart-tile"></a>Rad diagram sida vid sida
Den här panelen är ett linjediagram som visar flera serier från en loggfråga över tid. 

![Rad linjediagram och bildtext panel](media/view-designer-tiles/tile-line-chart.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på panelen. |
| **Linjediagram** | |
| Söka i data |Den fråga som körs för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln använder det här tidsintervallet. Om frågan inte använder den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| **Linjediagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för de värden som returneras av frågan. Den här informationen används för att visa etiketter i diagrammet som anger vilka värde och om du vill för att konvertera värden. Den **enhetstyp** anger kategorin för enheten och definierar de **aktuella enhetstypen** värden som är tillgängliga. Om du väljer ett värde i **konvertera till** och sedan de numeriska värdena konverteras från den **aktuella enheten** skriver till den **konvertera till** typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |
| **Avancerade** |**> Verifiering av dataflöde** |
| Enabled |Välj den här länken om verifiering av dataflöde ska aktiveras för panelen. Den här metoden ger ett alternativ meddelande om data inte är tillgänglig. Du kan normalt använda metoden för att tillhandahålla ett meddelande under den period när vyn har installerats och data blir tillgängliga. |
| Söka i data |Den fråga som körs för att avgöra om data är tillgänglig för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om dataflödet verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utvärdering körs* statusmeddelande visas. |


## <a name="line-chart-and-callout-tile"></a>Rad linjediagram och bildtext panel
Den här panelen har både en rad diagram som visar flera serier från en logg fråga över tid och en uppmaning med ett sammanfattande värde. 

![Rad linjediagram och bildtext panel](media/view-designer-tiles/tile-line-chart-callout.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på panelen. |
| **Linjediagram** | |
| Söka i data |Den fråga som körs för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln använder det här tidsintervallet. Om frågan inte använder den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| **Linjediagram** |**> Bildtext** |
| Bildtextrubrik | Den text som visas ovanför bildtextvärdet. |
| Dataseriens namn |Serien egenskapens värde som ska användas som värde för pratbubbla. Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Den åtgärd som utförs på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet på det sista intervallet som ingår i diagrammet.</li><li>Max: Det högsta värdet för de intervall som ingår i diagrammet.</li><li>Min: Det lägsta värdet på de intervall som ingår i diagrammet.</li><li>Sum: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |
| **Avancerade** |**> Verifiering av dataflöde** |
| Enabled |Välj den här länken om verifiering av dataflöde ska aktiveras för panelen. Den här metoden ger ett alternativ meddelande om data inte är tillgänglig. Du kan normalt använda metoden för att tillhandahålla ett meddelande under den period när vyn har installerats och data blir tillgängliga. |
| Söka i data |Den fråga som körs för att avgöra om data är tillgänglig för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om dataflödet verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utvärdering körs* statusmeddelande visas. |


## <a name="two-timelines-tile"></a>Två tidslinjer panel
Den **två tidslinjer** panelen visar resultatet av två loggfrågor över tid som kolumndiagram. En uppmaning visas för varje serie. 

![Två tidslinjer panel](media/view-designer-tiles/tile-two-timelines.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Den text som visas överst i panelen. |
| Beskrivning |Den text som visas under namnet på panelen. |
| Diagram 1 | |
| Teckenförklaring |Den text som visas under bildtext för den första serien. |
| Färg |Färg som används för kolumnerna i den första serien. |
| Diagramfråga |Den fråga som körs för första serien. Antalet poster under varje tidsintervall representeras av diagramkolumner. |
| Åtgärd |Den åtgärd som utförs på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet på det sista intervallet som ingår i diagrammet.</li><li>Max: Det högsta värdet för de intervall som ingår i diagrammet.</li></ul> |
| **Diagram 2** | |
| Teckenförklaring |Den text som visas under bildtext för den andra serien. |
| Färg |Färg som används för kolumnerna i den andra serien. |
| Diagramfråga |Den fråga som körs för den andra serien. Antalet poster under varje tidsintervall representeras av diagramkolumner. |
| Åtgärd |Den åtgärd som utförs på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet på det sista intervallet som ingår i diagrammet.</li><li>Max: Det högsta värdet för de intervall som ingår i diagrammet. |
| **Avancerade** |**> Verifiering av dataflöde** |
| Enabled |Välj den här länken om verifiering av dataflöde ska aktiveras för panelen. Den här metoden ger ett alternativ meddelande om data inte är tillgänglig. Du kan normalt använda metoden för att tillhandahålla ett meddelande under den period när vyn har installerats och data blir tillgängliga. |
| Söka i data |Den fråga som körs för att avgöra om data är tillgänglig för vyn. Om frågan returnerar inga resultat, visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om dataflödet verifiering frågan returnerar inga data. Om du anger inget meddelande, en *utvärdering körs* statusmeddelande visas. |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggsökningar](../../azure-monitor/log-query/log-query-overview.md) för frågorna i paneler.
* Lägg till [visualisering delar](view-designer-parts.md) till den anpassade vyn.
