---
title: En referensguide till View Designer-delarna i Azure Monitor | Microsoft-dokument
description: Genom att använda View Designer i Azure Monitor kan du skapa anpassade vyer som visas i Azure-portalen och innehåller en mängd olika visualiseringar på data på log analytics-arbetsytan. Den här artikeln är en referensguide till inställningarna för visualiseringsdelar som är tillgängliga i dina anpassade vyer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 7dc4164cac1998a02ae62d7682f9630aa4faa619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658565"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Referensguide till Visualiseringsdelar för Visa designer i Azure Monitor
Genom att använda View Designer i Azure Monitor kan du skapa en mängd anpassade vyer i Azure-portalen som kan hjälpa dig att visualisera data på din Log Analytics-arbetsyta. Den här artikeln är en referensguide till inställningarna för visualiseringsdelar som är tillgängliga i dina anpassade vyer.

Mer information om View Designer finns i:

* [View Designer](view-designer.md): Ger en översikt över Visa designer och procedurer för att skapa och redigera anpassade vyer.
* [Panelreferens:](view-designer-tiles.md)Innehåller en referens till inställningarna för varje tillgänglig panel i dina anpassade vyer.


De tillgängliga paneltyperna Visa designer beskrivs i följande tabell:

| Visa typ | Beskrivning |
|:--- |:--- |
| [Lista med frågor](#list-of-queries-part) |Visar en lista över loggfrågor. Du kan välja varje fråga för att visa dess resultat. |
| [Antal och lista](#number-and-list-part) |Rubriken visar ett enskilt nummer som visar antalet poster från en loggfråga. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden. |
| [Två siffror och lista](#two-numbers-and-list-part) |Rubriken visar två tal som visar antalet poster från separata loggfrågor. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden. |
| [Donut och lista](#donut-and-list-part) |Rubriken visar ett enskilt tal som summerar en värdekolumn i en loggfråga. Donuten visar grafiskt resultaten av de tre översta posterna. |
| [Två tidslinjer och lista](#two-timelines-and-list-part) |Rubriken visar resultatet av två loggfrågor över tid som stapeldiagram, med en bildtext som visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden. |
| [Information](#information-part) |Rubriken visar statisk text och en valfri länk. Listan visar ett eller flera objekt med en statisk rubrik och text. |
| [Linjediagram, bildtext och lista](#line-chart-callout-and-list-part) |Rubriken visar ett linjediagram, med flera serier från en loggfråga över tid och en bildtext med ett sammanfattat värde. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden. |
| [Linjediagram och lista](#line-chart-and-list-part) |Rubriken visar ett linjediagram med flera serier från en loggfråga över tid. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden. |
| [Stapel med linjediagram del](#stack-of-line-charts-part) |Visar tre separata linjediagram, med flera serier från en loggfråga över tid. |

I nästa avsnitt beskrivs paneltyperna och deras egenskaper i detalj.

> [!NOTE]
> Delar i vyer baseras på [loggfrågor](../log-query/log-query-overview.md) på din Log Analytics-arbetsyta. De stöder för närvarande inte [korsresursfrågor](../log-query/cross-workspace-query.md) för att hämta data från Application Insights.

## <a name="list-of-queries-part"></a>Lista över frågor del
Listan över frågor del visar en lista över loggfrågor. Du kan välja varje fråga för att visa dess resultat. Vyn innehåller en enda fråga som standard och du kan välja **+ Fråga** om du vill lägga till ytterligare frågor.

![Lista över frågor vy](media/view-designer-parts/view-list-queries.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Titel |Texten som visas högst upp i vyn. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Förvalda filter |En kommaavgränsad lista med egenskaper som ska inkluderas i den vänstra filterrutan när du väljer en fråga. |
| Återgivningsläge |Den första vyn som visas när frågan är markerad. Du kan välja alla tillgängliga vyer när de har öppnat frågan. |
| **Frågor** | |
| Sökfråga |Frågan som ska köras. |
| Eget namn | Det beskrivande namn som visas. |

## <a name="number-and-list-part"></a>Antal och listdel
Rubriken visar ett enskilt nummer som visar antalet poster från en loggfråga. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden.

![Lista över frågor vy](media/view-designer-parts/view-number-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp i vyn. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Titel** | |
| Förklaring |Texten som visas högst upp i sidhuvudet. |
| Söka i data |Frågan som ska köras för huvudet. Antalet poster som returneras av frågan visas. |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. De två första egenskaperna för de första tio posterna i resultatet visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt som baseras på det relativa värdet i den numeriska kolumnen.<br><br>Använd `Sort` kommandot i frågan för att sortera posterna i listan. Om du vill köra frågan och returnera alla poster kan du välja **Visa alla**. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. Mer information finns i [Vanliga inställningar](#sparklines). |
| Color |Färgen på staplarna eller miniatyrdiagrammen. |
| Namn- och värdeavgränsare |Den enteckensavgränsare som ska användas för att tolka textegenskapen till flera värden. Mer information finns i [Vanliga inställningar](#sparklines). |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på ett objekt i listan.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumnrubriker** |
| Namn |Texten som visas högst upp i den första kolumnen. |
| Värde |Texten som visas högst upp i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [Vanliga inställningar](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Två siffror och listdel
Huvudet har två tal som visar antalet poster från separata loggfrågor. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden.

![Två & listvy](media/view-designer-parts/view-two-numbers-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp i vyn. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Rubrik navigering** | |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Titel** | |
| Förklaring |Texten som visas högst upp i sidhuvudet. |
| Söka i data |Frågan som ska köras för huvudet. Antalet poster som returneras av frågan visas. |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. De två första egenskaperna för de första tio posterna i resultatet visas. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt baserat på det relativa värdet i den numeriska kolumnen.<br><br>Använd `Sort` kommandot i frågan för att sortera posterna i listan. Om du vill köra frågan och returnera alla poster kan du välja **Visa alla**. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. Mer information finns i [Vanliga inställningar](#sparklines). |
| Color |Färgen på staplarna eller miniatyrdiagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyrdiagram. Mer information finns i [Vanliga inställningar](#sparklines). |
| Namn- och värdeavgränsare |Den enteckensavgränsare som ska användas för att tolka textegenskapen till flera värden. Mer information finns i [Vanliga inställningar](#sparklines). |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på ett objekt i listan.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumnrubriker** |
| Namn |Texten som visas högst upp i den första kolumnen. |
| Värde |Texten som visas högst upp i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [Vanliga inställningar](#thresholds). |

## <a name="donut-and-list-part"></a>Donut och lista del
Rubriken visar ett enskilt tal som summerar en värdekolumn i en loggfråga. Donuten visar grafiskt resultaten av de tre översta posterna.

![Donut- och listvy](media/view-designer-parts/view-donut-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp på panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Huvudet** | |
| Titel |Texten som visas högst upp i sidhuvudet. |
| Underrubrik |Texten som visas under rubriken högst upp i sidhuvudet. |
| **Ring** | |
| Söka i data |Frågan som ska köras för munken. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Ring** |**> center** |
| Text |Texten som visas under värdet inuti munken. |
| Åtgärd |Åtgärden som ska utföras på egenskapen value för att sammanfatta den som ett enda värde.<ul><li>Summa: Lägger till värdena för alla poster.</li><li>Procent: Förhållandet mellan de poster som returneras av värdena i **resultatvärden som används i mittenåtgärden** och de totala posterna i frågan.</li></ul> |
| Resultatvärden som används vid mittoperation |Du kan också välja plustecknet (+) för att lägga till ett eller flera värden. Resultatet av frågan är begränsat till poster med de egenskapsvärden som du anger. Om inga värden läggs till inkluderas alla poster i frågan. |
| **Ytterligare alternativ** |**> färger** |
| Färg 1<br>Färg 2<br>Färg 3 |Välj färg för vart och ett av de värden som visas i munken. |
| **Ytterligare alternativ** |**> avancerad färgmappning** |
| Fältvärde |Skriv namnet på ett fält för att visa det som en annan färg om det ingår i munken. |
| Color |Välj färg för det unika fältet. |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. Mer information finns i [Vanliga inställningar](#sparklines). |
| Color |Färgen på staplarna eller miniatyrdiagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyrdiagram. Mer information finns i [Vanliga inställningar](#sparklines). |
| Namn- och värdeavgränsare |Den enteckensavgränsare som ska användas för att tolka textegenskapen till flera värden. Mer information finns i [Vanliga inställningar](#sparklines). |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på ett objekt i listan.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumnrubriker** |
| Namn |Texten som visas högst upp i den första kolumnen. |
| Värde |Texten som visas högst upp i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [Vanliga inställningar](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Två tidslinjer och listdel
Rubriken visar resultatet av två loggfrågor över tid som stapeldiagram, med en bildtext som visar ett enda tal som sammanfattar en värdekolumn i en loggfråga. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden.

![Två tidslinjer och listvy](media/view-designer-parts/view-two-timelines-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp på panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Rubrik navigering** | |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Första<br>diagrammet Andra diagrammet** | |
| Förklaring |Texten som visas under bildtexten för den första serien. |
| Color |Färgen som ska användas för kolumnerna i serien. |
| Söka i data |Frågan som ska köras för den första serien. Antalet poster under varje tidsintervall representeras av diagramkolumnerna. |
| Åtgärd |Åtgärden som ska utföras på egenskapen value för att sammanfatta den som ett enda värde för bildtexten.<ul><li>Summa: Summan av värdena från alla poster.</li><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Senaste exempel: Värdet från det sista intervallet som ingår i diagrammet.</li><li>Första exemplet: Värdet från det första intervallet som ingår i diagrammet.</li><li>Antal: Antalet poster som returneras av frågan.</li></ul> |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. Mer information finns i [Vanliga inställningar](#sparklines). |
| Color |Färgen på staplarna eller miniatyrdiagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyrdiagram. Mer information finns i [Vanliga inställningar](#sparklines). |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på ett objekt i listan.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumnrubriker** |
| Namn |Texten som visas högst upp i den första kolumnen. |
| Värde |Texten som visas högst upp i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [Vanliga inställningar](#thresholds). |

## <a name="information-part"></a>Informationsdel
Rubriken visar statisk text och en valfri länk. Listan visar ett eller flera objekt med en statisk rubrik och text.

![Informationsvy](media/view-designer-parts/view-information.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp på panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Color |Bakgrundsfärgen för sidhuvudet. |
| **Huvudet** | |
| Bild |Bildfilen som visas i sidhuvudet. |
| Label (Etikett) |Texten som visas i sidhuvudet. |
| **Huvudet** |**> Länk** |
| Label (Etikett) |Länktexten. |
| URL |Url:en för länken. |
| **Informationsobjekt** | |
| Titel |Texten som visas för titeln på varje objekt. |
| Innehåll |Texten som visas för varje objekt. |

## <a name="line-chart-callout-and-list-part"></a>Linjediagram, bildtext och listdel
Rubriken visar ett linjediagram med flera serier från en loggfråga över tid och en bildtext med ett sammanfattat värde. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden.

![Linjediagram, bildtext och listvy](media/view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp på panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Huvudet** | |
| Titel |Texten som visas högst upp i sidhuvudet. |
| Underrubrik |Texten som visas under rubriken högst upp i sidhuvudet. |
| **Linjediagram** | |
| Söka i data |Frågan som ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt *nyckelordet mått* för att sammanfatta resultaten. Om nyckelordet för frågan använder *nyckelordet intervall* används det här tidsintervallet i diagrammets x-axel. Om frågan inte innehåller *nyckelordet intervall* använder x-axeln timintervall. |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Linjediagram** |**> Bildtext** |
| Bildtexttitel |Texten som visas ovanför bildtextvärdet. |
| Seriens namn |Egenskapsvärde för serien som ska användas för bildtextvärdet. Om ingen serie anges används alla poster från frågan. |
| Åtgärd |Åtgärden som ska utföras på egenskapen value för att sammanfatta den som ett enda värde för bildtexten.<ul><li>Medelvärde: Medelvärdet av värdena från alla poster.</li><li>Antal: Antalet poster som returneras av frågan.</li><li>Senaste exempel: Värdet från det sista intervallet som ingår i diagrammet.</li><li>Max: Det maximala värdet från intervallen som ingår i diagrammet.</li><li>Min: Det minsta värdet från intervallen som ingår i diagrammet.</li><li>Summa: Summan av värdena från alla poster.</li></ul> |
| **Linjediagram** |**> Y-axeln** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagrametiketter som anger värdetyperna och eventuellt för att konvertera värdena. Enhetstypen anger enhetens kategori och definierar tillgängliga värden för *Unit* *aktuella enhetstyp.* Om du väljer ett värde i *Konvertera till*konverteras de numeriska värdena från typen *Aktuell enhet* till typen *Konvertera till.* |
| Anpassad etikett |Texten som visas för y-axeln bredvid etiketten för *Unit* enhetstypen. Om ingen etikett har angetts visas endast enhetstypen. *Unit* |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. Mer information finns i [Vanliga inställningar](#sparklines). |
| Color |Färgen på staplarna eller miniatyrdiagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyrdiagram. Mer information finns i [Vanliga inställningar](#sparklines). |
| Namn- och värdeavgränsare |Den enteckensavgränsare som ska användas för att tolka textegenskapen till flera värden. Mer information finns i [Vanliga inställningar](#sparklines). |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på ett objekt i listan.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumnrubriker** |
| Namn |Texten som visas högst upp i den första kolumnen. |
| Värde |Texten som visas högst upp i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [Vanliga inställningar](#thresholds). |

## <a name="line-chart-and-list-part"></a>Linjediagram och listdel
Rubriken visar ett linjediagram med flera serier från en loggfråga över tid. Listan visar de tio bästa resultaten från en fråga, med ett diagram som anger det relativa värdet för en numerisk kolumn eller dess förändring över tiden.

![Linjediagram och listvy](media/view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp på panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Huvudet** | |
| Titel |Texten som visas högst upp i sidhuvudet. |
| Underrubrik |Texten som visas under rubriken högst upp i sidhuvudet. |
| **Linjediagram** | |
| Söka i data |Frågan som ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt *nyckelordet mått* för att sammanfatta resultaten. Om nyckelordet för frågan använder *nyckelordet intervall* används det här tidsintervallet i diagrammets x-axel. Om frågan inte innehåller *nyckelordet intervall* använder x-axeln timintervall. |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Linjediagram** |**> Y-axeln** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagrametiketter som anger värdetyperna och eventuellt för att konvertera värdena. Enhetstypen anger enhetens kategori och definierar tillgängliga värden för *Unit* *aktuella enhetstyp.* Om du väljer ett värde i *Konvertera till*konverteras de numeriska värdena från typen *Aktuell enhet* till typen *Konvertera till.* |
| Anpassad etikett |Texten som visas för y-axeln bredvid etiketten för *Unit* enhetstypen. Om ingen etikett har angetts visas endast enhetstypen. *Unit* |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. Mer information finns i [Vanliga inställningar](#sparklines). |
| Color |Färgen på staplarna eller miniatyrdiagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyrdiagram. Mer information finns i [Vanliga inställningar](#sparklines). |
| Namn- och värdeavgränsare |Den enteckensavgränsare som ska användas för att tolka textegenskapen till flera värden. Mer information finns i [Vanliga inställningar](#sparklines). |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på ett objekt i listan.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumnrubriker** |
| Namn |Texten som visas högst upp i den första kolumnen. |
| Värde |Texten som visas högst upp i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [Vanliga inställningar](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stapel med linjediagram del
Stapeln med linjediagram visar tre separata linjediagram, med flera serier från en loggfråga över tid, som visas här:

![Stapel med linjediagram](media/view-designer-parts/view-stack-line-charts.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupprubrik |Texten som visas högst upp på panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn, med början i den aktuella vyn. |
| Ikon |Bildfilen som visas bredvid resultatet i sidhuvudet. |
| **Diagram 1<br>Diagram<br>2 Diagram 3** |**> sidhuvud** |
| Titel |Texten som visas högst upp i diagrammet. |
| Underrubrik |Texten som visas under rubriken högst upp i diagrammet. |
| **Diagram 1<br>Diagram<br>2 Diagram 3** |**Linjediagram** |
| Söka i data |Frågan som ska köras för linjediagrammet. Den första egenskapen är ett textvärde och den andra egenskapen är ett numeriskt värde. Den här frågan använder normalt *nyckelordet mått* för att sammanfatta resultaten. Om nyckelordet för frågan använder *nyckelordet intervall* används det här tidsintervallet i diagrammets x-axel. Om frågan inte innehåller *nyckelordet intervall* använder x-axeln timintervall. |
| Klicka-igenom navigering | Åtgärder som vidtas när du klickar på rubriken.  Mer information finns i [Vanliga inställningar](#click-through-navigation). |
| **Diagram** |**> Y-axeln** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagrametiketter som anger värdetyperna och eventuellt för att konvertera värdena. Enhetstypen anger enhetens kategori och definierar tillgängliga värden för *Unit* *aktuella enhetstyp.* Om du väljer ett värde i *Konvertera till*konverteras de numeriska värdena från typen *Aktuell enhet* till typen *Konvertera till.* |
| Anpassad etikett |Texten som visas för y-axeln bredvid etiketten för *Unit* enhetstypen. Om ingen etikett har angetts visas endast enhetstypen. *Unit* |

## <a name="common-settings"></a>Vanliga inställningar
I följande avsnitt beskrivs inställningar som är gemensamma för flera visualiseringsdelar.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Namn- och värdeavgränsare
Namn- och värdeavgränsaren är den enteckensavgränsare som ska användas för att tolka textegenskapen från en listfråga till flera värden. Om du anger en avgränsare kan du ange namn för varje fält, avgränsat med samma avgränsare i rutan **Namn.**

Tänk dig till exempel en egenskap som heter *Plats* som inkluderade värden som *Redmond-Building 41* och *Bellevue-Building 12*. Du kan ange ett streck (-) för namn- och värdeavgränsaren och *Stadsbyggnad* för namnet. Den här metoden tolkar varje värde i två egenskaper som kallas *Stad* och *Byggnad*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Genomgående navigering
Klicknavigering definierar vilken åtgärd som ska vidtas när du klickar på ett sidhuvud eller listobjekt i en vy.  Då öppnas en fråga i [Logganalysen](../../azure-monitor/log-query/portals.md) eller så starta en annan vy.

I följande tabell beskrivs inställningarna för klicknavigering.

| Inställning           | Beskrivning |
|:--|:--|
| Logga sökning (Auto) | Loggfråga som ska köras när du väljer ett huvudobjekt.  Det här är samma loggfråga som objektet baseras på.
| Loggsökning        | Loggfråga som ska köras när du markerar ett objekt i en lista.  Skriv frågan i **frågerutan Navigering.**   Använd *{markerat objekt}* om du vill ta med syntaxen för det objekt som användaren har markerat.  Om frågan till exempel har en kolumn med namnet *Dator* och navigeringsfrågan är *{markerat objekt}* körs en fråga som *Computer="MyComputer"* när du väljer en dator. Om navigeringsfrågan är *Type=Event {selected item}* körs frågan *Type=Event Computer="MyComputer".* |
| Visa              | Visa om du vill öppna när du markerar ett rubrikobjekt eller ett objekt i en lista.  Markera namnet på en vy på arbetsytan i rutan **Visa namn.** |



### <a name="sparklines"></a><a name="sparklines"></a>Miniatyrdiagram
En miniatyrdiagram är ett litet linjediagram som illustrerar värdet för en listpost över tid. För visualiseringsdelar med en lista kan du välja om du vill visa ett vågrätt fält, vilket anger det relativa värdet för en numerisk kolumn eller en miniatyrdiagram, som anger dess värde över tid.

I följande tabell beskrivs inställningarna för miniatyrdiagram:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera miniatyrdiagram |Markera den här länken om du vill visa en miniatyrlinje i stället för ett vågrät fält. |
| Åtgärd |Om miniatyrdiagram är aktiverade är det här åtgärden som ska utföras på varje egenskap i listan för att beräkna värdena för miniatyrdiagrammen.<ul><li>Senaste exempel: Det sista värdet för serien under tidsintervallet.</li><li>Max: Det maximala värdet för serien under tidsintervallet.</li><li>Min: Det minsta värdet för serien under tidsintervallet.</li><li>Summa: Summan av värdena för serien under tidsintervallet.</li><li>Sammanfattning: Använder `measure` samma kommando som frågan i huvudet.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Tröskelvärden
Med hjälp av tröskelvärden kan du visa en färgad ikon bredvid varje objekt i en lista. Trösklar ger dig en snabb visuell indikator på objekt som överskrider ett visst värde eller faller inom ett visst intervall. Du kan till exempel visa en grön ikon för objekt med ett acceptabelt värde, gult om värdet ligger inom ett intervall som indikerar en varning och rött om det överskrider ett felvärde.

När du aktiverar tröskelvärden för en del måste du ange ett eller flera tröskelvärden. Om värdet för en artikel är större än ett tröskelvärde och lägre än nästa tröskelvärde används färgen för det värdet. Om objektet är större än det högsta tröskelvärdet används en annan färg. 

Varje tröskelvärde har ett tröskelvärde med värdet **Standard**. Det här är färgen som anges om inga andra värden överskrids. Du kan lägga till eller ta bort tröskelvärden genom att välja knappen **Lägg till** (+) eller **Ta bort** (x).

I följande tabell beskrivs inställningarna för tröskelvärden:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera tröskelvärden |Välj den här länken om du vill visa en färgikon till vänster om varje värde. Ikonen anger värdets hälsotillstånd i förhållande till angivna tröskelvärden. |
| Namn |Namnet på tröskelvärdet. |
| Tröskelvärde |Värdet för tröskelvärdet. Hälsofärgen för varje listobjekt anges till färgen på det högsta tröskelvärdet som överskrids av objektets värde. Om inga tröskelvärden överskrids används en standardfärg. |
| Color |Färgen som anger tröskelvärdet. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [loggfrågor](../log-query/log-query-overview.md) för att stödja frågor i visualiseringsdelar.
