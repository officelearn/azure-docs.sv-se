---
title: Hur du lägger till en referens datauppsättning Azure tid serien Insights-miljö
description: Den här artikeln beskriver hur du lägger till en referens datauppsättning för att utöka data i Azure tid serien Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.openlocfilehash: 7da2393bb5114de20747581e366a8f416c9ff9a4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653645"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en referens för datauppsättning för tid serien insikter miljön med hjälp av Azure portal

Den här artikeln beskriver hur du lägger till en referens datauppsättning Azure tid serien Insights-miljö. Referensdata är användbar för att ansluta till datakällan till utöka värdena.

En referens datauppsättning är en samling objekt som utökar händelser från din händelsekälla. Tid serien insikter ingång motorn kopplar varje händelse från din händelsekällan med motsvarande dataraden i datauppsättningen referens. Den här förhöjda händelsen är sedan tillgängliga för frågor. Den här kopplingen baseras på primärnyckeln kolumnerna som definierats i datauppsättningen referens.

Referensdata är inte ansluten retroaktivt. Detta innebär att endast aktuella och framtida ingång data är matchade och ansluten till uppsättningen med referens datum när den har konfigurerats och har överförts.

## <a name="add-a-reference-data-set"></a>Lägg till en referens för datauppsättning

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Leta upp din befintliga miljö tid serien insikter. Klicka på **alla resurser** på menyn till vänster i Azure-portalen. Välj Time Series Insights-miljö.

3. Välj den **översikt** sidan. Leta upp den **tid serien insikter explorer URL** och öppna länken.  

   Visa explorer för TSD miljön.

4. Expandera miljö Väljaren i TSD explorer. Välj aktiv miljö. Välj ikonen referens data i det övre högra hörnet på sidan explorer.

   ![Lägg till referensdata](media/add-reference-data-set/add_reference_data.png)

5. Välj den **+ Lägg till en datamängd** om du vill börja lägga till en ny datauppsättning.

   ![Lägg till datamängd](media/add-reference-data-set/add_data_set.png)

6. På den **ny refererar till datauppsättningen** väljer du formatet för data: 
   - Välj **CSV** för kommaavgränsad data. Den första raden behandlas som en rubrikrad. 
   - Välj **JSON-matris** för javascript object notation (JSON) formaterade data.

   ![Välja dataformat.](media/add-reference-data-set/add_data.png)

7. Ange data på något av två sätt:
   - Klistra in data i textredigeraren. Markera **Parse referensdata** knappen.
   - Välj **Välj fil** för att lägga till data från en lokal textfil. 

   Till exempel klistra in data för CSV: ![CSV inklistrade data](media/add-reference-data-set/csv_data_pasted.png)

   Till exempel klistra in JSON-data för matris: ![klistra in JSON-data](media/add-reference-data-set/json_data_pasted.png)

   Om ett fel vid parsning av datavärdena felet visas i rött längst ned på sidan som `CSV parsing error, no rows extracted`.

8. När data har tolkas visas en datarutnätet Visa kolumner och rader som representerar data.  Granska rutnätet för att säkerställa är korrekt.

   ![Lägg till referensdata](media/add-reference-data-set/parse_data.png)

9. Granska varje kolumn för att se datatypen antas och ändra datatypen om det behövs.  Välj typsymbolen data i kolumnrubriken: **#** för double (numeriska data) **T | F** för ett booleskt värde, eller **Abc** sträng.

   ![Välj datatyper på kolumnrubrikerna.](media/add-reference-data-set/choose_datatypes.png)

10. Byt namn på kolumnrubrikerna om det behövs. Nyckelkolumnnamnet är nödvändiga för att ansluta till motsvarande egenskap i din händelsekälla. Se till att referens data nyckelkolumnnamnen matcha exakt med händelsenamnet till dina inkommande data, inklusive skiftlägeskänslighet. Icke-nyckelkolumn namn används för att utöka inkommande data med motsvarande referens datavärdena.

11. Klicka på **lägga till en rad** eller **lägga till en kolumn** att lägga till flera referens datavärden efter behov.

12. Ange ett värde i den **filtrera raderna...**  fält att granska specifika rader efter behov. Filtret är användbar för granskning av data, men har inte tillämpats när du överför data.
 
13. Namnge datamängd, genom att fylla i den **datauppsättningsnamnet** fältet ovanför rutnätet.

   ![Namnet datauppsättningen.](media/add-reference-data-set/name_reference_dataset.png)

14. Ange den **primärnyckel** kolumn i datauppsättningen genom att välja i listrutan ovan datarutnätet.

   ![Markera kolumnerna som nyckel.](media/add-reference-data-set/set_primary_key.png)

   Alternativt, Välj den **+** för att lägga till en sekundär nyckelkolumn som en sammansatt primär nyckel. Om du behöver återställa markeringen välja tomt värde från listrutan att ta bort den sekundära nyckeln.

15.  Om du vill överföra data, Välj den **överför rader** knappen.

   ![Ladda upp](media/add-reference-data-set/upload_rows.png)

   Sidan bekräftar den slutförda ladda upp och visa meddelandet **laddats dataset**.

## <a name="next-steps"></a>Nästa steg
* [Hantera referensdata](time-series-insights-manage-reference-data-csharp.md) programmässigt.
* En fullständig API-referens, se dokumentet [Referensdata-API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) .
