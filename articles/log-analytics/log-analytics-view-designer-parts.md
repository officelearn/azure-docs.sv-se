---
title: En referensguiden för Vydesigner delar i Azure Log Analytics | Microsoft Docs
description: Du kan skapa anpassade vyer i Azure-portalen som visar olika datavisualiseringar i Log Analytics-arbetsytan med hjälp av Vydesigner i Log Analytics. Den här artikeln är en referensguide i inställningarna för visualisering delarna som är tillgängliga i dina anpassade vyer.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: f3c24dbc0f5bb4d5b595799da4f1212b26f8c02a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016768"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Referensguide för Vydesigner visualisering delar i Log Analytics
Du kan skapa anpassade vyer i Azure-portalen som presenterar en mängd olika visualiseringar från Log Analytics-arbetsytan med hjälp av Vydesigner i Azure Log Analytics. Den här artikeln är en referensguide i inställningarna för visualisering delarna som är tillgängliga i dina anpassade vyer.

Mer information om Vydesigner finns:

* [Visa Designer](log-analytics-view-designer.md): innehåller en översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Panelen referens](log-analytics-view-designer-tiles.md): innehåller en referens till inställningarna för varje panel som är tillgängliga i din anpassade vyer.


Tillgängliga typer av Vydesigner paneler beskrivs i följande tabell:

| Vytyp | Beskrivning |
|:--- |:--- |
| [Lista över frågor](#list-of-queries-part) |Visar en lista över loggsökningsfrågor. Du kan välja varje fråga för att visa resultaten. |
| [Antal och lista](#number-and-list-part) |Rubriken visar ett tal som visar antalet poster från en sökfråga för loggen. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Två siffror och lista](#two-numbers-and-list-part) |Rubriken visar två tal som visar antalet poster från separata loggsökningsfrågor. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Ringdiagram och lista](#donut-and-list-part) |Rubriken visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Ringen visar grafiskt resultat för de tre översta posterna. |
| [Två tidslinjer och lista](#two-timelines-and-list-part) |Rubriken visas resultatet av två loggfrågor med tiden då stående stapeldiagram med en uppmaning som visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Information](#information-part) |Rubriken visar statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text. |
| [Linjediagram, bildtext och lista](#line-chart-callout-and-list-part) |Rubriken visar ett linjediagram med flera serier från en loggfråga över tid och en uppmaning med ett sammanfattande värde. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Linjediagram och lista](#line-chart-and-list-part) |Rubriken visar ett linjediagram med flera serier från en loggfråga över tid. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid. |
| [Stack för rad diagram del](#stack-of-line-charts-part) |Visar tre separata linjediagram med flera serier från en loggfråga över tid. |

I nästa avsnitt beskrivs typerna av paneler och deras egenskaper i detalj.

## <a name="list-of-queries-part"></a>Lista över frågor del
Lista över frågor del visar en lista över loggsökningsfrågor. Du kan välja varje fråga för att visa resultaten. Vyn innehåller en enkel fråga som standard, och du kan välja **+ fråga** att lägga till ytterligare frågor.

![Lista över frågor vy](media/log-analytics-view-designer-parts/view-list-queries.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Titel |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Förvalda filter |En kommaavgränsad lista över egenskaper som ska ingå i den vänstra filterfönstret när du väljer en fråga. |
| Återgivningsläge |Den inledande vyn som visas när frågan har valts. Du kan välja alla vyer som är tillgängliga när de öppnar frågan. |
| **Frågor** | |
| Sökfråga |Frågan ska köras. |
| Eget namn | Ett beskrivande namn som visas. |

## <a name="number-and-list-part"></a>En del tal och lista
Rubriken visar ett tal som visar antalet poster från en sökfråga för loggen. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Lista över frågor vy](media/log-analytics-view-designer-parts/view-number-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik** | |
| Teckenförklaring |Den text som visas överst i rubriken. |
| Söka i data |Frågan ska köras för sidhuvudet. Antalet poster som returneras av frågan visas. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** | |
| Söka i data |Frågan ska köras för listan. Första två egenskaperna för de första tio posterna i resultaten visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt som baseras på det relativa värdet för den numeriska kolumnen.<br><br>Använd den `Sort` i frågan för att sortera poster i listan. Om du vill köra frågan och returnerar alla poster, kan du välja **se alla**. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Två tal och en del lista
Rubriken har två siffror som visar antalet poster från separata loggsökningsfrågor. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Visa två siffror och lista](media/log-analytics-view-designer-parts/view-two-numbers-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik-navigering** | |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Rubrik** | |
| Teckenförklaring |Den text som visas överst i rubriken. |
| Söka i data |Frågan ska köras för sidhuvudet. Antalet poster som returneras av frågan visas. |
| **Lista** | |
| Söka i data |Frågan ska köras för listan. Första två egenskaperna för de första tio posterna i resultaten visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt baserat på det relativa värdet för den numeriska kolumnen.<br><br>Använd den `Sort` i frågan för att sortera poster i listan. Om du vill köra frågan och returnerar alla poster, kan du välja **se alla**. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="donut-and-list-part"></a>En del ringdiagram och lista
Rubriken visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Ringen visar grafiskt resultat för de tre översta posterna.

![Ringdiagram och lista](media/log-analytics-view-designer-parts/view-donut-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik** | |
| Titel |Den text som visas överst i rubriken. |
| Underrubrik |Den text som visas under rubriken överst i rubriken. |
| **Ringdiagram** | |
| Söka i data |Frågan ska köras för ringen. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Ringdiagram** |**> Center** |
| Text |Den text som visas under värdet mellan ringen. |
| Åtgärd |Åtgärd att utföra på värdeegenskapen och sammanfatta som ett enda värde.<ul><li>Sum: Lägger till värdena för alla poster.</li><li>Procent: Förhållandet mellan poster som returneras av värdena i **resultera värden som används i mittåtgärd** för totalt antal poster i frågan.</li></ul> |
| Resultatvärden som används i mittåtgärd |Du kan också klicka på plustecknet (+) att lägga till ett eller flera värden. Frågans resultat är begränsade till poster med egenskapsvärden som du anger. Om inga värden har lagts till, ingår alla poster i frågan. |
| **Ytterligare alternativ** |**> Färger** |
| Färg 1<br>Färg 2<br>Färg 3 |Välj färg för var och en av de värden som visas i ringdiagrammet. |
| **Ytterligare alternativ** |**> Avancerad färgmappning** |
| Fältvärde |Skriv namnet på ett fält att visa den som en annan färg om den ingår i ringdiagrammet. |
| Färg |Välj färg för unika fält. |
| **Lista** | |
| Söka i data |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Två tidslinjer och lista del
Rubriken visas resultatet av två loggfrågor med tiden då stående stapeldiagram med en uppmaning som visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Två och tidslinjer lista](media/log-analytics-view-designer-parts/view-two-timelines-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik-navigering** | |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Först diagrammets<br>andra diagram** | |
| Teckenförklaring |Den text som visas under bildtext för den första serien. |
| Färg |Färgen som ska användas för kolumnerna i serien. |
| Söka i data |Frågan ska köras under den första serien. Antalet poster under varje tidsintervall representeras av diagramkolumner. |
| Åtgärd |Åtgärd att utföra på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Sum: Summan av värdena från alla poster.</li><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Senaste exempel: värdet från det sista intervallet som ingår i diagrammet.</li><li>Exempel på först: värdet från det första intervallet som ingår i diagrammet.</li><li>Antal: Antal alla poster som returneras av frågan.</li></ul> |
| **Lista** | |
| Söka i data |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="information-part"></a>Informationsdel
Rubriken visar statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text.

![Visa information](media/log-analytics-view-designer-parts/view-information.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Färg |Bakgrundsfärg för rubriken. |
| **Rubrik** | |
| Bild |Bildfilen som visas i rubriken. |
| Etikett |Den text som visas i rubriken. |
| **Rubrik** |**> Länk** |
| Etikett |Länktext. |
| URL |URL: en för länken. |
| **Informationsobjekt** | |
| Titel |Den text som visas för rubriken för varje objekt. |
| Innehåll |Den text som visas för varje objekt. |

## <a name="line-chart-callout-and-list-part"></a>Linjediagram, bildtext och lista delar
Rubriken visar ett linjediagram med flera serier från en loggfråga över tid och en uppmaning med ett sammanfattande värde. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Linjediagram, bildtext och listvy](media/log-analytics-view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik** | |
| Titel |Den text som visas överst i rubriken. |
| Underrubrik |Den text som visas under rubriken överst i rubriken. |
| **Linjediagram** | |
| Söka i data |Frågan ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln i diagrammet använder det här tidsintervallet. Om frågan inte innehåller den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Linjediagram** |**> Bildtext** |
| Bildtextrubrik |Den text som visas ovanför bildtextvärdet. |
| Seriens namn |Egenskapsvärdet för serien för värde för pratbubbla. Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Åtgärd att utföra på värdeegenskapen och sammanfatta som ett enskilt värde för bildtexten.<ul><li>Genomsnittlig: Medelvärdet av värdena från alla poster.</li><li>Antal: Antal alla poster som returneras av frågan.</li><li>Senaste exempel: värdet från det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet mellan intervall som ingår i diagrammet.</li><li>Min: Det minsta värdet mellan intervall som ingår i diagrammet.</li><li>Sum: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |
| **Lista** | |
| Söka i data |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="line-chart-and-list-part"></a>En del diagrammet och listan av rad
Rubriken visar ett linjediagram med flera serier från en loggfråga över tid. I listan visas de översta tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess ändras över tid.

![Vy för diagrammet och listan av rad](media/log-analytics-view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| Använd ikon |Välj länken visas ikonen. |
| **Rubrik** | |
| Titel |Den text som visas överst i rubriken. |
| Underrubrik |Den text som visas under rubriken överst i rubriken. |
| **Linjediagram** | |
| Söka i data |Frågan ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln i diagrammet använder det här tidsintervallet. Om frågan inte innehåller den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Linjediagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |
| **Lista** | |
| Söka i data |Frågan ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken för att inaktivera diagrammet längst till höger i numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Färg |Färgen på staplarna och miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Namn och värde avgränsare |Avgränsaren som tecken ska använda för att parsa text-egenskapen till flera värden. Mer information finns i [gemensamma inställningar för](#sparklines). |
| Klicka igenom navigering | Åtgärd när du klickar på ett objekt i listan.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Lista** |**> Kolumnrubriker** |
| Namn |Den text som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken för att aktivera tröskelvärden. Mer information finns i [gemensamma inställningar för](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stack för rad diagram del
Stack med linjediagram visar tre separata linjediagram med flera serier från en loggfråga över tid, som visas här:

![Stack med linjediagram](media/log-analytics-view-designer-parts/view-stack-line-charts.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken för att skapa en ny grupp i vyn, med början vid den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i rubriken. |
| **Skapa diagram över 1<br>diagram 2<br>diagrammets 3** |**> Rubrik** |
| Titel |Den text som visas överst i diagrammet. |
| Underrubrik |Den text som visas under rubriken överst i diagrammet. |
| **Skapa diagram över 1<br>diagram 2<br>diagrammets 3** |**Linjediagram** |
| Söka i data |Frågan ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt den *mått* nyckelord och sammanfatta resultat. Om frågan använder den *intervall* nyckelord, x-axeln i diagrammet använder det här tidsintervallet. Om frågan inte innehåller den *intervall* nyckelordet, x-axeln använder per timme intervall. |
| Klicka igenom navigering | Åtgärd när du klickar på rubriken.  Mer information finns i [gemensamma inställningar för](#click-through-navigation). |
| **Diagram** |**> Y-axeln** |
| Använda logaritmisk skala |Välj den här länken för att använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheter för värden som returneras av frågan. Den här informationen används för att visa diagrammet etiketter som beskriver värdetyperna och du kan också konvertera värdena. Den *enhet* typen anger kategorin för enheten och definierar de tillgängliga *aktuella enheten* skriver värden. Om du väljer ett värde i *konvertera till*, de numeriska värdena konverteras från den *aktuella enheten* skriver till den *konvertera till* typen. |
| Anpassad etikett |Den text som visas för y-axeln bredvid etiketten för den *enhet* typen. Om ingen etikett anges endast den *enhet* visas. |

## <a name="common-settings"></a>Vanliga inställningar
I följande avsnitt beskrivs inställningar som är gemensamma för flera olika delar av visualiseringen.

### <a name="name-value-separator"></a>Namn och värde avgränsare
Avgränsaren som namn och värde är den du använder för att parsa textegenskapen från en listfråga till flera värden avgränsaren. Om du anger en avgränsare, kan du ange namn för varje fält, avgränsade med samma avgränsare i den **namn** box.

Anta exempelvis att en egenskap som kallas *plats* som finns med värden som *Redmond byggnad 41* och *Bellevue byggnad 12*. Du kan ange ett bindestreck (-) för namnet och värdet avgränsare och *Stad Byggnad* för namnet. Den här metoden Parsar varje värde i två egenskaper som kallas *Stad* och *byggnad*.

### <a name="click-through-navigation"></a>Genomklickningsnavigering
Klicka igenom navigering definierar vilken åtgärd som ska vidtas när du klickar på en huvud- eller listobjekt i en vy.  Då öppnas en fråga i antingen den [loggsökningsportalen](log-analytics-log-search-portals.md) eller starta en annan vy.

I följande tabell beskrivs inställningarna för klicka igenom navigering.

| Inställning           | Beskrivning |
|:--|:--|
| Loggsökning (automatisk) | Loggsökning ska köras när du väljer en rubrik-objekt.  Det här är samma loggsökningen som objektet baseras på.
| Loggsökning        | Loggsökning ska köras när du väljer ett objekt i en lista.  Skriv frågan i den **navigeringsfråga** box.   Använd *{markerade objektet}* att inkludera syntaxen för det objekt som användaren har valt.  Exempel: Om frågan har en kolumn med namnet *datorn* och navigering frågan är *{markerade objektet}*, en fråga som *datorn = ”den här datorn”* körs när du väljer en dator. Om frågan navigering är *typ = händelse {markerade objektet}*, frågan *typ = händelse Computer = ”den här datorn”* körs. |
| Visa              | Vy som ska öppnas när du väljer ett sidhuvud eller ett objekt i en lista.  Välj namnet på en vy i din arbetsyta i den **vynamn** box. |



### <a name="sparklines"></a>Miniatyrdiagram
Miniatyrdiagram är ett litet linjediagram som visar värdet för en listpost över tid. För visualisering delar med en lista, kan du välja om du vill visa ett vågrätt fält som anger det relativa värdet för en numerisk kolumn eller ett miniatyrdiagram, som anger dess värde över tid.

I följande tabell beskrivs inställningarna för miniatyrdiagram:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera miniatyrdiagram |Välj den här länken för att visa ett miniatyrdiagram i stället för ett vågrätt fält. |
| Åtgärd |Om miniatyrdiagram är aktiverade, är åtgärden som ska utföras på varje egenskap i listan för att beräkna värdena för miniatyrdiagrammet.<ul><li>Senaste exempel: det sista värdet för serien över ett tidsintervall.</li><li>Max: Det maximala värdet för seriens över ett tidsintervall.</li><li>Min: Det minsta värdet för seriens över ett tidsintervall.</li><li>Sum: Summan av värdena för serien över ett tidsintervall.</li><li>Sammanfattning: Använder samma `measure` kommandot eftersom frågan i rubriken.</li></ul> |

### <a name="thresholds"></a>Tröskelvärden
Du kan visa en färgad ikon bredvid varje objekt i en lista med hjälp av tröskelvärden. Tröskelvärden ger dig en snabb visuell indikering för objekt som överskrider ett visst värde eller faller inom ett visst intervall. Du kan till exempel visa en grön ikon för artiklar med ett giltigt värde, gult om värdet är inom ett intervall som indikerar en varning och rött om det överskrider ett felvärde.

När du aktiverar tröskelvärden för en del måste du ange en eller flera tröskelvärden. Om värdet för ett objekt är större än ett tröskelvärde och lägre än tröskelvärdet för nästa, används färg för detta värde. Om objektet är större än det högsta tröskelvärdet, används en annan färg. 

Varje uppsättning tröskelvärdet har ett tröskelvärde med värdet **standard**. Det här är den färg som anges om inga andra värden har överskridits. Du kan lägga till eller ta bort tröskelvärden genom att välja den **Lägg till** (+) eller **ta bort** (x)-knappen.

I följande tabell beskrivs inställningarna för tröskelvärden:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera tröskelvärden |Välj den här länken för att visa en färg-ikonen längst till vänster på varje värde. Ikonen visar värdets hälsotillstånd i förhållande till angivna tröskelvärden. |
| Namn |Namnet på tröskelvärdet. |
| Tröskelvärde |Värdet för tröskelvärdet. Färgen för hälsotillståndet för varje listobjekt har angetts till färgen på det högsta tröskelvärdet överskrids med objektets värde. Om inget tröskelvärden överskrids, används en standardfärg. |
| Färg |Färgen som anger tröskelvärdet. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggsökningar](log-analytics-queries.md) för frågorna i delar av visualiseringen.
