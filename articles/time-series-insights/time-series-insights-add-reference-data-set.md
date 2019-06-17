---
title: Hur du lägger till en referensdatauppsättning i miljön för Azure Time Series Insights | Microsoft Docs
description: Den här artikeln beskriver hur du lägger till en referensdatauppsättning för att utöka data i Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/13/2019
ms.custom: seodec18
ms.openlocfilehash: 62c3b35f80300a38213b089567c3f63a8a2ce8db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235056"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en referensdatauppsättning för miljön för Time Series Insights med hjälp av Azure portal

Den här artikeln beskriver hur du lägger till en referensdatauppsättning i din Azure Time Series Insights-miljö. Referensdata är användbar för att ansluta till dina data om du vill utöka värdena.

En Referensdatauppsättning är en samling objekt som kan förbättra händelser från din händelsekälla. Tid tidsserieinsikter kopplar varje händelse från din händelsekälla till motsvarande dataraden i referensdatauppsättningen. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primärnyckel kolumnerna som definierats i referensdatauppsättningen.

Referensdata är inte ansluten retroaktivt. Endast nuvarande och framtida inkommande data är därför matchade och ansluten till referens datum har angetts, när den har konfigurerats och laddat upp.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Läs mer om Time Series Insight referensmodell för data.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Lägg till en referensdatauppsättning

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Leta upp din befintliga Time Series Insights-miljö. Klicka på **alla resurser** på menyn på vänster sida av Azure-portalen. Välj Time Series Insights-miljö.

1. Välj den **översikt** sidan. Leta upp den **Webbadressen för Time Series Insights explorer** och öppna länken.  

   Visa explorer för din TSI-miljö.

1. Expandera miljöväljaren i TSI-Utforskaren. Välj den aktiva miljön. Välj referensikonen för data i det övre högra hörnet på sidan explorer.

   [![Lägg till referensdata](media/add-reference-data-set/add_reference_data.png)](media/add-reference-data-set/add_reference_data.png#lightbox)

1. Välj den **+ Lägg till en uppsättning** knappen för att börja lägga till en ny datauppsättning.

   [![Lägg till uppsättning av data](media/add-reference-data-set/add_data_set.png)](media/add-reference-data-set/add_data_set.png#lightbox)

1. På den **New-referensdatauppsättning** väljer formatet för data:
   - Välj **CSV** för kommaavgränsad data. Den första raden behandlas som en rubrikrad.
   - Välj **JSON-matris** för javascript object notation (JSON) formaterade data.

   [![Välja dataformat.](media/add-reference-data-set/add_data.png)](media/add-reference-data-set/add_data.png#lightbox)

1. Ange dina data, med en av två metoder:
   - Klistra in data i textredigeraren. Välj **parsa referensdata** knappen.
   - Välj **Välj fil** för att lägga till data från en lokal textfil.

   Till exempel klistra in CSV-data: [![Inklistrade CSV-data](media/add-reference-data-set/csv_data_pasted.png)](media/add-reference-data-set/csv_data_pasted.png#lightbox)

   Till exempel klistra in JSON-matris-data: [![Klistra in JSON-data](media/add-reference-data-set/json_data_pasted.png)](media/add-reference-data-set/json_data_pasted.png#lightbox)

   Om det finns ett fel vid parsning av datavärdena, felet visas i rött längst ned på sidan, till exempel `CSV parsing error, no rows extracted`.

1. När data har parsas, visas ett datarutnät Visa kolumner och rader som representerar data.  Granska datarutnätet för att säkerställa är korrekt.

   [![Lägg till referensdata](media/add-reference-data-set/parse_data.png)](media/add-reference-data-set/parse_data.png#lightbox)

1. Granska varje kolumn om du vill se den datatyp som antas och ändra om det behövs.  Välj symbolen för typ av data i kolumnrubriken: **#** för double-värde (numeriska data), **T | F** för boolesk eller **Abc** sträng.

   [![Välj datatyper på kolumnrubrikerna.](media/add-reference-data-set/choose_datatypes.png)](media/add-reference-data-set/choose_datatypes.png#lightbox)

1. Byt namn på kolumnrubrikerna om det behövs. Nyckelkolumnsnamn krävs för att ansluta till motsvarande egenskap i din händelsekälla. Se till att referens data nyckelkolumnnamnen matchar exakt med händelsenamnet till dina inkommande data, inklusive skiftlägeskänslighet. De icke-nyckelkolumn namn används för att utöka den inkommande data med motsvarande referens datavärdena.

1. Klicka på **lägger till en rad** eller **lägga till en kolumn** att lägga till flera referens datavärden efter behov.

1. Skriv ett värde i den **Filtrera rader...**  fält för att granska specifika rader efter behov. Filtret är användbart för att granska data, men tillämpas inte när du överför data.

1. Namnge datamängd, genom att fylla i den **datauppsättningsnamnet** fältet ovanför rutnätet.

    [![Namn på datauppsättning.](media/add-reference-data-set/name_reference_dataset.png)](media/add-reference-data-set/name_reference_dataset.png#lightbox)

1. Ange den **primärnyckel** kolumn i datauppsättningen, genom att välja listrutan ovan datarutnätet.

    [![Markera kolumnerna som är viktiga.](media/add-reference-data-set/set_primary_key.png)](media/add-reference-data-set/set_primary_key.png#lightbox)

    Alternativt kan du välja den **+** för att lägga till en sekundär nyckelkolumn som en sammansatt primärnyckel. Om du vill ångra valet kan du välja tomt värde från listrutan att ta bort den sekundära nyckeln.

1. Om du vill överföra data, Välj den **överför rader** knappen.

    [![Ladda upp](media/add-reference-data-set/upload_rows.png)](media/add-reference-data-set/upload_rows.png#lightbox)

    Sidan bekräftar den slutförda ladda upp och visa meddelandet **har laddats upp datauppsättningen**.

## <a name="next-steps"></a>Nästa steg

* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.

* En fullständig API-referens, se dokumentet [Referensdata-API](/rest/api/time-series-insights/ga-reference-data-api) .
