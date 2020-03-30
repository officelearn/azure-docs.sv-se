---
title: En referensguide till Visa designer-paneler i Azure Monitor | Microsoft-dokument
description: Genom att använda View Designer i Azure Monitor kan du skapa anpassade vyer som visas i Azure-portalen och innehåller en mängd olika visualiseringar på data på log analytics-arbetsytan. Den här artikeln är en referensguide till inställningarna för de paneler som är tillgängliga i dina anpassade vyer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 0320be3a2cfbb96367799577a6e56bcf5da87dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658514"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Referensguide till Visa designer-paneler i Azure Monitor
Genom att använda View Designer i Azure Monitor kan du skapa en mängd anpassade vyer i Azure-portalen som kan hjälpa dig att visualisera data på din Log Analytics-arbetsyta. Den här artikeln är en referensguide till inställningarna för de paneler som är tillgängliga i dina anpassade vyer.

Mer information om View Designer finns i:

* [View Designer](view-designer.md): Ger en översikt över Visa designer och procedurer för att skapa och redigera anpassade vyer.
* [Visualiseringsdelreferens:](view-designer-parts.md)Innehåller en referensguide till inställningarna för de visualiseringsdelar som är tillgängliga i dina anpassade vyer.


De tillgängliga Visa Designer-panelerna beskrivs i följande tabell:  

| Panel | Beskrivning |
|:--- |:--- |
| [Nummer](#number-tile) |Antalet poster från en fråga. |
| [Två siffror](#two-numbers-tile) |Antalet poster från två olika frågor. |
| [Ring](#donut-tile) | Ett diagram som baseras på en fråga, med ett summeringsvärde i mitten. |
| Linjediagram och bildtext | Ett linjediagram som baseras på en fråga och en bildtext med ett summeringsvärde. |
| [Linjediagram](#line-chart-tile) |Ett linjediagram som baseras på en fråga. |
| [Två tidslinjer](#two-timelines-tile) | Ett stapeldiagram med två serier, var och en baserat på en separat fråga. |

I nästa avsnitt beskrivs paneltyperna och deras egenskaper i detalj.

> [!NOTE]
> Paneler i vyer baseras på [loggfrågor](../log-query/log-query-overview.md) på logganalysarbetsytan. De stöder för närvarande inte [korsresursfrågor](../log-query/cross-workspace-query.md) för att hämta data från Application Insights.

## <a name="number-tile"></a>Antal kakel
På panelen **Tal** visas både antalet poster från en loggfråga och en etikett.

![Antal kakel](media/view-designer-tiles/tile-number.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Texten som visas högst upp på panelen. |
| Beskrivning |Texten som visas under panelnamnet. |
| **Kakel** | |
| Förklaring |Texten som visas under värdet. |
| Söka i data |Frågan som körs. Antalet poster som returneras av frågan visas. |
| **Avancerade** |**> verifiering av dataflöde** |
| Enabled |Välj den här länken om dataflödesverifiering ska aktiveras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligtvis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Söka i data |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om verifieringsfrågan för dataflöde returnerar inga data. Om du inte anger något meddelande visas ett statusmeddelande *för resultatbedömning.* |


## <a name="two-numbers-tile"></a>Panel med två nummer
Den här panelen visar antalet poster från två olika loggfrågor och en etikett för varje.

![Panel med två nummer](media/view-designer-tiles/tile-two-numbers.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Texten som visas högst upp på panelen. |
| Beskrivning |Texten som visas under panelnamnet. |
| **Första panelen** | |
| Förklaring |Texten som visas under värdet. |
| Söka i data |Frågan som körs. Antalet poster som returneras av frågan visas. |
| **Andra kakel** | |
| Förklaring |Texten som visas under värdet. |
| Söka i data |Frågan som körs. Antalet poster som returneras av frågan visas. |
| **Avancerade** |**> verifiering av dataflöde** |
| Enabled |Välj den här länken om dataflödesverifiering ska aktiveras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligtvis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Söka i data |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om verifieringsfrågan för dataflöde returnerar inga data. Om du inte anger något meddelande visas ett statusmeddelande *för resultatbedömning.* |


## <a name="donut-tile"></a>Donut kakel
Panelen **Donut** visar ett enskilt tal som summerar en värdekolumn i en loggfråga. Donuten visar grafiskt resultaten av de tre översta posterna.

![Donut kakel](media/view-designer-tiles/tile-donut.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Texten som visas högst upp på panelen. |
| Beskrivning |Texten som visas under panelnamnet. |
| **Ring** | |
| Söka i data |Frågan som körs för munken. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt *nyckelordet mått* för att sammanfatta resultaten. |
| **Ring** |**> center** |
| Text |Texten som visas under värdet inuti munken. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta den som ett enda värde.<ul><li>Summa: Lägg till värdena för alla poster med egenskapsvärdet.</li><li>Procent: Procentandel av de summerade värdena från poster med egenskapsvärdet jämfört med de summerade värdena för alla poster.</li></ul> |
| Resultatvärden som används vid mittoperation |Du kan också välja plustecknet (+) för att lägga till ett eller flera värden. Resultatet av frågan är begränsat till poster med de egenskapsvärden som du anger. Om inga värden läggs till inkluderas alla poster i frågan. |
| **Ring** |**> Ytterligare alternativ** |
| Färger |Färgen som visas för var och en av de tre översta egenskaperna. Om du vill ange alternativa färger för specifika egenskapsvärden använder du *Avancerad färgmappning*. |
| Avancerad färgmappning |Visar en färg som representerar specifika egenskapsvärden. Om värdet du anger finns i de tre översta visas den alternativa färgen i stället för standardfärgen. Om egenskapen inte finns i de tre översta visas inte färgen. |
| **Avancerade** |**> verifiering av dataflöde** |
| Enabled |Välj den här länken om dataflödesverifiering ska aktiveras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligtvis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Söka i data |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om verifieringsfrågan för dataflöde returnerar inga data. Om du inte anger något meddelande visas ett statusmeddelande *för resultatbedömning.* |


## <a name="line-chart-tile"></a>Panel för linjediagram
Den här panelen är ett linjediagram som visar flera serier från en loggfråga över tid. 

![Linjediagram och bildtextpanel](media/view-designer-tiles/tile-line-chart.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Texten som visas högst upp på panelen. |
| Beskrivning |Texten som visas under panelnamnet. |
| **Linjediagram** | |
| Söka i data |Frågan som körs för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt *nyckelordet mått* för att sammanfatta resultaten. Om nyckelordet för *frågearbetet* används intervallet använder x-axeln det här tidsintervallet. Om nyckelordet för frågan inte använder *nyckelordet intervall* använder x-axeln timintervall. |
| **Linjediagram** |**> Y-axeln** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan. Den här informationen används för att visa etiketter i diagrammet som anger värdetyperna och eventuellt för att konvertera värdena. **Enhetstypen** anger enhetens kategori och definierar de **aktuella enhetstypvärden** som är tillgängliga. Om du väljer ett värde i **Konvertera till** konverteras de numeriska värdena från typen **Aktuell enhet** till typen **Konvertera till.** |
| Anpassad etikett |Texten som visas för y-axeln bredvid etiketten för *Unit* enhetstypen. Om ingen etikett har angetts visas endast enhetstypen. *Unit* |
| **Avancerade** |**> verifiering av dataflöde** |
| Enabled |Välj den här länken om dataflödesverifiering ska aktiveras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligtvis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Söka i data |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om verifieringsfrågan för dataflöde returnerar inga data. Om du inte anger något meddelande visas ett statusmeddelande *för resultatbedömning.* |


## <a name="line-chart-and-callout-tile"></a>Linjediagram och bildtextpanel
Den här panelen har både ett linjediagram som visar flera serier från en loggfråga över tid och en bildtext med ett sammanfattat värde. 

![Linjediagram och bildtextpanel](media/view-designer-tiles/tile-line-chart-callout.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Texten som visas högst upp på panelen. |
| Beskrivning |Texten som visas under panelnamnet. |
| **Linjediagram** | |
| Söka i data |Frågan som körs för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt *nyckelordet mått* för att sammanfatta resultaten. Om nyckelordet för *frågearbetet* används intervallet använder x-axeln det här tidsintervallet. Om nyckelordet för frågan inte använder *nyckelordet intervall* använder x-axeln timintervall. |
| **Linjediagram** |**> Bildtext** |
| Bildtexttitel | Texten som visas ovanför bildtextvärdet. |
| Seriens namn |Egenskapsvärdet för serier som ska användas som bildtextvärde. Om ingen serie anges används alla poster från frågan. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta den som ett enda värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet för de intervall som ingår i diagrammet.</li><li>Min: Minimivärdet för de intervall som ingår i diagrammet.</li><li>Summa: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagrametiketter som anger värdetyperna och eventuellt för att konvertera värdena. Enhetstypen anger enhetens kategori och definierar tillgängliga värden för *Unit* *aktuella enhetstyp.* Om du väljer ett värde i *Konvertera till*konverteras de numeriska värdena från typen *Aktuell enhet* till typen *Konvertera till.* |
| Anpassad etikett |Texten som visas för y-axeln bredvid etiketten för *Unit* enhetstypen. Om ingen etikett har angetts visas endast enhetstypen. *Unit* |
| **Avancerade** |**> verifiering av dataflöde** |
| Enabled |Välj den här länken om dataflödesverifiering ska aktiveras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligtvis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Söka i data |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om verifieringsfrågan för dataflöde returnerar inga data. Om du inte anger något meddelande visas ett statusmeddelande *för resultatbedömning.* |


## <a name="two-timelines-tile"></a>Panel med två tidslinjer
Panelen **Två tidslinjer** visar resultatet av två loggfrågor över tid som stapeldiagram. En bildtext visas för varje serie. 

![Panel med två tidslinjer](media/view-designer-tiles/tile-two-timelines.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Namn |Texten som visas högst upp på panelen. |
| Beskrivning |Texten som visas under panelnamnet. |
| Första diagrammet | |
| Förklaring |Texten som visas under bildtexten för den första serien. |
| Color |Färgen som används för kolumnerna i den första serien. |
| Diagramfråga |Frågan som körs för den första serien. Antalet poster under varje tidsintervall representeras av diagramkolumnerna. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta den som ett enda värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet för de intervall som ingår i diagrammet.</li></ul> |
| **Andra diagrammet** | |
| Förklaring |Texten som visas under bildtexten för den andra serien. |
| Color |Färgen som används för kolumnerna i den andra serien. |
| Diagramfråga |Frågan som körs för den andra serien. Antalet poster under varje tidsintervall representeras av diagramkolumnerna. |
| Åtgärd |Åtgärden som utförs på egenskapen value för att sammanfatta den som ett enda värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet för de intervall som ingår i diagrammet. |
| **Avancerade** |**> verifiering av dataflöde** |
| Enabled |Välj den här länken om dataflödesverifiering ska aktiveras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligtvis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Söka i data |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för värdet för huvudfrågan. |
| Meddelande |Meddelandet som visas om verifieringsfrågan för dataflöde returnerar inga data. Om du inte anger något meddelande visas ett statusmeddelande *för resultatbedömning.* |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) som stöder frågor i paneler.
* Lägg till [visualiseringsdelar](view-designer-parts.md) i den anpassade vyn.
