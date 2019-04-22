---
title: Diagnostisera och felsöka problem när du använder Azure Cosmos DB-utlösare i Azure Functions
description: Vanliga problem och lösningar diagnos, när du använder Azure Cosmos DB-utlösare med Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 04/16/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5416c576e6392b7c5714ef83f152453aeff8d964
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685958"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnostisera och felsöka problem när du använder Azure Cosmos DB-utlösare i Azure Functions

Den här artikeln beskriver vanliga problem och lösningar diagnos, när du använder den [Azure Cosmos DB-utlösare](change-feed-functions.md) med Azure Functions.

## <a name="dependencies"></a>Beroenden

Azure Cosmos DB-utlösare och bindningar beror på tilläggspaket över den grundläggande Azure Functions-körningen. Alltid ha dessa paket uppdateras, eftersom de kan innehålla korrigeringar och nya funktioner som kan lösa alla potentiella problem som kan uppstå:

* Azure Functions V2 finns [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Azure Functions V1 finns [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Den här artikeln kommer alltid att referera till Azure Functions V2 när körningen har vi redan nämnt om inget anges uttryckligen.

## <a name="consuming-the-cosmos-db-sdk-separately-from-the-trigger-and-bindings"></a>Använda Cosmos DB SDK separat från utlösare och bindningar

Viktiga funktioner för tillägget paketet är att ge stöd för Azure Cosmos DB-utlösare och bindningar. Den innehåller också de [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), vilket är användbart om du vill interagera med Azure Cosmos DB programmässigt utan att använda utlösare och bindningar.

Om vill till Azure Cosmos DB SDK måste du kontrollera att du inte lägger till ditt projekt en annan referens för NuGet-paketet. I stället **låta SDK-referens lösa via Azure Functions-tilläggspaket**.

Även om du manuellt skapar en egen instans av den [Azure Cosmos DB SDK-klienten](./sql-api-sdk-dotnet-core.md), bör du följa mönstret för att ha endast en instans av klienten [med hjälp av en metod för Singleton-mönster](../azure-functions/manage-connections.md#documentclient-code-example-c) . Den här processen undviker de potentiella socket problemen i din verksamhet.

## <a name="common-known-scenarios-and-workarounds"></a>Vanliga scenarier för kända och lösningar

### <a name="azure-function-fails-with-error-message-either-the-source-collection-collection-name-in-database-database-name-or-the-lease-collection-collection2-name-in-database-database2-name-does-not-exist-both-collections-must-exist-before-the-listener-starts-to-automatically-create-the-lease-collection-set-createleasecollectionifnotexists-to-true"></a>Azure-funktion misslyckas med felmeddelandet ”antingen källsamlingen 'samlingsnamn” (i databasen ”databas-name”) eller lånsamling ”samling2-name” (i databasen ”Databas2-name”) finns inte. Båda samlingarna måste finnas innan lyssnaren startar. Ange 'CreateLeaseCollectionIfNotExists' till 'true' för att automatiskt skapa samlingen lån ”

Detta innebär att en eller båda av Azure Cosmos-behållare som krävs för utlösaren ska fungera finns inte eller kan inte nås för Azure-funktionen. **Felet själva ange vilken Azure-Cosmos-databas och behållare är utlösaren söker** utifrån din konfiguration.

1. Kontrollera den `ConnectionStringSetting` attribut och att den **refererar till en inställning som finns i din Azure-Funktionsapp**. Värdet på det här attributet får inte vara anslutningssträngen själva, men namnet på Konfigurationsinställningen.
2. Kontrollera att den `databaseName` och `collectionName` finns i ditt Azure Cosmos-konto. Om du använder automatisk värdet ersättning (med hjälp av `%settingName%` mönster), kontrollera att namnet på inställningen finns i din Azure-Funktionsapp.
3. Om du inte anger en `LeaseCollectionName/leaseCollectionName`, standardvärdet är ”lån”. Kontrollera att sådana behållaren finns. Om du vill kan du ange den `CreateLeaseCollectionIfNotExists` attribut i utlösaren till `true` automatiskt skapa den.
4. Kontrollera din [brandväggskonfiguration för Azure Cosmos-konto](how-to-configure-firewall.md) för att visas för att visa att det inte är den inte blockerar Azure Function.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure-funktion misslyckas att börja med ”delade dataflöde samling ska ha en partitionsnyckel”

Tidigare versioner av tillägget Azure Cosmos DB inte stöd för att använda en lån-behållare som skapades i en [delade dataflöde databasen](./set-throughput.md#set-throughput-on-a-database). Lös problemet genom att uppdatera den [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) tillägg för att hämta den senaste versionen.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure-funktion misslyckas att börja med ”lånsamling, om partitionerad måste ha partitionsnyckel som är lika med ID: t”.

Det här felet innebär att din aktuella lån behållare är partitionerad, men partitionen som Nyckelsökväg är inte `/id`. För att lösa problemet behöver du skapa behållaren lån med `/id` som partitionsnyckel.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Du ser en ”värdet får inte vara null. Parameternamn: o ”i Azure Functions loggarna vid försök att köra utlösaren

Det här problemet visas om du använder Azure-portalen och du försöker att välja den **kör** knappen på skärmen vid kontroll av en Azure-funktion som använder sig av utlösaren. Utlösaren kräver inte att markera kör för att starta, startar automatiskt när Azure Function har distribuerats. Om du vill kontrollera loggström för Azure-funktion på Azure portal, bara gå till din övervakade behållare och infoga några nya objekt visas automatiskt utlösaren körs.

### <a name="my-changes-take-too-long-be-received"></a>Mina ändringar tar för lång tid ska tas emot

Det här scenariot kan ha flera orsaker och alla bör kontrolleras:

1. Har din Azure-funktion som distribuerats i samma region som ditt Azure Cosmos-konto? För optimala Nätverksfördröjningen samplaceras både Azure-funktion och ditt Azure Cosmos-konto i samma Azure-region.
2. Ändringarna som sker i din Azure Cosmos-behållare kontinuerlig eller sporadiska?
Om det är det senare kan uppstå det en fördröjning mellan ändringarna lagras och Azure-funktion som jobbar med dem. Det beror på att internt när utlösaren söker efter ändringar i din Azure Cosmos-behållare och hittar ingen väntar på att läsas, den kommer viloläge under en inställbar tidsperiod (5 sekunder, som standard) innan du söker efter nya ändringar (att undvika hög RU-förbrukning). Du kan konfigurera strömsparläge nu via den `FeedPollDelay/feedPollDelay` i den [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) av utlösaren (värdet förväntas vara i millisekunder).
3. Din Azure Cosmos-behållare kan vara [rate-limited](./request-units.md).
4. Du kan använda den `PreferredLocations` attribut i utlösaren att ange en kommaavgränsad lista över Azure-regioner att definiera en anpassad anslutning för önskad ordning.

### <a name="some-changes-are-missing-in-my-trigger"></a>Det saknas några ändringar i min utlösare

Om du upptäcker att vissa ändringar som har inträffat i din Azure Cosmos-behållare som inte plockas upp av Azure-funktion, finns det en inledande undersökning steg som måste äga rum.

När din Azure-funktion får ändringarna, den ofta bearbetar dem och kan eventuellt kan skicka resultatet till en annan plats. När du undersöker saknas ändringar, se till att du **mått som vilken ändringar tas emot på inläsningspunkten** (när Azure Function startar) inte på målet.

Om några ändringar saknas på målet, innebär det är något fel uppstod under aktivering av Azure-funktion-körning när ändringarna har tagits emot.

I det här scenariot är bästa åtgärden att lägga till `try/catch blocks` i din kod och inuti loopar som kanske kan behandla ändringar för att upptäcka eventuella fel för en delmängd av objekt och hantera dem enlighet med detta (skicka dem till en annan lagring för ytterligare analys eller försök igen). 

> **Azure Cosmos DB-utlösare som standard inte gör en batch med ändringar om det uppstod ett ohanterat undantag** under körning av din kod. Det innebär att anledningen till att ändringarna inte når målet är att du inte kan bearbeta dem på.

Om du upptäcker att vissa ändringar inte har tagits emot alls av utlösaren, det vanligaste scenariot är att det finns **ett annat Azure-funktion körs**. Det kan vara en annan Azure-funktion som distribueras i Azure eller en Azure-funktion som körs lokalt på en utvecklare dator som har **exakt samma konfiguration** (samma övervakas och låna ut behållare), och denna Azure Function stjäla en delmängd av de ändringar som du förväntar dig din Azure-funktion för att bearbeta.

Dessutom kan du kontrollera scenariot, om du vet hur många instanser i Azure Function-App som du har som körs. Om du vill inspektera behållaren lån och räkna antalet lån objekt inom de distinkta värdena för den `Owner` -egenskapen i dem vara lika med antalet instanser av din Funktionsapp. Om det finns fler ägare än kända instanserna i Azure Function-App, innebär det att följande extra ägare är en ”stjäla” ändringarna.

Ett enkelt sätt att lösa den här situationen är att tillämpa en `LeaseCollectionPrefix/leaseCollectionPrefix` till din funktion med en ny/annan värde eller du kan också testa med en ny lån-behållare.

## <a name="next-steps"></a>Nästa steg

* [Aktivera övervakning för Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK-felsökning](./troubleshoot-dot-net-sdk.md)