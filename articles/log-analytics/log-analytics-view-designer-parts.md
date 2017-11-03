---
title: "Del referens för Vydesigner i OMS Log Analytics | Microsoft Docs"
description: "Vydesigner i logganalys kan du skapa anpassade vyer i OMS-konsol som innehåller olika visuella data i OMS-databasen. Den här artikeln innehåller en referens i inställningarna för var och en av de visualisering delarna som är tillgänglig för användning i din anpassade vyer."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 40a6101576708936404447576d704a49666143fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Log Analytics Vydesigner visualiseringen del referens
Vydesigner i logganalys kan du skapa anpassade vyer i OMS-konsol som innehåller olika visuella data från OMS-databasen. Den här artikeln innehåller en referens i inställningarna för var och en av de visualisering delarna som är tillgänglig för användning i din anpassade vyer.

Andra artiklar som är tillgängliga för Vydesigner är:

* [Visa Designer](log-analytics-view-designer.md) -översikt över Vydesigner och procedurer för att skapa och redigera anpassade vyer.
* [Panelen referens](log-analytics-view-designer-tiles.md) -referens i inställningarna för vart och ett av rutor tillgänglig för användning i din anpassade vyer.

>[!NOTE]
> Om ditt arbetsområde har uppgraderats till den [nya Log Analytics-frågespråket](log-analytics-log-search-upgrade.md), och sedan frågor i alla visningslägen måste skrivas den [nya frågespråket](https://go.microsoft.com/fwlink/?linkid=856078).  Alla vyer som har skapats innan arbetsytan har uppgraderats kommer att automtically konverteras.

I följande tabell beskrivs de olika typerna av paneler som är tillgängliga i View Designer.  I avsnitten nedan beskrivs varje typ av panelen i detalj och deras egenskaper.

| Typ av vy | Beskrivning |
|:--- |:--- |
| [Listan med frågor](#list-of-queries-part) |Visar en lista över loggen sökfrågor.  Användaren kan klicka på varje fråga för att visa dess resultat. |
| [Antal & lista](#number-amp-list-part) |Rubriken har enda antalet visar antalet poster från en sökfråga för loggen.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Två siffror & lista](#two-numbers-amp-list-part) |Rubriken har två tal visar antalet poster från separat loggen sökfrågor.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Ring & lista](#donut-amp-list-part) |Huvudet visar ett tal sammanfattats från en med en kolumn i en fråga i loggen.  Ringen visar grafiskt resultaten av de tre översta posterna. |
| [Två tidslinjer & lista](#two-timelines-amp-list-part) |Rubriken visas resultatet av två loggen frågor när kolumndiagram med en uppmaning visas ett tal sammanfattats från en med en kolumn i en fråga i loggen.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Information](#information-part) |Huvudet visar statisk text och en valfri länk.  Listan visar ett eller flera objekt med statisk text och rubrik. |
| [Linjediagram, callout & lista](#line-chart-callout-amp-list-part) |Huvudet visar ett linjediagram med flera serier från en fråga logg över tid och en kommentar med ett summerat värde.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Linjediagram & lista](#line-chart-amp-list-part) |Huvudet visar ett linjediagram med flera serier från en fråga logg över tid.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid. |
| [Stacken för rad diagram del](#stack-of-line-charts-part) |Visar tre separata linjediagram med flera serier från en fråga logg över tid. |

## <a name="list-of-queries-part"></a>Lista över en del frågor
Visar en lista över loggen sökfrågor.  Användaren kan klicka på varje fråga för att visa dess resultat.  Vyn innehåller en enda fråga som standard och du kan klicka på **+ frågan** att lägga till ytterligare frågor.

![Lista över frågor vy](media/log-analytics-view-designer/view-list-queries.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Rubrik |Text som visas överst i vyn. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Förvald filter |Kommaavgränsad lista över egenskaper som ska ingå i den vänstra filterfönstret när användaren väljer en fråga. |
| Återge läge |Inledande vyn som visas när frågan är markerad.  Användaren kan välja alla vyer som är tillgängliga när du har öppnat frågan. |
| **Frågor** | |
| Sökfråga |Frågan ska köras. |
| Eget namn |Beskrivande namn på frågan för att visa för användaren. |

## <a name="number--list-part"></a>Antal & lista del
Rubriken har enda antalet visar antalet poster från en sökfråga för loggen.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Lista över frågor vy](media/log-analytics-view-designer/view-number-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i vyn. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| Använd ikon |Välj ikonen visas. |
| **Rubrik** | |
| Förklaring |Text som visas överst i huvudet. |
| Fråga |Frågan ska köras för sidhuvudet.  Antalet poster som returneras av frågan ska visas. |
| **Lista** | |
| Fråga |Frågan ska köras för listan.  Första två egenskaperna för de första tio posterna i resultatet visas.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Staplar skapas automatiskt utifrån relativa värdet för numerisk kolumn.<br><br>Använd kommandot Sortera i frågan för att sortera posterna i listan.  Användaren kan välja finns alla om du vill köra frågan och alla poster. |
| Dölj diagram |Välj det här alternativet om du vill inaktivera diagrammet till höger om numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Färg |Färgen för staplarna eller miniatyrdiagram. |
| Namn och Värdesavgränsare |Enskild avgränsaren om du vill tolka textegenskapen i flera värden.  Se [gemensamma inställningar för](#name-value-separator) mer information. |
| Navigering fråga |Frågan ska köras när användaren väljer ett objekt i listan.  Se [gemensamma inställningar för](#navigation-query) mer information. |
| **Lista** |**> Kolumnrubriker** |
| Namn |Text som visas överst i den första kolumnen i listan. |
| Värde |Text som visas överst i den andra kolumnen i listan. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj det här alternativet om du vill aktivera tröskelvärden.  Se [gemensamma inställningar för](#thresholds) mer information. |

## <a name="two-numbers--list-part"></a>Två tal s & lista
Rubriken har två tal visar antalet poster från separat loggen sökfrågor.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Två tal & listvy](media/log-analytics-view-designer/view-two-numbers-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i vyn. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| Använd ikon |Välj ikonen visas. |
| **Rubrik** | |
| Förklaring |Text som visas överst i huvudet. |
| Fråga |Frågan ska köras för sidhuvudet.  Antalet poster som returneras av frågan ska visas. |
| **Lista** | |
| Fråga |Frågan ska köras för listan.  Första två egenskaperna för de första tio posterna i resultatet visas.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Staplar skapas automatiskt utifrån relativa värdet för numerisk kolumn.<br><br>Använd kommandot Sortera i frågan för att sortera posterna i listan.  Användaren kan välja finns alla om du vill köra frågan och alla poster. |
| Dölj diagram |Välj det här alternativet om du vill inaktivera diagrammet till höger om numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Färg |Färgen för staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Namn och Värdesavgränsare |Enskild avgränsaren om du vill tolka textegenskapen i flera värden.  Se [gemensamma inställningar för](#name-value-separator) mer information. |
| Navigering fråga |Frågan ska köras när användaren väljer ett objekt i listan.  Se [gemensamma inställningar för](#navigation-query) mer information. |
| **Lista** |**> Kolumnrubriker** |
| Namn |Text som visas överst i den första kolumnen i listan. |
| Värde |Text som visas överst i den andra kolumnen i listan. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj det här alternativet om du vill aktivera tröskelvärden.  Se [gemensamma inställningar för](#thresholds) mer information. |

## <a name="donut--list-part"></a>En del ringen & lista
Huvudet visar ett tal sammanfattats från en med en kolumn i en fråga i loggen.  Ringen visar grafiskt resultaten av de tre översta posterna.

![Ring & lista vy](media/log-analytics-view-designer/view-donut-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i panelen. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| Använd ikon |Välj ikonen visas. |
| **Huvudet** | |
| Rubrik |Text som visas överst i huvudet. |
| Underrubrik |Text som visas under rubriken överst i huvudet. |
| **Ring** | |
| Fråga |Frågan ska köras för ringen.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde. |
| **Ring** |**> Center** |
| Text |Text som visas under värde i ringen. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde.<br><br>-Summan: Lägg till värdena för alla poster.<br>-Procent: Procentandelen posterna som returneras av värdena i **leda till att värden som används i center åtgärden** för totalt antal poster i frågan. |
| Resultatvärden som används i center åtgärd |Du kan också klicka på plustecknet för att lägga till ett eller flera värden.  Resultatet av frågan kommer att begränsas till poster med egenskapsvärden som du anger.  Om inga värden läggs till, med alla poster i frågan. |
| **Ytterligare alternativ** |**> Färger** |
| Färg 1<br>Färg 2<br>Färg 3 |Välj färg för var och en av de värden som visas i ringen. |
| **Ytterligare alternativ** |**> Avancerade färgmappning** |
| Fältvärde |Skriv namnet på ett fält att visa den som en annan färg om den ingår i ringen. |
| Färg |Välj färg för unikt fält. |
| **Lista** | |
| Fråga |Frågan ska köras för listan.  Antalet poster som returneras av frågan ska visas. |
| Dölj diagram |Välj det här alternativet om du vill inaktivera diagrammet till höger om numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Färg |Färgen för staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Namn och Värdesavgränsare |Enskild avgränsaren om du vill tolka textegenskapen i flera värden.  Se [gemensamma inställningar för](#name-value-separator) mer information. |
| Navigering fråga |Frågan ska köras när användaren väljer ett objekt i listan.  Se [gemensamma inställningar för](#navigation-query) mer information. |
| **Lista** |**> Kolumnrubriker** |
| Namn |Text som visas överst i den första kolumnen i listan. |
| Värde |Text som visas överst i den andra kolumnen i listan. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj det här alternativet om du vill aktivera tröskelvärden.  Se [gemensamma inställningar för](#thresholds) mer information. |

## <a name="two-timelines--list-part"></a>Två tidslinjer & lista del
Rubriken visas resultatet av två loggen frågor när kolumndiagram med en uppmaning visas ett tal sammanfattats från en med en kolumn i en fråga i loggen.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Visa två tidslinjer & lista](media/log-analytics-view-designer/view-two-timelines-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i panelen. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| Använd ikon |Välj ikonen visas. |
| **Först diagrammets<br>andra diagrammet** | |
| Förklaring |Text som visas under callout för den första serien. |
| Färg |Färg som ska användas för kolumner i serien. |
| Fråga |Frågan ska köras under den första serien.  Antalet poster under varje tidsintervall et representeras av diagrammet kolumner. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde för bildtexten.<br><br>-Summan: Summan av värdet från alla poster.<br>-Genomsnittlig: Medelvärdet av värdet från alla poster.<br>-Sista: Exempelvärde från det sista intervallet som ingår i diagrammet.<br>-Det första exemplet: Värde från det första intervallet som ingår i diagrammet.<br>-Antal: Antalet poster som returneras av frågan. |
| **Lista** | |
| Fråga |Frågan ska köras för listan.  Antalet poster som returneras av frågan ska visas. |
| Dölj diagram |Välj det här alternativet om du vill inaktivera diagrammet till höger om numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Färg |Färgen för staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Navigering fråga |Frågan ska köras när användaren väljer ett objekt i listan.  Se [gemensamma inställningar för](#navigation-query) mer information. |
| **Lista** |**> Kolumnrubriker** |
| Namn |Text som visas överst i den första kolumnen i listan. |
| Värde |Text som visas överst i den andra kolumnen i listan. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj det här alternativet om du vill aktivera tröskelvärden.  Se [gemensamma inställningar för](#thresholds) mer information. |

## <a name="information-part"></a>En del information
Huvudet visar statisk text och en valfri länk.  Listan visar ett eller flera objekt med statisk text och rubrik.

![Visa information](media/log-analytics-view-designer/view-information.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i panelen. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Färg |Bakgrundsfärg för rubriken. |
| **Huvudet** | |
| Bild |Bild som ska visas i rubriken. |
| Etikett |Text som visas i rubriken. |
| **Huvudet** |**> Länk** |
| Etikett |Länktext. |
| URL |URL för länken. |
| **Information som** | |
| Rubrik |Text som visas i rubriken för varje objekt. |
| Innehåll |Text som ska visas för varje objekt. |

## <a name="line-chart-callout--list-part"></a>Linjediagram, callout & lista del
Huvudet visar ett linjediagram med flera serier från en fråga logg över tid och en kommentar med ett summerat värde.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Linjediagram, callout & listvy](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i panelen. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| Använd ikon |Välj ikonen visas. |
| **Huvudet** | |
| Rubrik |Text som visas överst i huvudet. |
| Underrubrik |Text som visas under rubriken överst i huvudet. |
| **Linjediagram** | |
| Fråga |Frågan ska köras för linjediagrammet.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Detta är vanligtvis en fråga som använder den **mått** nyckelord för att sammanfatta resultat.  Om frågan använder den **intervall** nyckelordet sedan x-axeln i diagrammet används för detta tidsintervall.  Om frågan inte innehåller den **intervall** nyckelord och varje timme sedan intervall används för x-axeln. |
| **Linjediagram** |**> Callout** |
| Callout-rubrik |Text som visas ovanför callout-värdet. |
| Serienamn |Egenskapsvärdet för serien ska användas för callout-värdet.  Om inga serier anges, används alla poster från frågan. |
| Åtgärd |Åtgärden att utföra på egenskapen value för att sammanfatta ett enskilt värde för bildtexten.<br><br>-Genomsnittlig: Medelvärdet av värdet från alla poster.<br>-Räkna antalet poster som returneras av frågan.<br>-Sista: Exempelvärde från det sista intervallet som ingår i diagrammet.<br>-Max: Maximalt värde från de intervall som ingår i diagrammet.<br>-Min: Minsta värde från de intervall som ingår i diagrammet.<br>-Summan: Summan av värdet från alla poster. |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj det här alternativet om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan.  Den här informationen används för att visa etiketter i diagram som visar värdetyperna och eventuellt för konvertering av värdena.  Enhetstypen anger kategorin för enheten och definierar de typ av aktuella värden som är tillgängliga.  Om du väljer ett värde i konvertera till och sedan de numeriska värdena konverteras från den aktuella enhetstypen ska konverteras till typen. |
| Anpassad etikett |Text som ska visas för Y-axeln bredvid etiketten för enhetstypen.  Om ingen etikett anges visas endast enhetstyp. |
| **Lista** | |
| Fråga |Frågan ska köras för listan.  Antalet poster som returneras av frågan ska visas. |
| Dölj diagram |Välj det här alternativet om du vill inaktivera diagrammet till höger om numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Färg |Färgen för staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Namn och Värdesavgränsare |Enskild avgränsaren om du vill tolka textegenskapen i flera värden.  Se [gemensamma inställningar för](#name-value-separator) mer information. |
| Navigering fråga |Frågan ska köras när användaren väljer ett objekt i listan.  Se [gemensamma inställningar för](#navigation-query) mer information. |
| **Lista** |**> Kolumnrubriker** |
| Namn |Text som visas överst i den första kolumnen i listan. |
| Värde |Text som visas överst i den andra kolumnen i listan. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj det här alternativet om du vill aktivera tröskelvärden.  Se [gemensamma inställningar för](#thresholds) mer information. |

## <a name="line-chart--list-part"></a>En del diagram & lista av raden
Huvudet visar ett linjediagram med flera serier från en fråga logg över tid.  Lista visar de bästa tio resultaten från en fråga med ett diagram som visar relativa värdet för en numerisk kolumn eller dess förändringar över tid.

![Vy för rad diagram & lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i panelen. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| Använd ikon |Välj ikonen visas. |
| **Huvudet** | |
| Rubrik |Text som visas överst i huvudet. |
| Underrubrik |Text som visas under rubriken överst i huvudet. |
| **Linjediagram** | |
| Fråga |Frågan ska köras för linjediagrammet.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Detta är vanligtvis en fråga som använder den **mått** nyckelord för att sammanfatta resultat.  Om frågan använder den **intervall** nyckelordet sedan x-axeln i diagrammet används för detta tidsintervall.  Om frågan inte innehåller den **intervall** nyckelord och varje timme sedan intervall används för x-axeln. |
| **Linjediagram** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj det här alternativet om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan.  Den här informationen används för att visa etiketter i diagram som visar värdetyperna och eventuellt för konvertering av värdena.  Enhetstypen anger kategorin för enheten och definierar de typ av aktuella värden som är tillgängliga.  Om du väljer ett värde i konvertera till och sedan de numeriska värdena konverteras från den aktuella enhetstypen ska konverteras till typen. |
| Anpassad etikett |Text som ska visas för Y-axeln bredvid etiketten för enhetstypen.  Om ingen etikett anges visas endast enhetstyp. |
| **Lista** | |
| Fråga |Frågan ska köras för listan.  Antalet poster som returneras av frågan ska visas. |
| Dölj diagram |Välj det här alternativet om du vill inaktivera diagrammet till höger om numerisk kolumn. |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Färg |Färgen för staplarna eller miniatyrdiagram. |
| Åtgärd |Åtgärden för miniatyrdiagrammet.  Se [gemensamma inställningar för](#sparklines) mer information. |
| Namn och Värdesavgränsare |Enskild avgränsaren om du vill tolka textegenskapen i flera värden.  Se [gemensamma inställningar för](#name-value-separator) mer information. |
| Navigering fråga |Frågan ska köras när användaren väljer ett objekt i listan.  Se [gemensamma inställningar för](#navigation-query) mer information. |
| **Lista** |**> Kolumnrubriker** |
| Namn |Text som visas överst i den första kolumnen i listan. |
| Värde |Text som visas överst i den andra kolumnen i listan. |
| **Lista** |**> Tröskelvärden** |
| Aktivera tröskelvärden |Välj det här alternativet om du vill aktivera tröskelvärden.  Se [gemensamma inställningar för](#thresholds) mer information. |

## <a name="stack-of-line-charts-part"></a>Stacken för rad diagram del
Visar tre separata linjediagram med flera serier från en fråga logg över tid.

![Stack för linjediagram](media/log-analytics-view-designer/view-stack-line-charts.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Gruppnamn |Text som visas överst i panelen. |
| Ny grupp |Välj det här alternativet om du vill skapa en ny grupp i vyn från den aktuella vyn. |
| Ikon |Bild som ska visas bredvid resultatet i huvudet. |
| **Diagram 1<br>diagram 2<br>diagram 3** |**> Sidhuvud** |
| Rubrik |Text som visas överst i diagrammet. |
| Underrubrik |Text som visas under rubriken överst i diagrammet. |
| **Diagram 1<br>diagram 2<br>diagram 3** |**Linjediagram** |
| Fråga |Frågan ska köras för linjediagrammet.  Den första egenskapen ska vara ett textvärde och den andra egenskapen ett numeriskt värde.  Detta är vanligtvis en fråga som använder den **mått** nyckelord för att sammanfatta resultat.  Om frågan använder den **intervall** nyckelordet sedan x-axeln i diagrammet används för detta tidsintervall.  Om frågan inte innehåller den **intervall** nyckelord och varje timme sedan intervall används för x-axeln. |
| **Diagrammet** |**> Y-axeln** |
| Använd en logaritmisk skala |Välj det här alternativet om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som returneras av frågan.  Den här informationen används för att visa etiketter i diagram som visar värdetyperna och eventuellt för konvertering av värdena.  Enhetstypen anger kategorin för enheten och definierar de typ av aktuella värden som är tillgängliga.  Om du väljer ett värde i konvertera till och sedan de numeriska värdena konverteras från den aktuella enhetstypen ska konverteras till typen. |
| Anpassad etikett |Text som ska visas för Y-axeln bredvid etiketten för enhetstypen.  Om ingen etikett anges visas endast enhetstyp. |

## <a name="common-settings"></a>Vanliga inställningar
I följande avsnitt beskrivs de inställningar som är gemensamma för flera olika visualiseringen delar.

### <a name="name-value-separator">Namn och Värdesavgränsare</a>
Enskild avgränsaren om du vill att parsa textegenskapen från en fråga till flera värden.  Om du anger en avgränsare, kan du ange namn för varje fält avgränsade med samma avgränsare i rutan namn.

Anta till exempel att en egenskap som kallas *plats* som ingår värden såsom *Redmond byggnad 41* och *Bellevue Building12*.  Du kan ange – för namn och Värdesavgränsare och *Stad Byggnad* för namnet.  Detta skulle parsas varje värde i två egenskaper kallas *Stad* och *byggnad*.

### <a name="navigation-query">Navigering fråga</a>
Frågan ska köras när användaren väljer ett objekt i listan.  Använd *{valda objektet}* Infoga syntaxen för objekt som användaren har valt.

Om exempelvis frågan har en kolumn med namnet *datorn* och navigering frågan *{valda objektet}*, en fråga som *datorn = ”den här datorn”* skulle köras när användaren välja en dator.  Om frågan navigering är *typ = händelsen {valda objektet}* sedan frågan *typ = händelse datorn = ”den här datorn”* skulle köras.

### <a name="sparklines">Miniatyrdiagram</a>
Ett miniatyrdiagram är ett litet linjediagram som illustrerar värdet för en lista över tid.  Du kan välja om du vill visa ett vågrätt streck som anger relativa värdet för en numerisk kolumn eller ett miniatyrdiagram som anger dess värde över tid för visualisering delar med en lista.

I följande tabell beskrivs inställningarna för miniatyrdiagram.

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera miniatyrdiagram |Välj det här alternativet om du vill visa miniatyrdiagram i stället för vågräta fältet. |
| Åtgärd |Om miniatyrdiagram är aktiverade är igen att utföra på varje egenskap i listan för att beräkna värdena för miniatyrdiagrammet.<br><br>-Sista exemplet: Det sista värdet för seriens över ett tidsintervall.<br>-Max: Maximalt värde för seriens över ett tidsintervall.<br>-Min: Minsta värde för seriens över ett tidsintervall.<br>-Summan: Summan av värdena för serien över ett tidsintervall.<br>-Sammanfattning: Använder samma kommando som mått som frågan i huvudet. |

### <a name="thresholds">Tröskelvärden</a>
Tröskelvärden kan du visa en färgade ikonen bredvid varje objekt i en lista som ger dig en snabb visual indikator för objekt som överskrider ett visst värde eller faller inom ett visst intervall.  Du kan till exempel visa en grön ikon för artiklar med ett giltigt värde, gult om värdet är inom ett intervall som indikerar en varning och rött om det överstiger ett felaktigt värde.

När du aktiverar tröskelvärden för en del, måste du ange en eller flera tröskelvärden.  Om värdet för ett objekt som är större än ett tröskelvärde och lägre än tröskelvärdet för nästa, används den färgen.  Om objektet är större än tröskelvärdet för sedan högsta har den färgen angetts.   

Varje uppsättning tröskelvärde har ett tröskelvärde med värdet **standard**.  Det här är den färg som anger om inga andra värden har överskridits.  Du kan lägga till eller ta bort tröskelvärden genom att klicka på den  **+**  eller **x** knappen.

I följande tabell beskrivs inställningarna för gränstal.

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera tröskelvärden |Välj det här alternativet om du vill visa en färg visas till vänster om varje värde som anger dess hälsa i förhållande till angivna tröskelvärden. |
| Namn |Namn för att identifiera tröskelvärdet. |
| Tröskelvärde |Värdet för tröskelvärdet.  Färg för hälsa för varje listobjekt har angetts till färgen för den högsta tröskelvärde överskridits av objektets värde.  Det finns en standard-tröskelvärde som är färgen om inget tröskelvärden överskrids. |
| Färg |Färg för tröskelvärdet. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logga sökningar](log-analytics-log-searches.md) att stödja frågorna i visualiseringen delar.
