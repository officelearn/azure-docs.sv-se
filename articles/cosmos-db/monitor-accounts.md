---
title: Övervaka Azure Cosmos DB begäranden och storage | Microsoft Docs
description: Lär dig hur du övervakar Azure Cosmos DB kontot för prestandavärden, till exempel begäranden och fel, och användningsstatistik, till exempel användningen av lagringsutrymme.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: sngun
ms.openlocfilehash: 1e5dba722f240bd6535ba64494912a153e9224c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34612647"
---
# <a name="monitor-azure-cosmos-db"></a>Övervaka Azure Cosmos DB
Du kan övervaka dina Azure DB som Cosmos-konton i den [Azure-portalen](https://portal.azure.com/). För varje Azure DB som Cosmos-konto är en fullständig uppsättning mått som kan övervakas dataflöde, lagring, tillgänglighet, svarstid och konsekvens.

Mått kan granskas på sidan konto sidan Ny mått eller i Azure-Monitor.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Visa prestandamått på sidan mått
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **alla tjänster**, bläddra till **databaser**, klickar du på **Azure Cosmos DB**, och klicka sedan på namnet på Azure Cosmos DB-konto som du vill visa prestandamått.
2. När den nya sidan läses in i menyn resurs under **övervakning**, klickar du på **mått**.
3. När mätvärdena som öppnar, välja en samling ska granska från den **samling(ar)** listrutan.

   Azure-portalen visar uppsättning tillgängliga mått för samlingen. Observera att dataflöde, lagring, tillgänglighet, svarstid och konsekvenskontroll mått som tillhandahålls på separata flikar. Att hämta ytterligare information på mätvärdena, klicka på dubbelpil längst upp till höger i fönstret varje mått.

   ![Skärmbild av övervakning linsen som visar mått suite](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Visa prestandamått med hjälp av Azure-övervakning
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **övervakaren** fältet till vänster.
2. Resurs-menyn klickar du på **mått**.
3. I den **Monitor - mått** fönster i den **resursgruppen** listrutan väljer du resursgruppen som är associerade med Azure DB som Cosmos-konto som du vill övervaka. 
4. I den **resurs** nedrullningsbara menyn och väljer databasen konto för att övervaka.
5. I listan över **tillgängliga mått**, välja mått att visa. Använd knappen CTRL du kan markera. 

## <a name="view-performance-metrics-on-the-account-page"></a>Visa prestandamått på kontosidan
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **alla tjänster**, bläddra till **databaser**, klickar du på **Azure Cosmos DB**, och klicka sedan på namnet på Azure Cosmos DB-konto som du vill visa prestandamått.
2. Den **övervakning** lins visar följande rubriker som standard:
   
   * Totalt antal begäranden för den aktuella dagen.
   * Lagringsutrymme som används.
   
   ![Skärmbild av övervakning linsen som visar begäranden och lagringsanvändningen](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Klicka på dubbelpil upp till höger i den **begäranden** panelen öppnar en detaljerad **mått** sidan.
4. Den **mått** visar information om förfrågningarna. 

## <a name="set-up-alerts-in-the-portal"></a>Ställa in aviseringar på portalen
1. I den [Azure-portalen](https://portal.azure.com/), klickar du på **alla tjänster**, klickar du på **Azure Cosmos DB**, och klicka sedan på namnet på Azure DB som Cosmos-konto som du vill ställa in prestanda mått aviseringar.
2. Resurs-menyn klickar du på **Varningsregler** att öppna sidan Varningsregler.  
   ![Skärmbild som visar aviseringen regler del markerad](./media/monitor-accounts/madocdb10.5.png)
3. I den **Varna regler** klickar du på **Lägg till avisering**.  
   ![Skärmbild av sidan Varningsregler med knappen Lägg till avisering markerad](./media/monitor-accounts/madocdb11.png)
4. I den **lägga till en varningsregel** anger:
   
   * Namnet på regeln som du ställer in.
   * En beskrivning av den nya regeln.
   * Mått för regeln.
   * Villkor, tröskelvärde och period som bestämmer när aviseringen aktiveras. Till exempel ett serverfel större antal än 5 under de senaste 15 minuterna.
   * Om tjänstadministratören och coadministrators via e-post när aviseringen utlöses.
   * Ytterligare e-postadresser för aviseringar.  
     ![Skärmbild av guiden Lägg till en varningsregel-sida](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Övervaka Azure Cosmos DB programmässigt
De konto nivån tillgängliga mått i portalen, till exempel kontot lagring användnings- och Totalt antal begäranden är inte tillgängliga via SQL-API: er. Du kan dock hämta data om programvaruanvändning på samlingsnivå med hjälp av SQL-API: er. Om du vill hämta data om nivån, gör du följande:

* Använda REST-API för [utföra GET på samlingen](https://msdn.microsoft.com/library/mt489073.aspx). Informationen om kvot- och användningsdata för samlingen returneras i x-ms-resurs-quota- och x-ms--Resursanvändning i svaret.
* Använd .NET SDK i [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) metod som returnerar en [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) som innehåller ett antal egenskaper som  **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**, med mera.

Om du vill komma åt ytterligare mått kan använda den [SDK för Azure-Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Tillgängliga mått definitioner kan hämtas genom att anropa:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Frågor för att hämta enskilda mått använder du följande format:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Mer information finns i [hämtar resurs mätvärden via REST API för Azure-Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Observera att ”Azure Insights” bytte ”Azure-Monitor”.  Det här blogginlägget refererar till äldre namn.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Cosmos DB kapacitetsplanering i den [Azure Cosmos DB kapacitet planner Kalkylatorn](https://www.documentdb.com/capacityplanner).

