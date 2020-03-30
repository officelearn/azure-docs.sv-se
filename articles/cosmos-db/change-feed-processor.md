---
title: Ändra feedprocessorbibliotek i Azure Cosmos DB
description: Lär dig hur du använder Azure Cosmos DB ändra feed processorbibliotek för att läsa ändringsflödet, komponenterna i ändringsfeedprocessorn
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273308"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Ändringsflödesprocessorn i Azure Cosmos DB 

Ändringsfeedprocessorn är en del av [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Det förenklar processen att läsa förändringsflödet och distribuera händelsebearbetningen mellan flera konsumenter på ett effektivt sätt.

Den största fördelen med ändringsflödesprocessorbiblioteket är dess feltoleranta beteende som säkerställer en "minst en gång" leverans av alla händelser i ändringsflödet.

## <a name="components-of-the-change-feed-processor"></a>Komponenter i ändringsmatningsprocessorn

Det finns fyra huvudkomponenter för att implementera ändringsmatningsprocessorn: 

1. **Den övervakade behållaren:** Den övervakade behållaren har de data från vilka ändringsflödet genereras. Alla skär och uppdateringar av den övervakade behållaren återspeglas i behållarens ändringsflöde.

1. **Lånebehållaren:** Lånebehållaren fungerar som en tillståndslagring och samordnar bearbetningen av ändringsflödet för flera arbetare. Lånebehållaren kan lagras på samma konto som den övervakade behållaren eller i ett separat konto. 

1. **Värden:** En värd är en programinstans som använder ändringsfeedprocessorn för att lyssna efter ändringar. Flera instanser med samma lånekonfiguration kan köras parallellt, men varje instans bör ha ett annat **förekomstnamn**. 

1. **Delegaten:** Ombudet är den kod som definierar vad du, utvecklaren, vill göra med varje batch med ändringar som ändringsflödesbehandlaren läser. 

Om du vill veta mer hur dessa fyra element i ändringsflödesbehandlare fungerar tillsammans ska vi titta på ett exempel i följande diagram. Den övervakade behållaren lagrar dokument och använder "Stad" som partitionsnyckel. Vi ser att partitionsnyckelvärdena fördelas i områden som innehåller objekt. Det finns två värdinstanser och ändringsfeedprocessorn tilldelar olika intervall av partitionsnyckelvärden till varje instans för att maximera beräkningsdistributionen. Varje intervall läss parallellt och dess framsteg bibehålls separat från andra intervall i lånebehållaren.

![Ändra exempel på feed-processor](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementera ändringsmatningsprocessorn

Startpunkten är alltid den övervakade behållaren, från en `Container` instans som du anropar: `GetChangeFeedProcessorBuilder`

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Där den första parametern är ett distinkt namn som beskriver målet för den här behandlaren och det andra namnet är ombudsimplementeringen som hanterar ändringar. 

Ett exempel på ett ombud skulle vara:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Slutligen definierar du ett namn `WithInstanceName` för den här processorinstansen med och vilken som är behållaren för att underhålla lånetillståndet med `WithLeaseContainer`.

När `Build` du ringer får du den processorinstans som du kan börja med att ringa `StartAsync`.

## <a name="processing-life-cycle"></a>Bearbetning av livscykel

Den normala livscykeln för en värdinstans är:

1. Läs ändringsflödet.
1. Om det inte finns några ändringar, sova för en fördefinierad tid (anpassningsbar med `WithPollInterval` i Builder) och gå till #1.
1. Om det finns ändringar skickar du dem till **ombudet**.
1. När ombudet har **bearbetat**ändringarna uppdaterar du lånearkivet med den senast bearbetade tidpunkten och går till #1.

## <a name="error-handling"></a>Felhantering

Ändringsfeedprocessorn är motståndskraftig mot användarkodsfel. Det innebär att om din ombudsimplementering har ett ohanterat undantag (steg #4), stoppas trådbearbetningen som viss uppsättning ändringar och en ny tråd skapas. Den nya tråden kontrollerar vilken som var den senaste tidpunkten för lånearkivet har för det intervallet av partitionsnyckelvärden och startar om därifrån, vilket effektivt skickar samma batch med ändringar till ombudet. Detta fortsätter tills ombudet bearbetar ändringarna korrekt och det är anledningen till att ändringsfeedprocessorn har en "minst en gång"-garanti, för om ombudskoden genererar försöker den igen.

## <a name="dynamic-scaling"></a>Dynamisk skalning

Som nämnts under introduktionen kan ändringsfeedprocessorn distribuera beräkning över flera instanser automatiskt. Du kan distribuera flera instanser av ditt program med hjälp av ändringsflödesprocessorn och dra nytta av det, de enda viktiga kraven är:

1. Alla instanser bör ha samma lånebehållare konfiguration.
1. Alla instanser ska ha samma arbetsflödesnamn.
1. Varje instans måste ha ett`WithInstanceName`annat förekomstnamn ( ).

Om dessa tre villkor gäller, kommer ändringsfeedprocessorn, med hjälp av en lika distributionsalgoritm, att distribuera alla lån i lånebehållaren över alla gående instanser och parallellisera beräkning. Ett lån kan bara ägas av en instans vid en viss tidpunkt, så det maximala antalet instanser är lika med antalet leasingavtal.

Antalet instanser kan växa och minska, och ändringsmatningsprocessorn justerar belastningen dynamiskt genom att omfördelas därefter.

Dessutom kan ändringsmatningsprocessorn dynamiskt justeras till behållare skala på grund av dataflöde eller lagring ökar. När behållaren växer hanterar ändringsfeedprocessorn transparent dessa scenarier genom att dynamiskt öka lånen och distribuera de nya lånen mellan befintliga instanser.

## <a name="change-feed-and-provisioned-throughput"></a>Ändra flöde och etablerat dataflöde

Du debiteras för ru:er som förbrukas, eftersom data förflyttar sig in och ut ur Cosmos-behållare alltid förbrukar RU:er. Du debiteras för ru:er som förbrukas av lånebehållaren.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användningsexempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta med att läsa mer om ändringsflödesbehandlare i följande artiklar:

* [Översikt över ändringsflödet](change-feed.md)
* [Så här migrerar du från ändringsflödesprocessorbiblioteket](how-to-migrate-from-change-feed-library.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
* [Starttid för ändringsflödesprocessor](how-to-configure-change-feed-start-time.md)
