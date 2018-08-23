---
title: Regional redundans i Azure Cosmos DB | Microsoft Docs
description: Läs mer om hur manuell och automatisk redundans fungerar med Azure Cosmos DB.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: govindk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 697be3a1eb07b2f2650f3dd94fd835b9431aec6b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055567"
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Automatisk regional redundans för verksamhetskontinuitet i Azure Cosmos DB
Azure Cosmos DB förenklar den globala distributionen av data genom att erbjuda fullständigt hanterad, [databaskonton](distribute-data-globally.md) som ger tydliga rätt balans mellan konsekvens, tillgänglighet och prestanda – allt med motsvarande garantier. Cosmos DB-konton ger hög tillgänglighet, entalssiffra ms fördröjning [väldefinierade konsekvensnivåer](consistency-levels.md), transparent regional redundans med flera API: er och möjligheten att skala Elastiskt dataflöde och lagring över i världen. 

Cosmos DB stöder både explicita och principen driven redundans som gör det möjligt att styra systembeteendet för slutpunkt till slutpunkt-vid fel. I den här artikeln ska titta vi på:

* Hur fungerar manuell växling vid fel i Cosmos DB?
* Hur automatisk redundans arbete i Cosmos DB och vad som händer när en data center går ned?
* Hur kan du använda manuell växling vid fel i arkitekturer?

Du kan också läsa om regional redundans i den här videon med Azure Cosmos DB-Programhanteraren Andrew Liu, som visar de globala distribution funktioner inklusive regional redundans.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Konfigurera program för flera regioner
Innan vi fördjupar oss i redundansväxlingen lägen tittar vi på hur du kan konfigurera ett program för att dra nytta av flera regional tillgänglighet och återhämtningsförmåga om regionala redundanser.

* Först distribuera ditt program i flera regioner
* För att säkerställa snabb svarstid från varje region som ditt program distribuerats, konfigurera motsvarande [över önskade regioner](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) för varje region via någon av de stödda SDK: erna.

Följande kodfragment visar hur du initierar ett program i flera regioner. Här, Azure Cosmos DB-kontot `contoso.documents.azure.com` är konfigurerad med två regioner – västra USA och Nordeuropa. 

* Programmet har distribuerats i regionen USA, västra (med Azure App Services exempelvis) 
* Konfigurerade med `West US` som den första önskad regionen för låg latens läser
* Konfigurerade med `North Europe` som andra önskad region (för hög tillgänglighet när regionala problem uppstått)

Den här konfigurationen ut som följande kodavsnitt i SQL-API:

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

Programmet distribueras också i regionen Europa, norra önskade regioner omvänd ordning. Det vill säga anges regionen Europa, norra först för läsningar med låg latens. Sedan har regionen West US angetts som den andra regionen som är prioriterade för hög tillgänglighet när regionala problem uppstått.

Följande Arkitekturdiagram visar en distribution av flera regioner där Cosmos DB och programmet har konfigurerats till att vara tillgängligt i fyra Azure geografiska regioner.  

![Distribution av globalt distribuerade program med Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Nu ska vi titta på hur Cosmos DB-tjänsten hanterar regionala fel via automatisk växling vid fel. 

## <a id="AutomaticFailovers"></a>Automatisk redundans
Om en Azure regionalt strömavbrott eller avbrott på datacentret utlöser Cosmos DB automatiskt redundanser för alla Cosmos DB-konton med i den berörda regionen. 

**Vad händer om en läsregion har ett avbrott?**

Cosmos DB-konton med en läsregion i någon av regionerna som påverkas är automatiskt kopplas bort från deras skrivregionen och markeras offline. Cosmos DB SDK implementerar en regional discovery-protokollet som låter dem identifiera automatiskt när en region är tillgänglig och omdirigering läsa anrop till den nästa tillgängliga regionen i listan över önskad region. Om ingen av regionerna i listan över önskad region är tillgänglig kan återgår anrop automatiskt till att den aktuella skrivregionen. Det krävs inga ändringar i din programkod för att hantera regionala redundanser. Under hela processen, konsekvensgarantier i fortsättningen att användas av Cosmos DB.

![Läs region fel i Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

När den berörda regionen återställer från driftstörningarna, återställs alla berörda Cosmos DB-konton i regionen automatiskt av tjänsten. Cosmos DB-konton som hade en läsregion i den berörda regionen och sedan automatiskt synkroniseras med den aktuella skrivregionen och aktivera online. Cosmos DB SDK identifiera tillgängligheten för den nya regionen och utvärdera om regionen som ska väljas som den aktuella läsregion baserat på önskad region-lista som konfigurerats av programmet. Efterföljande läsningar omdirigeras till den återställda regionen utan några ändringar i din programkod.

**Vad händer om en skrivregion har ett avbrott?**

Om den berörda regionen är den aktuella skrivregionen och automatisk redundans är aktiverat för Azure Cosmos DB-kontot, är regionen automatiskt markeras som offline. Sedan kan höjs en alternativ region som skrivregionen för berörda Azure Cosmos DB-kontot. Du kan aktivera automatisk redundans och helt styra i vilken ordning för val av region för dina Azure Cosmos DB-konton via Azure portal eller [programmässigt](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Prioriteter för redundans för Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Under automatisk redundans väljer Azure Cosmos DB automatiskt nästa skrivregionen för en viss Azure Cosmos DB-konto baserat på den angivna prioritetsordningen. Program kan använda egenskapen WriteEndpoint för DocumentClient-klassen för att identifiera ändringen i skrivregionen.

![Skriva region fel i Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

När den berörda regionen återställer från driftstörningarna, återställs alla berörda Cosmos DB-konton i regionen automatiskt av tjänsten. 

* Data som finns i föregående skrivregion som inte har replikerats om du vill läsa regioner under avbrottet har publicerats som en konflikt feed. Program kan läsa i konflikt flödet, baserat på specifika programlogiken konfliktlösning och skriva uppdaterade data tillbaka till Azure Cosmos DB-kontot efter behov. 
* Föregående skrivregion på nytt som en läsregion och online igen automatiskt. 
* Du kan konfigurera om läsregion som var online igen automatiskt som skrivbar genom att utföra en manuell redundansväxling från Azure Portal eller [programmässigt](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

Följande kodfragment visar hur du kan bearbeta konflikter när den berörda regionen återställer från avbrottet.

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

## <a id="ManualFailovers"></a>Manuell redundans

Förutom automatisk växling vid fel, kan den aktuella skrivregionen för en viss Cosmos DB-konto manuellt ändras dynamiskt till en av de befintliga läsregioner. Manuell redundans kan inledas via Azure portal eller [programmässigt](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Manuell redundans Kontrollera **noll dataförlust** och **noll tillgänglighet** förlust och smidigt skrivning Överföringsstatus från gammalt skrivregionen till den nya för det angivna Cosmos DB-kontot. Som i automatisk växling vid fel, Cosmos DB SDK automatiskt hanterar skrivning region ändringar vid manuell växling vid fel och säkerställer att anrop omdirigeras automatiskt till den nya skrivregionen. Det krävs inga kodning eller ändringar i din app för att hantera redundans. 

![Manuell redundans i Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Några vanliga scenarier där manuell redundans kan användas är:

**Följ klockan modellen**: om ditt program har förutsägbara trafikmönster baserat på tid på dagen, du regelbundet kan ändra status för skrivning till den mest aktiva geografiska region som är baserade på tid på dagen.

**Tjänstuppdateringen**: vissa distribution av globalt distribuerade program kan omfatta dirigera trafik till annan region via traffic manager under deras planerade tjänstuppdatering. Sådana programdistribution nu kan använda manuell redundans för att hålla status för skrivning till regionen där det ska vara aktiv trafik under uppdateringen servicetillfälle.

**Verksamhetskontinuitet och Disaster Recovery (BCDR) och hög tillgänglighet och katastrofåterställning (HADR) tester**: de flesta program omfattar business continuity tester som en del av sin utveckling och utgivning. BCDR och HADR testning är ofta ett viktigt steg i efterlevnadscertifieringar och garanterande tjänsttillgänglighet vid regionala avbrott. Du kan testa BCDR-beredskap för dina program som använder Cosmos DB för lagring av utlösa en manuell redundans för ditt Cosmos DB-konto och/eller att lägga till och ta bort en region dynamiskt.

Den här artikeln granskas hur manuell och automatisk redundans arbete i Cosmos DB och hur du kan konfigurera din Cosmos DB-konton och program ska vara globalt tillgängliga. Med stöd för Cosmos DB-global replikering kan du förbättra svarstiden för slutpunkt till slutpunkt och se till att de är med hög tillgänglighet även i händelse av fel region. 

## <a id="NextSteps"></a>Nästa steg
* Lär dig mer om hur Cosmos DB stöder [global distribution](distribute-data-globally.md)
* Lär dig mer om [global konsekvens med Azure Cosmos DB](consistency-levels.md)
* Utveckla med flera regioner med Azure Cosmos DB [SQL API](tutorial-global-distribution-sql-api.md)
* Lär dig hur du skapar [arkitekturer för flera regioner skrivaren](multi-region-writers.md) med Azure Cosmos DB

