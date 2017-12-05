---
title: "Azure DB Cosmos global distributionsplatsen självstudier för Graph API | Microsoft Docs"
description: "Lär dig hur du ställer in Azure Cosmos DB global distributionsplatsen med hjälp av Graph API."
services: cosmos-db
keywords: Global distributionsplatsen, diagram, gremlin
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.openlocfilehash: eb55bdee60400b4b14f47a6a0b1d0682b267d26f
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Hur du konfigurerar Azure Cosmos DB global distributionsplatsen med hjälp av Graph-API

I den här artikeln visar vi hur du använder Azure portal för att installationen Azure Cosmos DB global distributionsplatsen och ansluter sedan med hjälp av Graph-API (förhandsversion).

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Konfigurera distributionslistor med Azure-portalen
> * Konfigurera distributionslistor med hjälp av den [Graph API: er](graph-introduction.md) (förhandsgranskning)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Ansluter till en önskad region med Graph-API med .NET SDK

Graph API exponeras som ett tillägg bibliotek ovanpå DocumentDB SDK.

För att kunna dra nytta av [global distributionsplatsen](distribute-data-globally.md), klientprogram kan ange beställda inställningar listan över regioner som används för att utföra åtgärder för dokumentet. Detta kan göras genom att ställa in principen. Baserat på konfigurationen av Azure DB som Cosmos-kontot, aktuella regional tillgänglighet och inställningar listan som anges, kommer den mest optimala slutpunkten väljas av SDK, för att utföra skrivåtgärder och läsåtgärder.

Den här inställningen listan anges när du initierar en anslutning med SDK: erna. SDK: erna acceptera en valfri parameter ”PreferredLocations” som en sorterad lista över Azure-regioner.

* **Skriver**: SDK skickar automatiskt alla skrivningar till aktuellt skriva region.
* **Läser**: alla Läs kommer att skickas till den första tillgängliga regionen i listan över PreferredLocations. Om begäran inte klienten misslyckas nedåt i listan till nästa region, och så vidare. SDK: erna görs endast försök att läsa från de regioner som anges i PreferredLocations. Så, till exempel om Cosmos-DB-konto finns i tre regioner, men klienten endast anger två av regionerna som icke-och skrivbehörighet för PreferredLocations, sedan utan läsning hanteras utanför området skrivåtgärder även vid redundans.

Programmet kan verifiera den aktuella write-slutpunkten och läsa slutpunkt som valts av SDK genom att kontrollera två egenskaper, WriteEndpoint och ReadEndpoint, finns i SDK-version 1.8 och senare. Om egenskapen PreferredLocations inte har angetts hanteras alla förfrågningar från det aktuella området för skrivning.

### <a name="using-the-sdk"></a>Med SDK

Till exempel i .NET-SDK på `ConnectionPolicy` parameter för den `DocumentClient` konstruktorn har en egenskap som kallas `PreferredLocations`. Den här egenskapen kan anges till en lista över regionnamn. Visningsnamnen för [Azure-regioner] [ regions] kan anges som en del av `PreferredLocations`.

> [!NOTE]
> URL: er för slutpunkterna ska inte betraktas som långlivade konstanter. Tjänsten kan uppdatera dem när som helst. SDK hanterar automatiskt den här ändringen.
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

Det är den som Slutför den här kursen. Du kan lära dig hur du hanterar konsekvensen för globalt replikerade kontot genom att läsa [konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Och för mer information om hur globala databasreplikering fungerar i Azure Cosmos DB, se [distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Konfigurera distributionslistor med Azure-portalen
> * Konfigurera globala distribution via DocumentDB APIs

Du kan nu fortsätta till nästa kurs att lära dig hur du utvecklar lokalt med hjälp av lokala Azure DB som Cosmos-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med emulatorn](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

