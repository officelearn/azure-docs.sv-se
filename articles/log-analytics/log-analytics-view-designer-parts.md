---
title: En referenshandbok Vydesigner delar i Azure Log Analytics | Microsoft Docs
description: "Du kan skapa anpassade vyer i Azure portal som visar olika datavisualiseringar i logganalys-arbetsytan med hjälp av Vydesigner i logganalys. Den här artikeln är en referens guide till inställningar för visualisering delar som är tillgängliga i din anpassade vyer."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: bwren
ms.openlocfilehash: a2573eef3c90c1840c0d53b2f8aa2cfe2d3a7242
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Referenshandboken Vydesigner visualiseringen delar i logganalys
Du kan skapa anpassade vyer i Azure-portalen som presenterar en mängd olika datavisualiseringar från logganalys-arbetsytan med hjälp av Vydesigner i Azure logganalys. Den här artikeln är en referens guide till inställningar för visualisering delar som är tillgängliga i din anpassade vyer.

Mer information om Vydesigner finns:

* [Visa Designer](log-analytics-view-designer.md): ger en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Panelen referens](log-analytics-view-designer-tiles.md): innehåller en referens till inställningarna för varje bricka som är tillgängliga i anpassade vyer.


I följande tabell beskrivs tillgängliga Vydesigner panelen typer:

| Typ av vy | Beskrivning |
|:--- |:--- |
| [Listan med frågor](#list-of-queries-part) |Visar en lista över loggen sökfrågor. Du kan välja varje fråga för att visa dess resultat. |
| [Antal och lista](#number-and-list-part) |Huvudet visar ett tal som visar antalet poster från en sökfråga för loggen. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Två tal och lista](#two-numbers-and-list-part) |Rubriken visas två siffror som visar antalet poster från separat loggen sökfrågor. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Ring och lista](#donut-and-list-part) |Huvudet visar ett tal som sammanfattar en med en kolumn i en fråga i loggen. Ringen visar grafiskt resultaten av de tre översta posterna. |
| [Två tidslinjer och lista](#two-timelines-and-list-part) |Rubriken visas resultatet av två loggen frågor när kolumndiagram med en uppmaning som visar ett tal som sammanfattar en med en kolumn i en fråga i loggen. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Information](#information-part) |Rubriken visas statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text. |
| [Linjediagram och callout lista](#line-chart-callout-and-list-part) |Huvudet visar ett linjediagram med flera serier från en fråga logg över tid och en kommentar med ett summerat värde. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Linjediagram och lista](#line-chart-and-list-part) |Huvudet visar ett linjediagram med flera serier från en fråga logg över tid. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Stacken för rad diagram del](#stack-of-line-charts-part) |Visar tre separata linjediagram med flera serier från en fråga logg över tid. |

I nästa avsnitt beskrivs sida vid sida-typer och deras egenskaper i detalj.

## <a name="list-of-queries-part"></a>Lista över en del frågor
Listan med frågor del visar en lista med loggen sökfrågor. Du kan välja varje fråga för att visa dess resultat. Vyn innehåller en enda fråga som standard och du kan välja **+ frågan** att lägga till ytterligare frågor.

![Lista över frågor vy](media/log-analytics-view-designer/view-list-queries.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Namn |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Förvald filter |En kommaavgränsad lista över egenskaper som ska ingå i den vänstra filterfönstret när du väljer en fråga. |
| Återge läge |Den inledande vyn som visas när frågan har valts. Du kan välja alla tillgängliga vyer när de öppnar frågan. |
| **Frågor** | |
| Sökfråga |Frågan ska köras. |
| Eget namn | Ett beskrivande namn som visas. |

## <a name="number-and-list-part"></a>Antal och listan del
Huvudet visar ett tal som visar antalet poster från en sökfråga för loggen. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Lista över frågor vy](media/log-analytics-view-designer/view-number-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| Använd ikon |Välj den här länken för att visa ikonen. |
| **Rubrik** | |
| Förklaring |Den text som visas överst i huvudet. |
| Fråga |Frågan ska köras för sidhuvudet. Antalet poster som returneras av frågan visas. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** | |
| Fråga |Frågan ska köras för listan. Första två egenskaperna för de första tio posterna i resultatet visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt som baseras på det relativa värdet för numerisk kolumn.<br><br>Använd den `Sort` i frågan för att sortera posterna i listan. Du kan välja om du vill köra frågan och alla poster **se alla**. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna eller miniatyrdiagram. |
| Namn och värde avgränsare |Avgränsaren du använder för att parsa textegenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Två tal och en del lista
Rubriken har två tal som visar antalet poster från separat loggen sökfrågor. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Två tal & listvy](media/log-analytics-view-designer/view-two-numbers-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| Använd ikon |Välj den här länken för att visa ikonen. |
| **Rubrik navigering** | |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Rubrik** | |
| Förklaring |Den text som visas överst i huvudet. |
| Fråga |Frågan ska köras för sidhuvudet. Antalet poster som returneras av frågan visas. |
| **lista** | |
| Fråga |Frågan ska köras för listan. Första två egenskaperna för de första tio posterna i resultatet visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt utifrån relativa värdet för numerisk kolumn.<br><br>Använd den `Sort` i frågan för att sortera posterna i listan. Du kan välja om du vill köra frågan och alla poster **se alla**. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren du använder för att parsa textegenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="donut-and-list-part"></a>En del ringen och lista
Huvudet visar ett tal som sammanfattar en med en kolumn i en fråga i loggen. Ringen visar grafiskt resultaten av de tre översta posterna.

![Ring och lista](media/log-analytics-view-designer/view-donut-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| Använd ikon |Välj den här länken för att visa ikonen. |
| **Header** | |
| Namn |Den text som visas överst i huvudet. |
| Underrubrik |Den text som visas under rubriken överst i huvudet. |
| **Ring** | |
| Fråga |Frågan ska köras under ringen. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Ring** |**> Center** |
| Text |Den text som visas under värdet i ringen. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta som ett enskilt värde.<ul><li>Sum: Lägger till värdena för alla poster.</li><li>Procent: Förhållandet mellan posterna som returneras av värdena i **leda till att värden som används i center åtgärden** för totalt antal poster i frågan.</li></ul> |
| Resultatvärden som används i center åtgärd |Alternativt, Välj plustecken (+) för att lägga till en eller flera värden. Resultatet av frågan är begränsade till poster med egenskapsvärden som du anger. Om inga värden läggs till, inkluderas alla poster i frågan. |
| **Ytterligare alternativ** |**> Färger** |
| Färg 1<br>Färg 2<br>Färg 3 |Välj färg för de värden som visas i ringen. |
| **Ytterligare alternativ** |**> Avancerade färgmappning** |
| Fältvärde |Skriv namnet på ett fält att visa den som en annan färg om den ingår i ringen. |
| Färg |Välj färg för unikt fält. |
| **lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren du använder för att parsa textegenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Två tidslinjer och listan del
Rubriken visas resultatet av två loggen frågor när kolumndiagram med en uppmaning som visar ett tal som sammanfattar en med en kolumn i en fråga i loggen. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Två tidslinjer och listan Visa](media/log-analytics-view-designer/view-two-timelines-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| Använd ikon |Välj den här länken för att visa ikonen. |
| **Rubrik navigering** | |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Först diagrammets<br>andra diagram** | |
| Förklaring |Den text som visas under callout för den första serien. |
| Färg |Färgen som ska användas för kolumner i serien. |
| Fråga |Frågan ska köras under den första serien. Antal poster under varje tidsintervall representeras av diagrammet kolumner. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta som ett enstaka värde för bildtexten.<ul><li>Sum: Summan av värdena från alla poster.</li><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Senaste exempel: värdet från det sista intervallet som ingår i diagrammet.</li><li>Exempel på först: värdet från det första intervallet som ingår i diagrammet.</li><li>Antal: Antalet alla poster som returneras av frågan.</li></ul> |
| **lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="information-part"></a>En del information
Rubriken visas statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text.

![Visa information](media/log-analytics-view-designer/view-information.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Färg |Bakgrundsfärg för rubriken. |
| **Header** | |
| Bild |Avbildningsfilen som visas i rubriken. |
| Etikett |Den text som visas i rubriken. |
| **Header** |**> Link** |
| Etikett |Länktext. |
| URL |Url för länken. |
| **Information som** | |
| Namn |Den text som visas för rubriken för varje objekt. |
| Innehåll |Den text som visas för varje objekt. |

## <a name="line-chart-callout-and-list-part"></a>Linjediagram och callout listan del
Huvudet visar ett linjediagram med flera serier från en fråga logg över tid och en kommentar med ett summerat värde. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Linjediagram och callout listvy](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| Använd ikon |Välj den här länken för att visa ikonen. |
| **Header** | |
| Namn |Den text som visas överst i huvudet. |
| Underrubrik |Den text som visas under rubriken överst i huvudet. |
| **Linjediagram** | |
| Fråga |Frågan ska köras under linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord för att sammanfatta resultat. Om frågan använder den *intervall* nyckelordet x-axeln i diagrammet använder detta tidsintervall. Om frågan inte innehåller den *intervall* nyckelord, x-axeln använder timvis intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Linjediagram** |**> Callout** |
| Callout-rubrik |Den text som visas ovanför callout-värdet. |
| Serienamn |Egenskapsvärdet för serien ska användas för callout-värdet. Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta som ett enstaka värde för bildtexten.<ul><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet alla poster som returneras av frågan.</li><li>Senaste exempel: värdet från det sista intervallet som ingår i diagrammet.</li><li>Max: Det högsta värdet mellan intervall som ingår i diagrammet.</li><li>Min: Minimivärdet mellan intervall som ingår i diagrammet.</li><li>Sum: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa dataetiketter som visar värdetyperna och du kan också konvertera värden. Den *enhet* typ anger kategorin för enheten och definierar de tillgängliga *aktuell enhet* Skriv värden. Om du väljer ett värde i *omvandla till*, numeriska värden konverteras från den *aktuell enhet* typ till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast i *enhet* typen visas. |
| **lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren du använder för att parsa textegenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="line-chart-and-list-part"></a>En del diagrammet och listan av raden
Huvudet visar ett linjediagram med flera serier från en fråga logg över tid. I listan visas översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Raden diagrammet och listan Visa](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| Använd ikon |Välj den här länken för att visa ikonen. |
| **Header** | |
| Namn |Den text som visas överst i huvudet. |
| Underrubrik |Den text som visas under rubriken överst i huvudet. |
| **Linjediagram** | |
| Fråga |Frågan ska köras under linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord för att sammanfatta resultat. Om frågan använder den *intervall* nyckelordet x-axeln i diagrammet använder detta tidsintervall. Om frågan inte innehåller den *intervall* nyckelord, x-axeln använder timvis intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa dataetiketter som visar värdetyperna och du kan också konvertera värden. Den *enhet* typ anger kategorin för enheten och definierar de tillgängliga *aktuell enhet* Skriv värden. Om du väljer ett värde i *omvandla till*, numeriska värden konverteras från den *aktuell enhet* typ till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast i *enhet* typen visas. |
| **lista** | |
| Fråga |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren du använder för att parsa textegenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stacken för rad diagram del
Stacken linjediagram visar tre separata linjediagram med flera serier från en fråga logg över tid, som visas här:

![Stack för linjediagram](media/log-analytics-view-designer/view-stack-line-charts.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn med början vid den aktuella vyn. |
| Ikon |Avbildningsfilen som visas bredvid resultatet i huvudet. |
| **Diagram 1<br>diagram 2<br>diagram 3** |**> Header** |
| Namn |Den text som visas överst i diagrammet. |
| Underrubrik |Den text som visas under rubriken överst i diagrammet. |
| **Diagram 1<br>diagram 2<br>diagram 3** |**Linjediagram** |
| Fråga |Frågan ska köras under linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord för att sammanfatta resultat. Om frågan använder den *intervall* nyckelordet x-axeln i diagrammet använder detta tidsintervall. Om frågan inte innehåller den *intervall* nyckelord, x-axeln använder timvis intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Diagrammet** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa dataetiketter som visar värdetyperna och du kan också konvertera värden. Den *enhet* typ anger kategorin för enheten och definierar de tillgängliga *aktuell enhet* Skriv värden. Om du väljer ett värde i *omvandla till*, numeriska värden konverteras från den *aktuell enhet* typ till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast i *enhet* typen visas. |

## <a name="common-settings"></a>Vanliga inställningar
I följande avsnitt beskrivs de inställningar som är gemensamma för flera olika visualiseringen delar.

### <a name="name-value-separator"></a>Namn och värde avgränsare
Avgränsaren som namn och värde är den du använder för att parsa textegenskapen från en fråga till flera värden avgränsaren. Om du anger en avgränsare, kan du ange namn för varje fält avgränsade med samma avgränsare i den **namn** rutan.

Anta till exempel att en egenskap som kallas *plats* som ingår värden såsom *Redmond byggnad 41* och *Bellevue byggnad 12*. Du kan ange ett streck (-) för name och value avgränsare och *Stad Byggnad* för namnet. Den här metoden Parsar varje värde i två egenskaper kallas *Stad* och *byggnad*.

### <a name="click-through-navigation"></a>Klicka på via navigering
Klicka igenom navigering definierar vilken åtgärd som ska vidtas när du klickar på ett sidhuvud eller en lista över objekt i en vy.  Då öppnas antingen en fråga i den [loggen Sök portal](log-analytics-log-search-portals.md#log-search) eller starta en annan vy.

I följande tabell beskrivs inställningarna för klicka igenom navigering.

| Inställning           | Beskrivning |
|:--|:--|
| Loggen Sök (Auto) | Logg-sökning för att köras när du väljer en rubrikobjektet.  Det här är samma loggen sökning som objektet baseras på.
| Loggsökning        | Log-sökning för att köras när du markerar ett objekt i en lista.  Skriv frågan i den **navigering frågan** rutan.   Använd *{valda objektet}* med syntaxen för ett objekt som användaren har valt.  Om exempelvis frågan har en kolumn med namnet *datorn* och navigering frågan *{valda objektet}*, en fråga som *datorn = ”den här datorn”* körs när du väljer en dator. Om frågan navigering är *typ = händelsen {valda objektet}*, frågan *typ = händelse datorn = ”den här datorn”* körs. |
| Visa              | Visa öppna när du väljer en rubrik eller ett objekt i en lista.  Välj namnet på en vy i arbetsytan i den **vynamn** rutan. |



### <a name="sparklines"></a>Miniatyrdiagram
Ett miniatyrdiagram är ett litet linjediagram som illustrerar värdet för en lista över tid. Du kan välja om du vill visa en vågräta fältet som anger relativa värdet för en numerisk kolumn eller ett miniatyrdiagram som anger dess värde över tid för visualisering delar med en lista.

I följande tabell beskrivs inställningarna för miniatyrdiagram:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. |
| Åtgärd |Om miniatyrdiagram är aktiverade är igen att utföra på varje egenskap i listan för att beräkna värdena för miniatyrdiagrammet.<ul><li>Exempel på senast: det sista värdet för seriens över ett tidsintervall.</li><li>Max: Det maximala värdet för seriens över ett tidsintervall.</li><li>Min: Minsta värde för seriens över ett tidsintervall.</li><li>Sum: Summan av värdena för serie över ett tidsintervall.</li><li>Sammanfattning: Använder samma `measure` kommandot som frågan i huvudet.</li></ul> |

### <a name="thresholds"></a>Tröskelvärden
Du kan visa en färgade ikonen bredvid varje objekt i en lista med hjälp av tröskelvärden. Tröskelvärden ger dig en snabb visual indikator för objekt som överskrider ett visst värde eller faller inom ett visst intervall. Du kan till exempel visa en grön ikon för artiklar med ett giltigt värde, gult om värdet är inom ett intervall som indikerar en varning och rött om det överstiger ett felaktigt värde.

När du aktiverar tröskelvärden för en del, måste du ange en eller flera tröskelvärden. Om värdet för ett objekt som är större än ett tröskelvärde och lägre än tröskelvärdet för nästa, används färg för värdet. Om objektet är större än det högsta tröskelvärdet, används en annan färg. 

Varje uppsättning tröskelvärde har ett tröskelvärde med värdet **standard**. Det här är en färg som ska anges om inga andra värden har överskridits. Du kan lägga till eller ta bort tröskelvärden genom att välja den **Lägg till** (+) eller **ta bort** (x)-knappen.

I följande tabell beskrivs inställningarna för tröskelvärden:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera tröskelvärden |Välj den här länken för att visa en färg-ikonen längst till vänster i varje värde. Ikonen anger värdets hälsa i förhållande till angivna tröskelvärden. |
| Namn |Namnet på tröskelvärdet. |
| Tröskelvärde |Värdet för tröskelvärdet. Färg för hälsa för varje listobjekt har angetts till färgen för den högsta tröskelvärde har överskridits med objektets värde. Om inget tröskelvärden överskrids, används en standardfärg som. |
| Färg |Färgen som anger tröskelvärdet. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att stödja frågorna i visualiseringen delar.
