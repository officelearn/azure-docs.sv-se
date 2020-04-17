---
title: Ändra mönster för feeddesign i Azure Cosmos DB
description: Översikt över vanliga mönster för ändringsflödesdesign
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450359"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Ändra mönster för feeddesign i Azure Cosmos DB

Azure Cosmos DB-ändringsflödet möjliggör effektiv bearbetning av stora datauppsättningar med en hög mängd skrivningar. Ändringsfeeden erbjuder också ett alternativ till att fråga en hel datauppsättning för att identifiera vad som har ändrats. Det här dokumentet fokuserar på vanliga designmönster för ändringsflöden, designavvägningar och ändringsflöden.

Azure Cosmos DB är väl lämpad för IoT-, spel-, butiks- och driftloggningsprogram. Ett vanligt designmönster i dessa program är att använda ändringar i data för att utlösa ytterligare åtgärder. Exempel på ytterligare åtgärder är:

* Utlöser ett meddelande eller ett anrop till ett API när ett objekt infogas eller uppdateras.
* Direktuppspelningsbearbetning i realtid för IoT- eller realtidsanalysbearbetning på driftdata.
* Dataförflyttning som synkronisering med en cache, en sökmotor, ett informationslager eller kyllagring.

Ändringsflödet i Azure Cosmos DB gör att du kan skapa effektiva och skalbara lösningar för vart och ett av dessa mönster, vilket visas i följande avbildning:

![Använda Azure Cosmos DB ändra foder för att driva analyser i realtid och händelsedrivna datorscenarier](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Datoranvändning och meddelanden om evenemang

Azure Cosmos DB-ändringsflödet kan förenkla scenarier som behöver utlösa ett meddelande eller ett anrop till ett API baserat på en viss händelse. Du kan använda [ändringsflödesprocessbiblioteket](change-feed-processor.md) för att automatiskt avsöka behållaren efter ändringar och anropa ett externt API varje gång det finns en skriv- eller uppdatering.

Du kan också selektivt utlösa ett meddelande eller skicka ett anrop till ett API baserat på specifika kriterier. Om du till exempel läser från ändringsflödet med [Azure Functions](change-feed-functions.md)kan du placera logik i funktionen för att bara skicka ett meddelande om ett visst villkor har uppfyllts. Azure-funktionskoden skulle köras under varje skriv och uppdatering, men meddelandet skulle bara skickas om specifika kriterier hade uppfyllts.

## <a name="real-time-stream-processing"></a>Bearbetning av ström i realtid

Azure Cosmos DB-ändringsflödet kan användas för direktströmsbearbetning i realtid för IoT- eller realtidsanalysbearbetning på driftdata.
Du kan till exempel ta emot och lagra händelsedata från enheter, sensorer, infrastruktur och program och bearbeta dessa händelser i realtid med [Spark](../hdinsight/spark/apache-spark-overview.md). Följande avbildning visar hur du kan implementera en lambda-arkitektur med Hjälp av Azure Cosmos DB via ändringsflöde:

![Azure Cosmos DB-baserad lambda-pipeline för inmatning och fråga](./media/change-feed/lambda.png)

I många fall får dataflödesbearbetning implementeringar först en stor mängd inkommande data till en tillfällig meddelandekö som Azure Event Hub eller Apache Kafka. Ändringsflödet är ett bra alternativ på grund av Azure Cosmos DB:s förmåga att stödja en varaktig hög datainmatning med garanterad låg läs- och skrivfördröjning. Fördelarna med Azure Cosmos DB ändra foder över en meddelandekö inkluderar:

### <a name="data-persistence"></a>Data persistens

Data som skrivs till Azure Cosmos DB visas i ändringsflödet och behålls tills de tas bort. Meddelandeköer har vanligtvis en maximal kvarhållningsperiod. [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) erbjuder till exempel en maximal datalagring på 90 dagar.

### <a name="querying-ability"></a>Frågeförmåga

Förutom att läsa från en Cosmos-behållares ändringsfeed kan du även köra SQL-frågor på data som lagras i Azure Cosmos DB. Ändringsflödet är inte en dubblering av data som redan finns i behållaren utan bara en annan mekanism för att läsa data. Om du läser data från ändringsflödet är det därför alltid förenligt med frågor från samma Azure Cosmos DB-behållare.

### <a name="high-availability"></a>Hög tillgänglighet

Azure Cosmos DB erbjuder upp till 99,999 % läs- och skrivtillgänglighet. Till skillnad från många meddelandeköer kan Azure Cosmos DB-data enkelt distribueras globalt och konfigureras med ett [RTO -mål (Återställningstid)](consistency-levels-tradeoffs.md#rto) på noll.

När du har bearbetat artiklar i ändringsflödet kan du skapa en materialiserad vy och bevara aggregerade värden i Azure Cosmos DB. Om du använder Azure Cosmos DB för att skapa ett spel kan du till exempel använda ändringsflödet för att implementera rankningslistor i realtid baserat på poäng från slutförda spel.

## <a name="data-movement"></a>Dataförflyttning

Du kan också läsa från ändringsflödet för dataförflyttning i realtid.

Ändringsflödet hjälper dig till exempel att utföra följande uppgifter effektivt:

* Uppdatera en cache, ett sökindex eller ett informationslager med data som lagras i Azure Cosmos DB.

* Utför noll nedtidsmigreringar till ett annat Azure Cosmos-konto eller en annan Azure Cosmos-behållare med en annan logisk partitionsnyckel.

* Implementera en datanivå och arkivering på programnivå. Du kan till exempel lagra "heta data" i Azure Cosmos DB och åldras "kalla data" till andra lagringssystem som [Azure Blob Storage](../storage/common/storage-introduction.md).

När du måste [denormalisera data över partitioner och behållare](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)kan du läsa från behållarens ändringsflöde som en källa för den här datareplikeringen. Datareplikering i realtid med ändringsflödet kan bara garantera eventuell konsekvens. Du kan [övervaka hur långt ändringsflödesprocessorn släpar efter](how-to-use-change-feed-estimator.md) när det gäller att bearbeta ändringar i Cosmos-behållaren.

## <a name="event-sourcing"></a>Inköp av evenemang

[Händelseinköpsmönstret](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) innebär att du använder ett tilläggslagringslager för att registrera hela serien med åtgärder på dessa data. Azure Cosmos DB:s ändringsflöde är ett utmärkt val som ett centralt datalager i händelseinköpsarkitekturer där all datainmatning modelleras som skrivningar (inga uppdateringar eller borttagningar). I det här fallet är varje skriv till Azure Cosmos DB en "händelse" och du har en fullständig post av tidigare händelser i ändringsflödet. Typisk användning av de händelser som publiceras av den centrala händelsearkivet är för att upprätthålla materialiserade vyer eller för integration med externa system. Eftersom det inte finns någon tidsgräns för kvarhållning i ändringsflödet kan du spela upp alla tidigare händelser igen genom att läsa från början av Cosmos-behållarens ändringsflöde.

Du kan låta [flera växelflöden som konsumenter prenumererar på samma behållares ändringsflöde](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers). Bortsett från [lånebehållarens](change-feed-processor.md#components-of-the-change-feed-processor) etablerade dataflöde, finns det ingen kostnad för att använda ändringsflödet. Ändringsflödet är tillgängligt i alla behållare oavsett om den används.

Azure Cosmos DB är ett utmärkt beständigt datalager med endast bifogade tillägg i händelseinköpsmönstret på grund av dess styrkor i horisontell skalbarhet och hög tillgänglighet. Dessutom erbjuder biblioteket för ändring av feedprocessorn en ["minst en gång"-garanti,](change-feed-processor.md#error-handling) vilket säkerställer att du inte missar att bearbeta några händelser.

## <a name="current-limitations"></a>Aktuella begränsningar

Ändringsflödet har viktiga begränsningar som du bör förstå. Även om objekt i en Cosmos-behållare alltid finns kvar i ändringsflödet, är ändringsflödet inte en fullständig driftlogg. Det finns viktiga områden att tänka på när du utformar ett program som använder ändringsflödet.

### <a name="intermediate-updates"></a>Mellanliggande uppdateringar

Endast den senaste ändringen för ett visst objekt inkluderas i ändringsflödet. När du bearbetar ändringar kommer du att läsa den senaste tillgängliga artikelversionen. Om det finns flera uppdateringar av samma objekt på kort tid är det möjligt att missa bearbetning av mellanliggande uppdateringar. Om du vill spåra uppdateringar och kunna spela upp tidigare uppdateringar av ett objekt rekommenderar vi att du modellerar dessa uppdateringar som en serie skrivningar i stället.

### <a name="deletes"></a>Tar bort

Ändringsflödet tar inte bort. Om du tar bort ett objekt från behållaren tas det också bort från ändringsflödet. Den vanligaste metoden för att hantera detta är att lägga till en mjuk markör på de objekt som tas bort. Du kan lägga till en egenskap som kallas "borttagen" och ställa in den på "true" vid tidpunkten för borttagningen. Den här dokumentuppdateringen visas i ändringsflödet. Du kan ange en TTL för det här objektet så att den kan tas bort automatiskt senare.

### <a name="guaranteed-order"></a>Garanterad beställning

Det finns garanterad ordning i ändringsfeeden i ett partitionsnyckelvärde men inte över partitionsnyckelvärden. Du bör välja en partitionsnyckel som ger dig en meningsfull ordergaranti.

Tänk dig till exempel ett butiksprogram med hjälp av designmönstret för händelsekällor. I det här programmet är olika användaråtgärder varje "händelser" som modelleras som skrivningar till Azure Cosmos DB. Tänk om några exempelhändelser inträffade i följande sekvens:

1. Kunden lägger till artikel A i kundvagnen
2. Kunden lägger till punkt B i kundvagnen
3. Kunden tar bort artikel A från kundvagnen
4. Kundcheckar och kundvagnens innehåll levereras

En materialiserad vy över aktuell kundvagn innehåll upprätthålls för varje kund. Denna ansökan måste säkerställa att dessa händelser bearbetas i den ordning de inträffar. Om till exempel vagnsutcheckningen skulle bearbetas innan artikel A skulle tas bort, är det troligt att kunden skulle ha fått artikel A levererad, i motsats till önskad artikel B. För att garantera att dessa fyra händelser bearbetas i ordning efter att de förekomsteras bör de omfattas av samma partitionsnyckelvärde. Om du väljer **användarnamn** (varje kund har ett unikt användarnamn) som partitionsnyckel kan du garantera att dessa händelser visas i ändringsflödet i samma ordning som de skrivs till Azure Cosmos DB.

## <a name="examples"></a>Exempel

Här är några verkliga exempel på ändringsflödeskod som sträcker sig utanför de exempel som ingår i Microsoft-dokument:

- [Introduktion till ändringsflödet](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT-användningsfall centrerat kring ändringsflödet](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Butiksanvändningsfodralet centrerat kring ändringsflödet](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Nästa steg

* [Översikt över ändringsflöde](change-feed.md)
* [Alternativ för att läsa ändringsflöde](read-change-feed.md)
* [Använda ändringsfeed med Azure Functions](change-feed-functions.md)