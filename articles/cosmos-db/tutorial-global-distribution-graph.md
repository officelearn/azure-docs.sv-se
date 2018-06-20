---
title: Självstudie för global distribution i Azure Cosmos DB för Graph API | Microsoft Docs
description: Lär dig att konfigurera global distribution i Azure Cosmos DB med Graph API.
services: cosmos-db
keywords: global distribution, graph, gremlin
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 61004a735f731cfd1303cf40b6e60cba496e942a
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763688"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Konfigurera global distribution i Azure Cosmos DB med Graph API

I den här artikeln visar vi hur du kan konfigurera global distribution i Azure Cosmos DB med Azure Portal och sedan ansluta med hjälp av Graph API.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [Graph API](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Ansluta till en önskad region med hjälp av Graph API och .NET SDK

Graph API exponeras som ett tilläggsbibliotek ovanpå SQL API.

I syfte att dra nytta av den [globala distributionen](distribute-data-globally.md) kan klientprogrammen ange den beställda listan med inställningar för regioner som ska användas för att utföra dokumentåtgärder. Detta gör du genom att konfigurera anslutningspolicyn. Utifrån Azure Cosmos DB-kontokonfigurationen, den aktuella regionala tillgängligheten och den angivna listan med inställningar, väljs den mest optimala slutpunkten för skriv- och läsåtgärder av SDK:n.

Denna lista med inställningar anges när en anslutning initieras med SDK:erna. SDK:erna accepterar den valfria parametern PreferredLocations, som är en sorterad lista över Azure-regioner.

* **Skriver**: SDK:n skickar automatiskt alla skrivningar till den aktuella skrivregionen.
* **Läser**: Alla läsningar skickas till den första tillgängliga regionen i PreferredLocations-listan. Om begäran misslyckas fortsätter klienten nedåt i listan till nästa region osv. SDK:erna försöker endast läsa från de regioner som anges i PreferredLocations. Det innebär att om Cosmos DB-kontot t.ex. är tillgängligt i tre regioner men klienten enbart anger två av de skrivskyddade regionerna för PreferredLocations, så hanteras inga läsningar från skrivregionen även om en redundans sker.

Programmet kan verifiera den aktuella skrivslutpunkt och lässlutpunkt som valts av SDK:n genom att kontrollera de två egenskaperna WriteEndpoint och ReadEndpoint, som är tillgängliga i SDK-version 1.8 och senare. Om egenskapen PreferredLocations inte har angetts hanteras alla förfrågningar från den aktuella skrivregionen.

### <a name="using-the-sdk"></a>Med SDK

I .NET SDK har exempelvis parametern `ConnectionPolicy` för konstruktorn `DocumentClient` en egenskap som kallas `PreferredLocations`. Den här egenskapen kan anges som en lista med regionnamn. Visningsnamnen för [Azure-regioner][regions] kan anges som en del av `PreferredLocations`.

> [!NOTE]
> Slutpunkternas URL:er ska inte betraktas som långlivade konstanter. Tjänsten kan uppdatera dem när som helst. SDK:n hanterar sådana ändringar automatiskt.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

Och med detta är den här självstudiekursen klar. Mer information om hur du kan hantera ditt globalt replikerade kontos konsekvens finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Mer information om hur global databasreplikering fungerar i Azure Cosmos DB finns i [Distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med SQL-API:erna

Du kan nu fortsätta till nästa självstudiekurs och lära dig hur du utvecklar lokalt med hjälp av den lokala Azure Cosmos DB-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med emulatorn](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

