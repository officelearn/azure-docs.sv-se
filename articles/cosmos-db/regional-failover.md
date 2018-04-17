---
title: Regional växling vid fel i Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur manuell och automatisk redundans fungerar med Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977027de0627a6eca5f9eb5d1ab83dea5347c6d4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Automatisk regional växling vid fel för kontinuitet i Azure Cosmos DB
Azure Cosmos-DB förenklar globala fördelning av data genom att erbjuda fullständigt hanterade [flera regioner databasen konton](distribute-data-globally.md) som ger tydliga kompromisser mellan konsekvens, tillgänglighet och prestanda, alla med motsvarande garanterar. Cosmos DB konton ger hög tillgänglighet, siffra ms latens [väldefinierade konsekvensnivåer](consistency-levels.md), transparent regional växling vid fel med flera API: er och möjligheten att skala Elastiskt genomflöde och lagring på globalt. 

Cosmos DB stöder både explicit och principen drivs växling vid fel som gör det möjligt att styra systembeteendet för slutpunkt till slutpunkt-vid fel. I den här artikeln titta på:

* Hur fungerar manuell växling vid fel i Cosmos-databasen?
* Hur automatisk redundans arbete i Cosmos-DB och vad som händer när data center går ned?
* Hur kan du använda manuell växling vid fel i programarkitekturer?

Du kan också information om regional växling vid fel i den här videon av Azure Cosmos DB Programhanteraren Andrew Liu, som visar funktionerna global distributionsplatsen inklusive regional växling vid fel.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Konfigurera flera regioner program
Innan vi fördjupa dig i redundans lägen titta vi på hur du kan konfigurera ett program kan dra nytta av flera regional tillgänglighet och bli motståndskraftiga i händelse av regional växling vid fel.

* Först distribuera ditt program i flera områden
* Kontrollera låg latens åtkomst från varje region som ditt program är distribuerat och konfigurera motsvarande [över önskade regioner](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) för varje region via en av de SDK: er som stöds.

Följande utdrag visar hur du initierar ett program i flera regioner. Här kan Azure DB som Cosmos-kontonamnet `contoso.documents.azure.com` har konfigurerats med två områden - västra USA och Norra Europa. 

* Programmet har distribuerats i USA, västra region (med Azure App Service till exempel) 
* Konfigurerad med `West US` som första önskad region för låg fördröjning läser
* Konfigurerad med `North Europe` som andra önskad region (för hög tillgänglighet under regionala fel)

Den här konfigurationen ser ut följande kodavsnitt i SQL-API:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

Programmet distribueras också i Norra Europa region prioriterade regioner omvänd ordning. Det vill säga har Nordeuropa region angetts först för låg latens läsningar. Sedan har regionen västra USA angetts som andra önskad region för hög tillgänglighet under regionala fel.

Följande Arkitekturdiagram visar en distribution av flera regioner där Cosmos-DB och programmet har konfigurerats ska vara tillgängliga i fyra Azure geografiska områden.  

![Globalt distribuerade programdistribution med Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Nu ska vi titta på hur tjänsten Cosmos DB hanterar regionala fel via automatisk redundans. 

## <a id="AutomaticFailovers"></a>Automatisk redundans
Om en Azure regionalt strömavbrott eller data center avbrott utlöser Cosmos DB automatiskt redundans av alla Cosmos-DB-konton med i det aktuella området. 

**Vad händer om en skrivskyddad region har ett avbrott?**

Cosmos DB konton med en skrivskyddad region i någon av de berörda regionerna frånkopplad från deras skrivåtgärder region och markerats offline automatiskt. SDK: er för Cosmos DB implementera ett regionalt discovery-protokollet som gör att de automatiskt ska identifiera när en region är tillgänglig och omdirigering läsa anrop till nästa tillgängliga område i listan med önskad region. Om ingen av regionerna i listan över önskad region finns återgår samtal automatiskt till att det aktuella området för skrivning. Inga ändringar krävs i din programkod för att hantera regional växling vid fel. Under hela processen, konsekvens garanterar även fortsättningsvis användas av Cosmos DB.

![Läs region fel i Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

När den berörda regionen återställer från avbrottet, återställs alla berörda Cosmos-DB-konton i regionen automatiskt av tjänsten. Cosmos DB-konton som hade en skrivskyddad region i den berörda regionen och sedan automatiskt synkroniseras med den aktuella skrivåtgärder region och aktivera online. DB-SDK Cosmos identifiera tillgängligheten för den nya regionen och utvärdera om regionen måste väljas som den aktuella skrivskyddade region baserat på önskad regionlistan konfigurerade av programmet. Efterföljande läsningar omdirigeras till den återställda regionen utan ändringar av programkoden.

**Vad händer om en skrivning region har ett avbrott?**

Om den berörda regionen är det aktuella området för skrivning och automatisk redundans är aktiverat för Azure DB som Cosmos-konto, är regionen automatiskt markeras som offline. Sedan höjs en annan region som write-region för kontot berörda Azure Cosmos DB. Du kan aktivera automatisk redundans och helt styra den region Markeringsordningen för dina Azure Cosmos DB konton via Azure portal eller [programmässigt](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Redundans prioriteringar för Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Under automatisk redundans väljer Azure Cosmos DB automatiskt nästa skriva region för en viss Azure Cosmos DB konto baserat på angivna prioritetsordningen. Program kan använda egenskapen WriteEndpoint DocumentClient klassen för att identifiera ändringen i skrivning region.

![Skriva fel region i Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

När den berörda regionen återställer från avbrottet, återställs alla berörda Cosmos-DB-konton i regionen automatiskt av tjänsten. 

* Data som finns i den föregående write-region som inte har replikerats om du vill läsa regioner under avbrottet publiceras som en konflikt feed. Program kan läsa konflikt feeden lös konflikterna baserat på specifika programlogiken och skriva den uppdaterade informationen tillbaka till Azure Cosmos DB kontot efter behov. 
* Den tidigare skrivåtgärder regionen skapas som en skrivskyddad region och online igen automatiskt. 
* Du kan konfigurera om skrivskyddade region som var online igen automatiskt som skrivning region genom att utföra en manuell växling via Azure portal eller [programmässigt](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

Följande kodavsnitt visar hur du kan bearbeta konflikter när den berörda regionen återställer från avbrottet.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>Manuell växling vid fel

Förutom automatisk redundans kan det aktuella området skrivning av en viss Cosmos-DB-konto manuellt ändras dynamiskt till någon av de befintliga skrivskyddade regionerna. Manuell redundans kan inledas via Azure portal eller [programmässigt](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Kontrollera manuell redundans **noll dataförlust** och **noll tillgänglighet** förlust och att skrivåtgärder Överföringsstatus från gammalt skriva region till en ny för det angivna Cosmos-DB-kontot. Som i automatisk redundans Cosmos DB SDK automatiskt hanterar skrivning region ändringar under manuell redundans och säkerställer att omdirigeras automatiskt anrop till den nya write-regionen. Ingen kod eller ändringar av konfigurationen som krävs i ditt program för att hantera redundans. 

![Manuell växling vid fel i Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Här är några vanliga scenarier där manuell växling kan vara användbara:

**Följ klockan modellen**: om dina program har förutsägbar trafikmönster baserat på tid på dagen, du regelbundet kan ändra status för skrivning till den mest aktiva geografiska region som baseras på tidpunkt på dagen.

**Tjänstuppdatering**: vissa globalt distribuerade programdistribution kan omfatta omdirigering trafik till annan region via traffic manager under deras planerade tjänstuppdatering. Sådana programdistribution nu kan använda manuell växling att skrivstatus för regionen där det ska vara aktiv trafik under servicefönster för uppdateringen.

**Kontinuitet för företag och Disaster Recovery BCDR- och hög tillgänglighet och Disaster Recovery (HADR) flyttar**: de flesta företagsprogram inkluderar business continuity tester som en del av deras utveckling och versionen. BCDR och HADR testning är ofta ett viktigt steg i efterlevnad certifieringar och garanterande tjänsttillgänglighet för regional avbrott. Du kan testa dina program som använder Cosmos-databas för lagring av utlösa en manuell växling för Cosmos-DB-konto och/eller att lägga till och ta bort en region dynamiskt BCDR beredskap.

Den här artikeln har granskat hur manuell och automatisk redundans arbete i Cosmos-databasen och hur du kan konfigurera Cosmos DB konton och programmen ska vara globalt tillgängliga. Genom att använda Cosmos DB globala replication stöd kan du förbättra svarstiden för slutpunkt till slutpunkt och så att de är hög tillgänglighet även i händelse av fel region. 

## <a id="NextSteps"></a>Nästa steg
* Lär dig mer om hur Cosmos DB stöder [global distributionsplatsen](distribute-data-globally.md)
* Lär dig mer om [global konsekvens med Azure Cosmos DB](consistency-levels.md)
* Utveckla med flera områden med Azure Cosmos DB [SQL API](tutorial-global-distribution-sql-api.md)
* Lär dig hur du skapar [flera regioner writer arkitekturer](multi-region-writers.md) med Azure Cosmos DB

