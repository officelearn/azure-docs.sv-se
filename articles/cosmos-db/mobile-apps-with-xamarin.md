---
title: Skapa mobila program med Xamarin och Azure Cosmos DB | Microsoft Docs
description: "En självstudiekurs som skapar en Xamarin-iOS, Android eller formulär program med hjälp av Azure Cosmos DB. Azure Cosmos DB är en snabb, planeten skala, molnet databas för mobila appar."
services: cosmos-db
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 5ab36a16ca87ae7a3e7b26571fc6aadd108841c9
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Skapa mobila program med Xamarin och Azure Cosmos DB
De flesta mobila appar måste du lagra data i molnet och Azure Cosmos DB är en moln-databas för mobila appar. Den innehåller allt mobila utvecklare behöver. Det är en helt hanterad databas som en tjänst som kan skalas på begäran. Den kan ge dina data i tillämpningsprogrammet transparent, oavsett var användarna befinner sig över hela världen. Med hjälp av den [Azure Cosmos DB .NET Core SDK](documentdb-sdk-dotnet-core.md), kan du aktivera Xamarin mobila appar kan interagera direkt med Azure Cosmos DB, utan en mellannivå.

Den här artikeln innehåller en vägledning för att skapa mobila appar med Xamarin och Azure Cosmos DB. Du hittar källkoden för fullständig genomgång på [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), inklusive hur du hanterar användare och behörigheter.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Azure DB Cosmos-funktioner för mobila appar
Azure Cosmos-DB innehåller följande viktiga funktioner för utvecklare av mobila program:

![Azure DB Cosmos-funktioner för mobila appar](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Omfattande frågor över schemalös data. Azure Cosmos-DB lagrar data som schemalös JSON-dokument i heterogena samlingar. Den erbjuder [omfattande och snabb frågor](documentdb-sql-query.md) utan att behöva bekymra dig om scheman eller index.
* Snabb genomflöde. Det tar bara några millisekunder att läsa och skriva dokument med Azure Cosmos DB. Utvecklare kan ange genomströmning som de behöver och Azure Cosmos DB godkänner den med 99,99% tillgänglighet SLA för alla enskild region och konton för alla flera regioner med Avslappnad konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databas .
* Obegränsad skala. Azure DB som Cosmos-samlingar [växer när din app växer](partition-data.md). Du kan starta med liten storlek och dataflöde hundratals begäranden per sekund. Samlingar kan växa till petabyte data och godtyckligt högt dataflöde med hundratals miljoner förfrågningar per sekund.
* Globalt distribuerad. Användare av mobila appar är i farten, ofta över hela världen. Azure Cosmos-databasen är en [globalt distribuerad databas](distribute-data-globally.md). Klicka på kartan för att göra data tillgängliga för användarna.
* Inbyggda omfattande auktorisering. Med Azure Cosmos DB, kan du enkelt implementera populära mönster som [användardata](https://aka.ms/documentdb-xamarin-todouser) eller fleranvändar delade data utan kod för komplexa anpassad auktorisering.
* Geospatiala frågor. Geo-kontextuella upplevelser erbjuder idag för flera mobila appar. Med förstklassigt stöd för [geospatiala typer](geospatial.md), Azure Cosmos DB gör att skapa upplevelserna lätt att åstadkomma.
* Binära bifogade filer. Din AppData innehåller ofta binär blobbar. Inbyggt stöd för bifogade filer gör det enklare att använda Azure Cosmos DB som en affär för din app.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB och Xamarin självstudiekursen
Följande kursen visar hur du skapar ett mobilt program med Xamarin och Azure Cosmos DB. Du hittar källkoden för fullständig genomgång på [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Kom igång
Det är lätt att komma igång med Azure Cosmos DB. Gå till Azure-portalen och skapa ett nytt konto i Azure Cosmos DB. Klicka på den **Snabbstart** fliken. Hämta Xamarin Forms uppgiften listan exemplet som redan är ansluten till din Azure DB som Cosmos-konto. 

![Azure Cosmos DB Snabbstart för mobila appar](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

Om du har en befintlig Xamarin-app kan du lägga till den [Azure Cosmos DB NuGet-paketet](documentdb-sdk-dotnet-core.md). Azure Cosmos-DB stöder Xamarin.IOS Xamarin.Android, och Xamarin Forms delade bibliotek.

### <a name="work-with-data"></a>Arbeta med data
Spara data lagras i Azure Cosmos DB som schemalös JSON-dokument i heterogena samlingar. Du kan lagra dokument med olika strukturer i samma samling:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Du kan använda språkintegrerade frågor över schemalös data i dina Xamarin-projekt:

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
Som många Kom igång exempel autentiseras Azure DB som Cosmos-exempel som du hämtade för tjänsten med hjälp av en huvudnyckel hårdkodad i appens kod. Den här standardinställningen är inte bra för en app som du tänker köra var som helst utom på din lokala emulatorn. Om en obehörig användare som fick huvudnyckeln äventyras alla data över ditt konto i Azure Cosmos DB. I stället vill du appen för att få åtkomst till endast poster för den inloggade användaren. Azure Cosmos-DB gör att utvecklare kan bevilja läsbehörighet för programmet eller läsning och skrivning behörighet till en samling, en uppsättning grupperade efter en partitionsnyckel dokument eller ett visst dokument. 

Följ dessa steg om du vill ändra uppgiften listan appen till en app för flera användare att göra-lista: 

  1. Lägga till inloggningen i appen med hjälp av Facebook, Active Directory eller någon annan provider.

  2. Skapa en samling Azure Cosmos DB UserItems med **/userId** som partitionsnyckel. Genom att ange Partitionsnyckeln för samlingen kan Azure Cosmos DB att skala oändligt när antalet appanvändare som din växer när fortsätter att erbjuda snabb frågor.

  3. Lägg till Azure Cosmos DB resurs Token Broker. Den här enkla Web API autentiserar användare och problem tillfällig tokens för att inloggade användare med endast åtkomst till dokument inom deras partition. I det här exemplet finns resurs Token Broker i App Service.

  4. Ändra appen för att autentisera till resursen Token Broker med Facebook och begära resurs-token för Facebook inloggade användare. Du kan sedan komma åt sina data i samlingen UserItems.  

Du hittar en fullständig kodexemplet i det här mönstret på [resurs Token Broker på GitHub](http://aka.ms/documentdb-xamarin-todouser). Det här diagrammet visar lösningen:

![Azure DB Cosmos-användare och behörigheter broker](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Om du vill att två användare får åtkomst till samma uppgiftslista kan du lägga till ytterligare behörigheter åtkomst-token i resursen Token Broker.

### <a name="scale-on-demand"></a>Skala på begäran
Azure Cosmos-DB är en hanterad databas som en tjänst. När användarbasen växer kan behöver du inte bry dig om etablering av virtuella datorer eller att öka kärnor. Allt du behöver se Azure Cosmos DB är hur många åtgärder per sekund (genomströmning) din app måste. Du kan ange genomströmning via den **skala** fliken med hjälp av ett mått på dataflödet som kallas begära enheter (RUs) per sekund. Till exempel en Läsåtgärd på ett dokument på 1 KB kräver 1 RU. Du kan också lägga till aviseringar till den **genomströmning** mått för att övervaka trafik tillväxt och programmässigt Ändra genomströmning som aviseringar brand.

![Azure DB Cosmos skala genomströmning på begäran](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>Gå planeten skala
Du kan få användare världen som din app får popularitet. Eller så kanske du vill att förbereda för oförutsedda händelser. Gå till Azure-portalen och öppna Azure DB som Cosmos-konto. Klicka på kartan för att göra dina data kontinuerligt replikera till flera regioner över hela världen. Den här funktionen gör data tillgängliga var användarna är. Du kan också lägga till principer för växling vid fel förberedas för risker.

![Azure DB Cosmos skala över geografiska områden](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

Grattis! Du har slutfört lösningen och har en mobil app med Xamarin och Azure Cosmos DB. Följa liknande steg för att skapa Cordova-appar med hjälp av Azure Cosmos DB JavaScript SDK och inbyggda iOS/Android-appar med hjälp av Azure Cosmos DB REST API: er.

## <a name="next-steps"></a>Nästa steg
* Visa källkoden för [Xamarin och Azure Cosmos DB på GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Hämta den [Cosmos Azure DB .NET Core SDK](documentdb-sdk-dotnet-core.md).
* Hitta fler kodexempel för [.NET-program](documentdb-dotnet-samples.md).
* Lär dig mer om [Azure Cosmos DB omfattande fråga funktioner](documentdb-sql-query.md).
* Lär dig mer om [geospatiala stöd i Azure Cosmos DB](geospatial.md).



