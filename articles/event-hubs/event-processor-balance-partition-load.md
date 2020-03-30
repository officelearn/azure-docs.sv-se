---
title: Fördelningspartitionsbelastning över flera instanser – Azure Event Hubs | Microsoft-dokument
description: Beskriver hur du balanserar partitionsbelastning över flera instanser av ditt program med hjälp av en händelsebehandlare och Azure Event Hubs SDK.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: 1244fe64d0c23782fdae7a0f92415bada4bef55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907661"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Balansera partitionsbelastning över flera instanser av ditt program
Om du vill skala händelsebearbetningsprogrammet kan du köra flera instanser av programmet och låta det balansera belastningen sinsemellan. I de äldre versionerna kunde Du med [EventProcessorHost](event-hubs-event-processor-host.md) balansera belastningen mellan flera instanser av programmet och kontrollpunktshändelser när du tar emot. I de nyare versionerna (5.0 och framåt) kan du göra samma sak i **EventProcessorClient** (.NET och Java) eller **EventHubConsumerClient** (Python och JavaScript). Utvecklingsmodellen förenklas med hjälp av händelser. Du prenumererar på de händelser som du är intresserad av genom att registrera en händelsehanterare.

I den här artikeln beskrivs ett exempelscenario för att använda flera instanser för att läsa händelser från en händelsehubb och sedan ge dig information om funktioner i händelseprocessorklienten, vilket gör att du kan ta emot händelser från flera partitioner samtidigt och belastningsbalans med andra konsumenter som använder samma händelsenav och konsumentgrupp.

> [!NOTE]
> Nyckeln till skala för Event Hubs är idén om partitionerade konsumenter. I motsats till det [konkurrerande konsumentmönstret](https://msdn.microsoft.com/library/dn568101.aspx) möjliggör det avskiljade konsumentmönstret högskalan genom att undanröja flaskhalsen i påståendet och underlätta parallellitet från till.

## <a name="example-scenario"></a>Exempel på ett scenario

Som ett exempel scenario, överväga ett hem säkerhetsföretag som övervakar 100.000 hem. Varje minut får den data från olika sensorer såsom en rörelsedetektor, dörr / fönster öppen sensor, glaskross detektor, och så vidare, installeras i varje hem. Företaget tillhandahåller en webbplats för boende att övervaka verksamheten i sitt hem i nära realtid.

Varje sensor skickar data till en händelsehubb. Händelsehubben är konfigurerad med 16 partitioner. I den tidskrävande änden behöver du en mekanism som kan läsa dessa händelser, konsolidera dem (filtrera, aggregera och så vidare) och dumpa aggregatet till en lagringsblob, som sedan projiceras på en användarvänlig webbsida.

## <a name="write-the-consumer-application"></a>Skriv konsumentprogrammet

När du utformar konsumenten i en distribuerad miljö måste scenariot hantera följande krav:

1. **Skala:** Skapa flera konsumenter, där varje konsument blir ägare till läsning från några Event Hubs-partitioner.
2. **Belastningsbalans:** Öka eller minska konsumenterna dynamiskt. Till exempel, när en ny sensortyp (till exempel en kolmonoxiddetektor) läggs till i varje hem, ökar antalet händelser. I så fall ökar operatören (en människa) antalet konsumentinstanser. Sedan kan poolen av konsumenter balansera antalet partitioner de äger, att dela belastningen med de nyligen tillagda konsumenterna.
3. **Sömlös återuppta vid fel:** Om en konsument **(konsument A)** misslyckas (till exempel kraschar den virtuella datorn som är värd för konsumenten plötsligt), kan andra konsumenter plocka upp de partitioner som ägs av **konsument A** och fortsätta. Dessutom bör fortsättningspunkten, som kallas *en kontrollpunkt* eller *offset,* vara vid den exakta punkt där **konsumenten A** misslyckades, eller något före det.
4. **Förbruka händelser:** Medan de tre föregående punkterna handlar om förvaltningen av konsumenten, måste det finnas kod för att konsumera händelserna och göra något användbart med det. Till exempel aggregera den och ladda upp den till blob lagring.

## <a name="event-processor-or-consumer-client"></a>Händelsebehandlare eller konsumentklient

Du behöver inte bygga din egen lösning för att uppfylla dessa krav. Azure Event Hubs SDK:er tillhandahåller den här funktionen. I .NET- eller Java-SDK:er använder du en händelseprocessorklient (EventProcessorClient) och i Python- och Java Script-SDK:er använder du EventHubConsumerClient. I den gamla versionen av SDK var det händelseprocessorvärden (EventProcessorHost) som stödde dessa funktioner.

För de flesta produktionsscenarier rekommenderar vi att du använder händelseprocessorklienten för att läsa och bearbeta händelser. Processorklienten är avsedd att ge en robust upplevelse för bearbetning av händelser över alla partitioner i en händelsehubb på ett performant och feltolerant sätt samtidigt som det ger ett sätt att väga dess framsteg. Event processor klienter kan också arbeta i samarbete inom ramen för en konsumentgrupp för en viss händelse nav. Klienter hanterar automatiskt fördelning och balansering av arbete när instanser blir tillgängliga eller otillgängliga för gruppen.

## <a name="partition-ownership-tracking"></a>Spårning av partitionsägarskap

En händelseprocessorinstans äger och bearbetar vanligtvis händelser från en eller flera partitioner. Ägarskap för partitioner fördelas jämnt mellan alla aktiva händelseprocessorinstanser som är associerade med en händelsehubb och konsumentgruppkombination. 

Varje händelsebehandlare får en unik identifierare och gör anspråk på ägarskap för partitioner genom att lägga till eller uppdatera en post i ett kontrollpunktsarkiv. Alla händelseprocessorinstanser kommunicerar med det här arkivet med jämna mellanrum för att uppdatera sitt eget bearbetningstillstånd samt för att lära dig mer om andra aktiva instanser. Dessa data används sedan för att balansera belastningen bland de aktiva processorerna. Nya instanser kan ansluta till bearbetningspoolen för att skala upp. När instanser går ner, antingen på grund av fel eller för att skala ned, överförs partitionsägarskapet smidigt till andra aktiva processorer.

Partitionsägarposter i kontrollpunktsarkivet håller reda på händelsehubbarnamnområde, händelsehubbnamn, konsumentgrupp, händelseprocessoridentifierare (kallas även ägare), partitions-ID och den senaste ändrade tiden.



| Event Hubs-namnområde               | Namn på händelsehubb | **Konsumentgrupp** | Ägare                                | Partitions-ID | Senast ändrad tid  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Varje händelseprocessorinstans förvärvar ägarskap för en partition och börjar bearbeta partitionen från den senast kända [kontrollpunkten](# Checkpointing). Om en processor misslyckas (VM stängs av), identifierar andra instanser detta genom att titta på den senaste ändrade tiden. Andra instanser försöker få ägarskap för de partitioner som tidigare ägdes av den inaktiva instansen, och kontrollpunktsarkivet garanterar att endast en av instanserna lyckas hävda ägarskap för en partition. Så, vid en viss tidpunkt, det finns högst en processor som tar emot händelser från en partition.

## <a name="receive-messages"></a>Ta emot meddelanden

När du skapar en händelsebehandlare anger du de funktioner som ska bearbeta händelser och fel. Varje anrop till funktionen som bearbetar händelser levererar en enskild händelse från en viss partition. Det är ditt ansvar att hantera denna händelse. Om du vill vara säker på att konsumenten bearbetar varje meddelande minst en gång måste du skriva din egen kod med logik för återförsök. Men var försiktig med förgiftade meddelanden.

Vi rekommenderar att du gör saker relativt snabbt. Det vill än, gör så lite bearbetning som möjligt. Om du behöver skriva till lagring och göra en del routning är det bättre att använda två konsumentgrupper och ha två händelsebehandlare.

## <a name="checkpointing"></a>Kontrollpunkter

*Kontrollpunkt* är en process genom vilken en händelsebehandlare markerar eller begår positionen för den senast bearbetade händelsen i en partition. Markera en kontrollpunkt görs vanligtvis inom den funktion som bearbetar händelserna och sker per partition i en konsumentgrupp. 

Om en händelseprocessor kopplas från en partition kan en annan instans återuppta bearbetningen av partitionen vid kontrollpunkten som tidigare har bekräftats av den sista processorn för den partitionen i den konsumentgruppen. När processorn ansluter skickas förskjutningen till händelsehubben för att ange den plats där avläsningen ska börja. På så sätt kan du använda kontrollpunkter för att både markera händelser som "kompletta" av underordnade program och för att ge återhämtning när en händelsebehandlare går ner. Du kan återgå till äldre data genom att ange en lägre offset i den här kontrollpunktsprocessen. 

När kontrollpunkten utförs för att markera en händelse som bearbetad läggs en post i kontrollpunktsarkivet till eller uppdateras med händelsens förskjutnings- och sekvensnummer. Användarna bör bestämma hur ofta kontrollpunkten uppdateras. Uppdatering efter varje bearbetad händelse kan ha prestanda- och kostnadskonsekvenser eftersom den utlöser en skrivåtgärd till det underliggande kontrollpunktsarkivet. Kontrollpunkter varje enskild händelse är också ett tecken på ett köat meddelandemönster för vilket en Service Bus-kö kan vara ett bättre alternativ än en händelsehubb. Tanken bakom Event Hubs är att du får "minst en gång" leverans i stor skala. Genom att göra dina nedströms system idempotenta, är det lätt att återställa från fel eller omstarter som resulterar i samma händelser som tas emot flera gånger.

## <a name="thread-safety-and-processor-instances"></a>Trådsäkerhets- och processorinstanser

Som standard är händelsebehandlare eller konsument trådsäker och beter sig på ett synkront sätt. När händelser anländer för en partition anropas funktionen som bearbetar händelserna. Efterföljande meddelanden och anrop till den här funktionskön bakom kulisserna när meddelandepumpen fortsätter att köras i bakgrunden på andra trådar. Denna tråd säkerhet tar bort behovet av trådsäkra samlingar och dramatiskt ökar prestanda.

## <a name="next-steps"></a>Nästa steg
Se följande snabbstarter:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-node-send-v2.md)
