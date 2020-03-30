---
title: Felsöka problem när du använder Azure Functions-utlösare för Cosmos DB
description: Vanliga problem, lösningar och diagnostiksteg när du använder Azure Functions-utlösaren för Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365516"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnostisera och felsöka problem när du använder Azure Functions-utlösare för Cosmos DB

Den här artikeln innehåller vanliga problem, lösningar och diagnostiksteg när du använder [Azure Functions-utlösaren för Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Beroenden

Azure Functions-utlösaren och bindningarna för Cosmos DB är beroende av tilläggspaketen över den grundläggande Azure Functions-körningen. Håll alltid dessa paket uppdaterade, eftersom de kan innehålla korrigeringar och nya funktioner som kan lösa eventuella problem som du kan stöta på:

* För Azure Functions V2 finns i [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* För Azure Functions V1 finns i [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Den här artikeln refererar alltid till Azure Functions V2 när körningen nämns, om inte uttryckligen anges.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Använda Azure Cosmos DB SDK oberoende av detta

Nyckelfunktionerna i tilläggspaketet är att ge stöd för Azure Functions-utlösaren och bindningarna för Cosmos DB. Den innehåller också [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), vilket är användbart om du vill interagera med Azure Cosmos DB programmässigt utan att använda utlösaren och bindningarna.

Om du vill använda Azure Cosmos DB SDK kontrollerar du att du inte lägger till en annan NuGet-paketreferens i projektet. Låt i stället **SDK-referensen lösa via Azure Functions Extension-paketet**. Använda Azure Cosmos DB SDK separat från utlösaren och bindningarna

Om du skapar din egen instans av [Azure Cosmos DB SDK-klienten](./sql-api-sdk-dotnet-core.md)manuellt bör du dessutom följa mönstret för att bara ha en instans av klienten med hjälp av [en Singleton-mönstermetod](../azure-functions/manage-connections.md#documentclient-code-example-c). Den här processen undviker potentiella socketproblem i din verksamhet.

## <a name="common-scenarios-and-workarounds"></a>Vanliga scenarier och lösningar

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure-funktionen misslyckas med att samla in felmeddelanden finns inte

Azure-funktionen misslyckas med felmeddelandet "Antingen finns inte källsamlingen 'samlingsnamn' (i databasen 'databasnamn') eller lånesamlingen 'collection2-name' (i databasen 'database2-name'). Båda samlingarna måste finnas innan lyssnaren startar. Om du vill skapa lånesamlingen automatiskt anger du "CreateLeaseCollectionIfNotExists" till 'true'"

Det innebär att antingen en eller båda Azure Cosmos-behållare som krävs för att utlösaren ska fungera inte finns eller inte kan nås till Azure-funktionen. **Själva felet talar om för dig vilken Azure Cosmos-databas och behållare som är den utlösare som letar efter** baserat på din konfiguration.

1. Verifiera `ConnectionStringSetting` attributet och att det **refererar till en inställning som finns i din Azure Function App**. Värdet för det här attributet bör inte vara själva anslutningssträngen, utan namnet på konfigurationsinställningen.
2. Kontrollera att `databaseName` `collectionName` och finns i ditt Azure Cosmos-konto. Om du använder automatisk värdeersättning (med mönster) `%settingName%` kontrollerar du att namnet på inställningen finns i din Azure Function App.
3. Om du inte anger `LeaseCollectionName/leaseCollectionName`en är standardvärdet "lån". Kontrollera att det finns en sådan behållare. Du kan också `CreateLeaseCollectionIfNotExists` ställa in attributet i utlösaren så att `true` det skapas automatiskt.
4. Verifiera [brandväggskonfigurationen för ditt Azure Cosmos-konto](how-to-configure-firewall.md) för att se att det inte blockerar Azure-funktionen.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure-funktionen kan inte startas med "Insamling av delat dataflöde bör ha en partitionsnyckel"

De tidigare versionerna av Azure Cosmos DB Extension har inte stöd för att använda en lånebehållare som skapades i en [delad dataflödesdatabas](./set-throughput.md#set-throughput-on-a-database). LÃ¶s problemet genom att uppdatera tillägget [Microsoft.Azure.WebJobs.Extensions.CosmosDB fÃ¶r](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) att få den senaste versionen.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure-funktionen kan inte startas med "PartitionKey måste anges för den här åtgärden."

Det här felet innebär att du för närvarande använder en partitionerad lånesamling med ett gammalt [tilläggsberoende](#dependencies). Uppgradera till den senaste tillgängliga versionen. Om du för närvarande körs på Azure Functions V1 måste du uppgradera till Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure-funktionen kan inte börja med "Lånesamlingen, om den partitioneras, måste ha partitionsnyckel som är lika med id.",

Det här felet innebär att din nuvarande lånebehållare är partitionerad, men sökvägen till partitionsnyckeln är inte `/id`. För att lösa problemet måste du återskapa `/id` lånebehållaren med som partitionsnyckel.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Du ser ett "Värde kan inte vara null. Parameternamn: o" i dina Azure Functions-loggar när du försöker köra utlösaren

Det här problemet visas om du använder Azure-portalen och du försöker välja **knappen Kör** på skärmen när du inspekterar en Azure-funktion som använder utlösaren. Utlösaren kräver inte att du väljer Kör för att starta, den startar automatiskt när Azure-funktionen distribueras. Om du vill kontrollera Azure-funktionens loggström på Azure-portalen, bara gå till din övervakade behållare och infoga några nya objekt, kommer du automatiskt att se utlösaren köra.

### <a name="my-changes-take-too-long-to-be-received"></a>Mina ändringar tar för lång tid att ta emot

Det här scenariot kan ha flera orsaker och alla bör kontrolleras:

1. Distribueras din Azure-funktion i samma region som ditt Azure Cosmos-konto? För optimal nätverksfördröjning bör både Azure-funktionen och ditt Azure Cosmos-konto vara i samma Azure-region.
2. Sker ändringarna i din Azure Cosmos-container kontinuerligt eller sporadiskt?
Om de sker sporadiskt kan det uppstå en fördröjning mellan det att ändringarna sparas och Azure-funktionen hämtar dem. Detta beror på att när utlösaren söker efter ändringar i Azure Cosmos-containern och inte hittar någon som väntar på att läsas hamnar den i viloläge en viss tid (fem sekunder som standard) innan den söker efter nya ändringar (för att undvika hög RU-förbrukning). Du kan ändra vilotiden med inställningen `FeedPollDelay/feedPollDelay` i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) för utlösaren (värdet förväntas anges i millisekunder).
3. Din Azure Cosmos-behållare kan vara [klassbegränsad](./request-units.md).
4. Du kan `PreferredLocations` använda attributet i utlösaren för att ange en kommaavgränsad lista över Azure-regioner för att definiera en anpassad önskad anslutningsordning.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Vissa ändringar upprepas i min trigger

Begreppet "ändring" är en åtgärd i ett dokument. De vanligaste scenarierna där händelser för samma dokument tas emot är:
* Kontot använder slutlig konsekvens. När du konsumerar ändringsflödet på en eventuell konsekvensnivå kan det finnas dubbletthändelser mellan efterföljande ändringsflödesläsningsåtgärder (den sista händelsen i en läsåtgärd visas som den första i nästa).
* Dokumentet uppdateras. Ändringsflödet kan innehålla flera åtgärder för samma dokument, om dokumentet tar emot uppdateringar kan den hämta flera händelser (en för varje uppdatering). Ett enkelt sätt att skilja mellan olika åtgärder `_lsn` för samma dokument är att spåra [egenskapen för varje ändring](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Om de inte matchar är det olika ändringar jämfört med samma dokument.
* Om du identifierar dokument `id`bara efter , kom ihåg att `id` den unika identifieraren för ett dokument `id` är och dess partitionsnyckel (det kan finnas två dokument med samma men olika partitionsnyckel).

### <a name="some-changes-are-missing-in-my-trigger"></a>Vissa ändringar saknas i min trigger

Om du upptäcker att vissa av de ändringar som hände i azure Cosmos-behållaren inte hämtas av Azure-funktionen, finns det ett första undersökningssteg som måste ske.

När din Azure-funktion tar emot ändringarna bearbetar den ofta dem och kan eventuellt skicka resultatet till ett annat mål. När du undersöker saknade ändringar ska du se till **att mäta vilka ändringar som tas emot vid inmatningspunkten** (när Azure-funktionen startar), inte på målet.

Om vissa ändringar saknas på målet kan det innebära att det inträffar något fel under Azure Function-körningen efter att ändringarna har tagits emot.

I det här fallet är det `try/catch` bästa tillvägagångssättet att lägga till block i koden och inuti loopar som kan bearbeta ändringarna, att upptäcka eventuella fel för en viss delmängd av objekt och hantera dem därefter (skicka dem till en annan lagring för ytterligare analys eller återförsök). 

> [!NOTE]
> Som standard kommer Azure Functions-utlösaren inte att köra en ändringssats om det inträffar ett ohanterat undantag under körningen av din kod. Detta innebär att anledningen till att ändringarna inte kom fram till destinationen beror på att du inte bearbetar dem.

Om du upptäcker att vissa ändringar inte har tagits emot alls av utlösaren är det vanligaste scenariot att det finns **en annan Azure-funktion som körs**. Det kan vara en annan Azure-funktion som distribueras i Azure eller en Azure-funktion som körs lokalt på en utvecklares dator som har **exakt samma konfiguration** (samma övervakade och lånebehållare), och den här Azure-funktionen stjäl en delmängd av de ändringar du förväntar dig att din Azure-funktion ska bearbetas.

Dessutom kan scenariot valideras om du vet hur många Azure Function App-instanser du har kört. Om du inspekterar din lånebehållare och räknar antalet låneartiklar `Owner` inom, ska de distinkta värdena för egenskapen i dem vara lika med antalet instanser i funktionsappen. Om det finns fler ägare än de kända Azure Function App-instanserna betyder det att dessa extra ägare är de som "stjäl" ändringarna.

Ett enkelt sätt att komma runt denna `LeaseCollectionPrefix/leaseCollectionPrefix` situation, är att tillämpa en på din funktion med ett nytt / annat värde eller alternativt testa med en ny lånebehållare.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Behöver starta om och bearbeta om alla objekt i behållaren från början 
Så här bearbetar du om alla artiklar i en behållare från början:
1. Stoppa din Azure-funktion om den körs för tillfället. 
1. Ta bort dokumenten i lånesamlingen (eller ta bort och återskapa lånesamlingen så att den är tom)
1. Ange [attributet StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger i din funktion till true. 
1. Starta om Azure-funktionen. Det kommer nu att läsa och bearbeta alla förändringar från början. 

Om du ställer in [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) till true kommer Azure-funktionen att tala om för Azure-funktionen att börja läsa ändringar från början av samlingens historik i stället för den aktuella tiden. Detta fungerar bara när det inte redan finns några redan skapade lån (det vill än dokument i lånesamlingen). Att ange den här egenskapen till true när det redan finns lån har ingen effekt. I det här fallet när en funktion stoppas och startas om, kommer den att börja läsa från den sista kontrollpunkten, enligt definitionen i lånesamlingen. Om du vill bearbeta om från början följer du stegen ovan 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Bindning kan endast göras med\<IReadOnlyList-dokument> eller JArray

Det här felet inträffar om ditt Azure Functions-projekt (eller något refererat projekt) innehåller en manuell NuGet-referens till Azure Cosmos DB SDK med en annan version än den som tillhandahålls av [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies).

För att komma runt den här situationen, ta bort den manuella NuGet-referensen som lades till och låta Azure Cosmos DB SDK-referensen lösa via Azure Functions Cosmos DB Extension-paketet.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Ändra Avsökningsintervallet för Azure-funktionen för identifieringsändringar

Som tidigare förklarats för [Mina ändringar tar för lång tid att ta emot](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received), Azure-funktionen kommer att sova under en konfigurerbar tid (5 sekunder, som standard) innan du söker efter nya ändringar (för att undvika hög RU-förbrukning). Du kan ändra vilotiden med inställningen `FeedPollDelay/feedPollDelay` i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) för utlösaren (värdet förväntas anges i millisekunder).

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning för dina Azure-funktioner](../azure-functions/functions-monitoring.md)
* [Felsökning av Azure Cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)
