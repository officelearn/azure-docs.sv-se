---
title: Bygga mobilappar med Xamarin och Azure Cosmos DB | Microsoft Docs
description: En självstudiekurs som skapar en Xamarin iOS-, Android- eller Forms-app med Azure Cosmos DB. Azure Cosmos DB är en snabb, världsomfattande molndatabas för mobilappar.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: b70a16c2f4c17a8cee008bee342a9c3693d1eb4b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34614500"
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Bygga mobilappar med Xamarin och Azure Cosmos DB

De flesta mobilappar måste lagra data i molnet och Azure Cosmos DB är en molndatabas för mobilappar. Den innehåller allt som mobilutvecklare behöver. Den är en fullständigt hanterad databas som en tjänst som kan skalas på begäran. Den överför dina data till tillämpningsprogrammet transparent, oavsett var dina användare befinner sig i världen. Med hjälp av [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) kan du aktivera Xamarin-mobilappar så att de kan interagera direkt med Azure Cosmos DB, utan mellannivå.

Den här artikeln innehåller en självstudiekurs för att skapa mobilappar med Xamarin och Azure Cosmos DB. Du hittar källkoden för hela självstudiekursen på [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), inklusive information om hur du hanterar användare och behörigheter.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Azure Cosmos DB-funktioner för mobilappar
Azure Cosmos DB innehåller följande viktiga funktioner för utvecklare av mobilappar:

![Azure Cosmos DB-funktioner för mobilappar](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Omfattande frågor för schemalösa data. Azure Cosmos DB lagrar data som schemalösa JSON-dokument i heterogena samlingar. Den erbjuder [omfattande och snabba frågor](sql-api-sql-query.md) utan att du behöver bekymra dig om scheman eller index.
* Högt dataflöde. Det tar bara några millisekunder att läsa och skriva dokument med Azure Cosmos DB. Utvecklare kan specificera vilket dataflöde de behöver och Azure Cosmos DB erbjuder ett serviceavtal med 99,99 % tillgänglighet för alla konton med tillgång till en eller flera regioner med konsekvensmodellen ”relaxed” (avslappnad), samt 99,999 % läsningstillgänglighet för alla databaskonton med tillgång till flera regioner.
* Obegränsad skalning. Dina Azure Cosmos DB-samlingar [växer i takt med din app](partition-data.md). Du kan starta med ett liten datastorlek och ett dataflöde på ett par hundra förfrågningar per sekund. Dina samlingar eller databaser kan växa till flera petabyte data och ett högt dataflöde med hundratals miljoner förfrågningar per sekund.
* Globalt distribuerad. Användare av mobilappar är ofta på resande fot över hela världen. Azure Cosmos DB är en [globalt distribuerad databas](distribute-data-globally.md). Klicka på kartan för att göra data tillgängliga för användarna.
* Inbyggd omfattande autentisering. Med Azure Cosmos DB kan du enkelt implementera populära mönster som [data per användare](https://aka.ms/documentdb-xamarin-todouser) eller delade data för flera användare utan kod för komplex anpassad autentisering.
* Geospatiala frågor. Många mobilappar erbjuder idag geo-baserade upplevelser. Med förstklassigt stöd för [geospatiala typer](geospatial.md) gör Azure Cosmos DB det enkelt att skapa sådana upplevelser.
* Binära bilagor. Dina appdata innehåller ofta binära blobar. Inbyggt stöd för bifogade filer gör Azure Cosmos DB till det enda du behöver för din app.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Självstudiekurs för Azure Cosmos DB och Xamarin
Följande självstudiekurs visar hur du skapar en mobilapp med Xamarin och Azure Cosmos DB. Du hittar källkoden för hela självstudiekursen på [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Kom igång
Det är lätt att komma igång med Azure Cosmos DB. Gå till Azure Portal och skapa ett nytt Azure Cosmos DB-konto. Klicka på fliken **Snabbstart**. Ladda ned Xamarin Forms-exemplet för att göra-listan som redan är kopplad till ditt Azure Cosmos DB-konto. 

![Azure Cosmos DB snabbstart för mobilappar](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Eller om du har en befintlig Xamarin-app kan du lägga till [Azure Cosmos DB NuGet-paketet](sql-api-sdk-dotnet-core.md). Azure Cosmos DB har stöd för Xamarin.IOS, Xamarin.Android och delade bibliotek för Xamarin Forms.

### <a name="work-with-data"></a>Arbeta med data
Dina dataposter lagras i Azure Cosmos DB som schemalösa JSON-dokument i heterogena samlingar. Du kan lagra dokument med olika strukturer i samma samling:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Du kan använda språkintegrerade frågor över schemalösa data i dina Xamarin-projekt:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Lägga till användare
Som många kom igång-exempel autentiseras det Azure Cosmos DB-exempel som du laddar ned för tjänsten med hjälp av en huvudnyckel som är hårdkodad i appens kod. Den här standardinställningen är inte bra för appar du tänker dig ska kunna köras var som helst utom på din lokala emulator. Om en obehörig användare får tag på huvudnyckeln kan alla data i ditt Azure Cosmos DB-konto äventyras. Istället vill du att appen bara ska få åtkomst till poster som hör till den inloggade användaren. Azure Cosmos DB gör att utvecklare kan bevilja läsbehörighet eller läs- och skrivbehörighet för programmet till en samling, en uppsättning dokument grupperade efter en partitionsnyckel eller ett visst dokument. 

Följ dessa anvisningar om du vill ändra att göra-appen till en app med en att göra-lista för flera användare: 

  1. Lägg till inloggningen i appen med hjälp av Facebook, Active Directory eller en annan provider.

  2. Skapa en Azure Cosmos DB UserItems-samling med **/userId** som partitionsnyckel. Genom att ange partitionsnyckeln för samlingen kan Azure Cosmos DB skala oändligt när antalet appanvändare växer, utan att hastigheten för frågorna påverkas.

  3. Lägg till asynkron Azure Cosmos DB-meddelandekö för resurstoken. Detta enkla webb-API autentiserar användare och utfärdar tillfälliga token för inloggade användare som bara ger åtkomst till dokument inom deras partition. I det här exemplet finns den asynkrona meddelandekön för resurstoken i App Service.

  4. Ändra appen så att den asynkrona meddelandekön för resurstoken autentiseras med Facebook och begär resurstoken för inloggade Facebook-användare. Du kan sedan komma åt deras data i samlingen UserItems.  

Du hittar en fullständig kodsamling för det här mönstret under den [asynkrona meddelandekön för resurstoken på GitHub](http://aka.ms/documentdb-xamarin-todouser). Det här diagrammet visar lösningen:

![Azure DB Cosmos-användare och asynkron meddelandekö för behörigheter](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Om du vill att två användare ska ha åtkomst till samma att göra-lista kan du lägga till ytterligare behörigheter för åtkomsttoken i den asynkrona meddelandekön för resurstoken.

### <a name="scale-on-demand"></a>Skala på begäran
Azure Cosmos-DB är en hanterad databas som en tjänst. När användarbasen växer behöver du inte tänka på att behöva etablera virtuella datorer eller att öka kärnorna. Allt du behöver tala om för Azure Cosmos DB är hur många åtgärder per sekund (dataflöde) din app behöver. Du kan ange dataflödet via fliken **Skala** genom att använda ett mått på dataflödet som kallas enheter för programbegäran (RU) per sekund. En läsåtgärd på ett dokument på 1 kB kräver exempelvis 1 RU. Du kan också lägga till aviseringar till måttet för **dataflöde** för att övervaka trafiktillväxten och programmässigt ändra dataflödet när aviseringar utlöses.

![Azure DB Cosmos skalar dataflödet på begäran](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Världsomfattande skalning
Allt eftersom din app blir populärare kan du få användare över hela världen. Eller så kanske du vill vara förbered för oförutsedda händelser. Gå till Azure Portal och öppna ditt Azure Cosmos DB-konto. Klicka på kartan för att kontinuerligt replikera dina data till flera regioner över hela världen. Den här funktionen gör dina data tillgängliga oavsett var användarna är. Du kan också lägga till redundans-principer som extra säkerhet.

![Azure DB Cosmos skalar över geografiska områden](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Grattis! Du har slutfört lösningen och har en mobilapp med Xamarin och Azure Cosmos DB. Cordova-appar byggs på liknande sätt med hjälp av Azure Cosmos DB JavaScript SDK och inbyggda iOS-/Android-appar med hjälp av Azure Cosmos DB REST API:er.

## <a name="next-steps"></a>Nästa steg
* Visa källkoden för [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Ladda ned [Cosmos Azure DB .NET Core SDK](sql-api-sdk-dotnet-core.md).
* Hitta fler kodexempel för [.NET-program](sql-api-dotnet-samples.md).
* Lär dig mer om [Azure Cosmos DB omfattande frågefunktioner](sql-api-sql-query.md).
* Lär dig mer om [geospatialt stöd i Azure Cosmos DB](geospatial.md).



