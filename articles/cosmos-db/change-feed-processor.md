---
title: Ändringsflödesprocessorn i Azure Cosmos DB
description: Lär dig hur du använder Azure Cosmos DB ändra feed-processorn för att läsa ändrings flödet, komponenterna i bytet av Change feed
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: aa9b090627b6f27a54b67c361b45b6f99e3a6338
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982385"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Ändringsflödesprocessorn i Azure Cosmos DB

Processorn för förändrings flödet är en del av [Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Det fören klar processen med att läsa ändrings flödet och att distribuera händelse bearbetningen över flera konsumenter på ett effektivt sätt.

Den största fördelen med att ändra flödes processor bibliotek är dess feltoleranta beteende som säkerställer en "minst en gång"-leverans av alla händelser i ändrings flödet.

## <a name="components-of-the-change-feed-processor"></a>Komponenter i processorn för Change-feed

Det finns fyra huvud komponenter i implementeringen av Change feed-processorn:

1. **Den övervakade behållaren:** Den övervakade behållaren har data som ändrings flödet genereras från. Eventuella infogningar och uppdateringar av den övervakade behållaren visas i behållarens ändrings flöde.

1. **Leasing container:** Lease-behållaren fungerar som en tillstånds lagring och samordnar bearbetning av ändrings flödet över flera arbetare. Lease-behållaren kan lagras i samma konto som den övervakade behållaren eller i ett separat konto.

1. **Värden:** En värd är en program instans som använder en Change feed-processor för att lyssna efter ändringar. Flera instanser med samma låne konfiguration kan köras parallellt, men varje instans bör ha ett annat **instans namn**.

1. **Delegaten:** Delegaten är den kod som definierar vad du, utvecklaren vill göra med varje grupp ändringar som har lästs av Change feed-processorn. 

För att bättre förstå hur dessa fyra delar av ändra flödes processor fungerar tillsammans, ska vi titta på ett exempel i följande diagram. Den övervakade behållaren lagrar dokument och använder "ort" som partitionsnyckel. Vi ser att värdena för partitionsnyckel distribueras i intervall som innehåller objekt. Det finns två värd instanser och processorn för ändrings flöden tilldelar olika intervall med nyckel värden till varje instans för att maximera beräknings distributionen. Varje intervall läses parallellt och förloppet bevaras separat från andra intervall i leasing behållaren.

![Exempel på ändring av flödes processor](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>Implementera bearbetning av Change feeds-processorn

Posten är alltid den övervakade behållaren, från en `Container` instans som du anropar: `GetChangeFeedProcessorBuilder`

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Där den första parametern är ett distinkt namn som beskriver målet för den här processorn och det andra namnet är den ombuds implementering som ska hantera ändringarna. 

Ett exempel på ett ombud skulle vara:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Slutligen definierar du ett namn för processor instansen `WithInstanceName` med och som är behållaren för att underhålla låne status med `WithLeaseContainer`.

Genom `Build` att anropa får du den processor instans som du kan starta genom `StartAsync`att anropa.

## <a name="processing-life-cycle"></a>Bearbetnings livs cykel

Den normala livs cykeln för en värd instans är:

1. Läs ändrings flödet.
1. Om det inte finns några ändringar kan du försätta i vilo läge under en `WithPollInterval` fördefinierad tid (anpassningsbar med i-verktyget) och gå till #1.
1. Om det finns ändringar skickar du dem till **ombudet**.
1. När ombudet har slutfört bearbetningen **av ändringarna uppdaterar**du leasing lagret med den senaste bearbetade tidpunkten och går till #1.

## <a name="error-handling"></a>Felhantering

Processorn för ändrings flöden är elastisk för användar kod fel. Det innebär att om din ombuds implementering har ett ohanterat undantag (steg #4), stoppas tråd bearbetningen av den aktuella batchen av ändringar och en ny tråd skapas. Den nya tråden kontrollerar vilken som är den senaste tidpunkten som leasing lagret har för intervallet av partitionsnyckel, och sedan startar om därifrån, skickar samma batch med ändringar i delegaten. Det här beteendet fortsätter tills ditt ombud bearbetar ändringarna korrekt och det är orsaken till att den ändrade feed-processorn har en "minst en gång"-garanti, eftersom om Delegerings koden genererar ett undantag kommer den att försöka utföra batchen igen.

För att förhindra att en ändrings flödes processor får "fastnat" och samtidigt prova samma batch med ändringar, bör du lägga till logik i din ombuds kod för att skriva dokument, vid undantag, till en kö för obeställbara meddelanden. Den här designen garanterar att du kan hålla koll på obearbetade ändringar samtidigt som du fortfarande kan fortsätta att bearbeta framtida ändringar. Kön för obeställbara meddelanden kan bara vara en annan Cosmos-behållare. Det exakta data lagret spelar ingen roll, bara att de obearbetade ändringarna är bestående.

Dessutom kan du använda [uppskattningen ändra feed](how-to-use-change-feed-estimator.md) för att övervaka förloppet för dina byte av Change feed processor när de läser ändrings flödet. Förutom övervakningen om processorn för förändrings flödet får "fastnat" och försöker igen med samma batch med ändringar, kan du också förstå om din process för ändrings flöden håller på att förfalla efter tillgängliga resurser som processor, minne och nätverks bandbredd.

## <a name="dynamic-scaling"></a>Dynamisk skalning

Som vi nämnt under introduktionen kan bytet av byte-processorn distribuera data bearbetningen över flera instanser automatiskt. Du kan distribuera flera instanser av ditt program med hjälp av Change feed-processorn och dra nytta av det, de enda viktiga kraven är:

1. Alla instanser bör ha samma konfiguration för låne behållare.
1. Alla instanser ska ha samma arbets flödes namn.
1. Varje instans måste ha ett annat instans namn (`WithInstanceName`).

Om dessa tre villkor är uppfyllda, kommer byte av byte-processorn att använda en algoritm för samma distribution, distribuera alla lån i leasing behållaren över alla instanser som körs och parallellisera-beräkning. Ett lån kan bara ägas av en instans vid en specifik tidpunkt, så det maximala antalet instanser är lika med antalet lån.

Antalet instanser kan utökas och krympas dynamiskt, och processorn för förändrings flödet justerar belastningen dynamiskt genom att distribuera om enligt detta.

Dessutom kan Processorn för förändrings flöden dynamiskt justera till behållarens skala på grund av data flöde eller lagrings utrymme. När din behållare växer, hanterar Change feed-processorn transparent dessa scenarier genom att dynamiskt öka lånet och distribuera nya lån mellan befintliga instanser.

## <a name="change-feed-and-provisioned-throughput"></a>Ändra feed och tillhandahållet data flöde

Du debiteras för ru: er som förbrukas, eftersom data förflyttning in och ut ur Cosmos-behållare använder alltid ru: er. Du debiteras för ru: er som används av leasing containern.

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Användnings exempel på GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Ytterligare exempel på GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta med att lära dig mer om att ändra flödes processor i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Ändra feed-pull-modell](change-feed-pull-model.md)
* [Så här migrerar du från biblioteket Change feed processor](how-to-migrate-from-change-feed-library.md)
* [Använda ändringsflödesövervakaren](how-to-use-change-feed-estimator.md)
* [Starttid för ändringsflödesprocessor](how-to-configure-change-feed-start-time.md)
