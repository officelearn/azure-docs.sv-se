---
title: Hur du lägger till en referensdatauppsättning i miljön för Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en referensdatauppsättning för att utöka data i Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.custom: seodec18
ms.openlocfilehash: 17ba15ea12efc80dcc830f6338a837d1abb77a71
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764438"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en referensdatauppsättning för miljön för Time Series Insights med hjälp av Azure portal

Den här artikeln beskriver hur du lägger till en referensdatauppsättning i din Azure Time Series Insights-miljö. Referensdata är användbar för att ansluta till dina data om du vill utöka värdena.

En Referensdatauppsättning är en samling objekt som kan förbättra händelser från din händelsekälla. Tid tidsserieinsikter kopplar varje händelse från din händelsekälla till motsvarande dataraden i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primärnyckel kolumnerna som definierats i referensdatauppsättningen.

Referensdata är inte ansluten retroaktivt. Det innebär att endast nuvarande och framtida inkommande data är matchade och ansluten till referens datum har angetts, när den har konfigurerats och laddat upp.

## <a name="video"></a>Video: 

### <a name="in-this-video-we-cover-time-series-insights-reference-data-modelbr"></a>I den här videon beskriver vi Time Series Insight referensmodell för data.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Lägg till en referensdatauppsättning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga Time Series Insights-miljö. Klicka på **alla resurser** på menyn på vänster sida av Azure-portalen. Välj Time Series Insights-miljö.

3. Välj den **översikt** sidan. Leta upp den **Webbadressen för Time Series Insights explorer** och öppna länken.  

   Visa explorer för din TSI-miljö.

4. Expandera miljöväljaren i TSI-Utforskaren. Välj den aktiva miljön. Välj referensikonen för data i det övre högra hörnet på sidan explorer.

   ![Lägg till referensdata](media/add-reference-data-set/add_reference_data.png)

5. Välj den **+ Lägg till en uppsättning** knappen för att börja lägga till en ny datauppsättning.

   ![Lägg till uppsättning av data](media/add-reference-data-set/add_data_set.png)

6. På den **New-referensdatauppsättning** väljer formatet för data: 
   - Välj **CSV** för kommaavgränsad data. Den första raden behandlas som en rubrikrad. 
   - Välj **JSON-matris** för javascript object notation (JSON) formaterade data.

   ![Välja dataformat.](media/add-reference-data-set/add_data.png)

7. Ange dina data, med en av två metoder:
   - Klistra in data i textredigeraren. Välj **parsa referensdata** knappen.
   - Välj **Välj fil** för att lägga till data från en lokal textfil. 

   Till exempel klistra in CSV-data: ![Inklistrade CSV-data](media/add-reference-data-set/csv_data_pasted.png)

   Till exempel klistra in JSON-matris-data: ![Klistra in JSON-data](media/add-reference-data-set/json_data_pasted.png)

   Om det finns ett fel vid parsning av datavärdena, felet visas i rött längst ned på sidan, till exempel `CSV parsing error, no rows extracted`.

8. När data har parsas, visas ett datarutnät Visa kolumner och rader som representerar data.  Granska datarutnätet för att säkerställa är korrekt.

   ![Lägg till referensdata](media/add-reference-data-set/parse_data.png)

9. Granska varje kolumn om du vill se den datatyp som antas och ändra om det behövs.  Välj symbolen för typ av data i kolumnrubriken: **#** för double-värde (numeriska data), **T | F** för boolesk eller **Abc** sträng.

   ![Välj datatyper på kolumnrubrikerna.](media/add-reference-data-set/choose_datatypes.png)

10. Byt namn på kolumnrubrikerna om det behövs. Nyckelkolumnsnamn krävs för att ansluta till motsvarande egenskap i din händelsekälla. Se till att referens data nyckelkolumnnamnen matchar exakt med händelsenamnet till dina inkommande data, inklusive skiftlägeskänslighet. De icke-nyckelkolumn namn används för att utöka den inkommande data med motsvarande referens datavärdena.

11. Klicka på **lägger till en rad** eller **lägga till en kolumn** att lägga till flera referens datavärden efter behov.

12. Skriv ett värde i den **Filtrera rader...**  fält för att granska specifika rader efter behov. Filtret är användbart för att granska data, men tillämpas inte när du överför data.
 
13. Namnge datamängd, genom att fylla i den **datauppsättningsnamnet** fältet ovanför rutnätet.

    ![Namn på datauppsättning.](media/add-reference-data-set/name_reference_dataset.png)

14. Ange den **primärnyckel** kolumn i datauppsättningen, genom att välja listrutan ovan datarutnätet.

    ![Markera kolumnerna som är viktiga.](media/add-reference-data-set/set_primary_key.png)

    Alternativt kan du välja den **+** för att lägga till en sekundär nyckelkolumn som en sammansatt primärnyckel. Om du vill ångra valet kan du välja tomt värde från listrutan att ta bort den sekundära nyckeln.

15. Om du vill överföra data, Välj den **överför rader** knappen.

    ![Ladda upp](media/add-reference-data-set/upload_rows.png)

    Sidan bekräftar den slutförda ladda upp och visa meddelandet **har laddats upp datauppsättningen**.

## <a name="next-steps"></a>Nästa steg
* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.
* En fullständig API-referens, se dokumentet [Referensdata-API](/rest/api/time-series-insights/ga-reference-data-api) .
