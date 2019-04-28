---
title: Övervaka prestanda- och mått i Azure Cosmos DB
description: Lär dig hur du övervakar Azure Cosmos DB-kontot för prestandavärden, till exempel begäranden och fel, och användningsstatistik, till exempel lagringsanvändningen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a12e0f567747b67c67f7bc825df1079d852e2f16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330997"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Övervaka prestanda- och mått i Azure Cosmos DB

Du kan övervaka dina Azure Cosmos DB-konton i den [Azure-portalen](https://portal.azure.com/). För varje Azure Cosmos DB-konto finns en komplett uppsättning mått för att övervaka dataflöde, lagring, tillgänglighet, svarstid och konsekvens.

Mått kan granskas på sidan för den nya mått-sidan, eller i Azure Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Visa prestandamått på sidan mått
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **alla tjänster**, bläddrar du till **databaser**, klickar du på **Azure Cosmos DB**, och klicka sedan på namnet på Azure Cosmos DB-konto som du vill visa prestandamått.
2. När den nya sidan har lästs in i resurs-menyn under **övervakning**, klickar du på **mått**.
3. När mått som sidan öppnas, Välj den samling att granska från den **samling(ar)** listrutan.

   Azure-portalen visar uppsättning tillgängliga mått för samlingen. Observera att måtten för dataflöde, lagring, tillgänglighet, svarstid och konsekvens tillhandahålls på en separat flik. Att hämta ytterligare information om måtten tillhandahålls, klicka på dubbelpilen längst upp till höger i fönstret för varje mått.

   ![Skärmbild av övervakning instrumentpanel som visar programsviten mått](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Visa prestandamått med hjälp av Azure-övervakning
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **övervakaren** i det vänstra fältet.
2. I resurs-menyn klickar du på **mått**.
3. I den **övervaka – mått** fönstret i den **resursgrupp** nedrullningsbara menyn och välj resursgruppen som är associerade med Azure Cosmos DB-konto som du vill övervaka. 
4. I den **Resource** nedrullningsbara menyn och välj databasen konto för att övervaka.
5. I listan över **tillgängliga mått**, välja mått att visa. Använd CTRL-tangenten för att välja flera. 

## <a name="view-performance-metrics-on-the-account-page"></a>Visa prestandamått på sidan
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **alla tjänster**, bläddrar du till **databaser**, klickar du på **Azure Cosmos DB**, och klicka sedan på namnet på Azure Cosmos DB-konto som du vill visa prestandamått.
2. Den **övervakning** lins visar följande rubriker som standard:
   
   * Totalt antal begäranden för den aktuella dagen.
   * Lagring som används.
   
   ![Skärmbild av övervakning instrumentpanel som visar begäranden och lagringsanvändningen](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. När du klickar på dubbelpilen längst upp till höger i den **begäranden** panelen öppnas en detaljerad **mått** sidan.
4. Den **mått** sidan visar information om förfrågningarna. 

## <a name="set-up-alerts-in-the-portal"></a>Konfigurera aviseringar i portalen
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **alla tjänster**, klickar du på **Azure Cosmos DB**, och klicka sedan på namnet på det Azure Cosmos DB-konto som du vill konfigurera prestanda måttaviseringar.
2. I resurs-menyn klickar du på **Varningsregler** att öppna sidan Varningsregler.  
   ![Skärmbild av Varningsregler del valda](./media/monitor-accounts/madocdb10.5.png)
3. I den **Aviseringsregler** klickar du på **Lägg till avisering**.  
   ![Skärmbild av sidan Varningsregler med knappen Lägg till avisering markerad](./media/monitor-accounts/madocdb11.png)
4. I den **Lägg till en varningsregel** anger du:
   
   * Namnet på regeln som du ställer in.
   * En beskrivning av den nya aviseringsregeln.
   * Mått för regeln.
   * Villkor, tröskelvärde och period som bestämmer när aviseringen aktiveras. Ett serverfel räknas exempelvis är större än 5 under de senaste 15 minuterna.
   * Om tjänstadministratören och medadministratörer via e-post när aviseringen utlöses.
   * Ytterligare e-postadresser för varningsmeddelanden.  
     ![Skärmbild av Lägg till en varningsregel-sida](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Övervaka Azure Cosmos DB programmässigt
De konto statistik tillgänglig i portalen, till exempel konto storage-användning och Totalt antal begäranden, är inte tillgängliga via SQL-API: er. Du kan dock hämta användningsdata på samlingsnivå med hjälp av SQL-API: er. Om du vill hämta data för samlingen på, gör du följande:

* Du använder REST-API [utför en hämtning på samlingen](https://msdn.microsoft.com/library/mt489073.aspx). Information om kvot och användning för samlingen returneras i x-ms-resource-quota- och x-ms--Resursanvändning i svaret.
* Om du vill använda .NET SDK använder den [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metod som returnerar en [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) som innehåller ett antal egenskaper som  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**, med mera.

För att komma åt ytterligare mått, använda den [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Tillgängliga definitioner av mått kan hämtas genom att anropa:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Frågor att hämta enskilda mått använder du följande format:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Mer information finns i [hämtning av mätvärden för resurs via REST-API i Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Observera att ”Azure Insights” har bytt namn ”Azure Monitor”.  Det här blogginlägget refererar till äldre namnet.

## <a name="next-steps"></a>Nästa steg
Läs mer om kapacitetsplanering för Azure Cosmos DB i den [Azure Cosmos DB kapacitet planner Kalkylatorn](https://www.documentdb.com/capacityplanner).

