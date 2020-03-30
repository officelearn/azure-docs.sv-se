---
title: Ta emot händelser med händelsebehandlare – Azure Event Hubs | Microsoft-dokument
description: I den här artikeln beskrivs händelsebehandlarevärden i Azure Event Hubs, vilket förenklar hanteringen av kontrollpunkter, leasing och läsning av händelser parallellt.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372232"
---
# <a name="event-processor-host"></a>Värd för händelsebearbetning
> [!NOTE]
> Den här artikeln gäller den gamla versionen av Azure Event Hubs SDK. Mer information om hur du migrerar koden till den nyare versionen av SDK finns i dessa migreringsguider. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Java-skript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Se också [Skapa partitionsbelastning över flera instanser av ditt program](event-processor-balance-partition-load.md).

Azure Event Hubs är en kraftfull tjänst för inmatning av telemetri som kan användas för att strömma miljontals händelser till låg kostnad. I den här artikeln beskrivs hur du använder intövda händelser med hjälp av eph *(Event Processor Host);* en intelligent konsumentagent som förenklar hanteringen av kontrollpunkter, leasing och parallella händelseläsare.  

Nyckeln till skala för Event Hubs är idén om partitionerade konsumenter. I motsats till det [konkurrerande konsumentmönstret](https://msdn.microsoft.com/library/dn568101.aspx) möjliggör det avskiljade konsumentmönstret högskalan genom att undanröja flaskhalsen i påståendet och underlätta parallellitet från till.

## <a name="home-security-scenario"></a>Scenario för säkerhet i hemmet

Som ett exempel scenario, överväga ett hem säkerhetsföretag som övervakar 100.000 hem. Varje minut får den data från olika sensorer såsom en rörelsedetektor, dörr / fönster öppen sensor, glaskrossdetektor, etc., installeras i varje hem. Företaget tillhandahåller en webbplats för boende att övervaka verksamheten i sitt hem i nära realtid.

Varje sensor skickar data till en händelsehubb. Händelsehubben är konfigurerad med 16 partitioner. I den tidskrävande änden behöver du en mekanism som kan läsa dessa händelser, konsolidera dem (filtrera, aggregera, etc.) och dumpa aggregatet till en lagringsblob, som sedan projiceras på en användarvänlig webbsida.

## <a name="write-the-consumer-application"></a>Skriv konsumentprogrammet

När du utformar konsumenten i en distribuerad miljö måste scenariot hantera följande krav:

1. **Skala:** Skapa flera konsumenter, där varje konsument blir ägare till läsning från några Event Hubs-partitioner.
2. **Belastningsbalans:** Öka eller minska konsumenterna dynamiskt. Till exempel, när en ny sensortyp (till exempel en kolmonoxiddetektor) läggs till i varje hem, ökar antalet händelser. I så fall ökar operatören (en människa) antalet konsumentinstanser. Sedan kan poolen av konsumenter balansera antalet partitioner de äger, att dela belastningen med de nyligen tillagda konsumenterna.
3. **Sömlös återuppta vid fel:** Om en konsument **(konsument A)** misslyckas (till exempel kraschar den virtuella datorn som är värd för konsumenten plötsligt) måste andra konsumenter kunna plocka upp de partitioner som ägs av **konsument A** och fortsätta. Dessutom bör fortsättningspunkten, som kallas *en kontrollpunkt* eller *offset,* vara vid den exakta punkt där **konsumenten A** misslyckades, eller något före det.
4. **Förbruka händelser:** Medan de tre föregående punkterna handlar om förvaltningen av konsumenten, måste det finnas kod för att konsumera händelserna och göra något användbart med det; till exempel aggregera den och ladda upp den till bloblagring.

I stället för att skapa en egen lösning för detta tillhandahåller Event Hubs den här funktionen via [IEventProcessor-gränssnittet](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) och [klassen EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)

## <a name="ieventprocessor-interface"></a>IEventProcessor-gränssnitt

Först implementerar tidskrävande program [IEventProcessor-gränssnittet,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) som har fyra metoder: [OpenAsync, CloseAsync, ProcessErrorAsync och ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Det här gränssnittet innehåller den faktiska koden för att använda de händelser som Event Hubs skickar. Följande kod visar en enkel implementering:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Därefter instansierar du en [EventProcessorHost-instans.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Beroende på överbelastning, när du skapar [EventProcessorHost-instansen](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) i konstruktorn, används följande parametrar:

- **hostName:** namnet på varje konsumentinstans. Varje instans av **EventProcessorHost** måste ha ett unikt värde för den här variabeln inom en konsumentgrupp, så koda inte det här värdet hårt.
- **eventHubPath:** Namnet på händelsehubben.
- **consumerGroupName:** Event Hubs använder **$Default** som namnet på standardkonsumentgruppen, men det är en bra idé att skapa en konsumentgrupp för din specifika aspekt av bearbetningen.
- **eventHubConnectionString:** Anslutningssträngen till händelsehubben, som kan hämtas från Azure-portalen. Den här anslutningssträngen bör ha **lyssningsbehörighet** för händelsehubben.
- **lagringAnslutningssträngning:** Lagringskontot som används för intern resurshantering.

Slutligen registrerar konsumenter [EventProcessorHost-instansen](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) med tjänsten Event Hubs. Om du registrerar en händelseprocessorklass med en instans av EventProcessorHost startas händelsebearbetning. Registrering instruerar eventhubbar-tjänsten att förvänta sig att konsumentappen förbrukar händelser från vissa av sina partitioner och att anropa [IEventProcessor-implementeringskoden](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) när den skickar händelser att förbruka. 


### <a name="example"></a>Exempel

Som ett exempel, föreställa sig att det finns 5 virtuella datorer (VMs) dedikerade till att konsumera händelser, och en enkel konsol program i varje virtuell dator, som gör den faktiska förbrukningen arbete. Varje konsolprogram skapar sedan en [EventProcessorHost-instans](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) och registrerar den med tjänsten Event Hubs.

I det här exemplet ska vi säga att 16 partitioner allokeras till 5 **EventProcessorHost-instanserna.** Vissa **EventProcessorHost-instanser** kan äga några fler partitioner än andra. För varje partition som en **EventProcessorHost-instans** äger `SimpleEventProcessor` skapas en instans av klassen. Därför finns det 16 `SimpleEventProcessor` instanser av totalt, med en tilldelad till varje partition.

I följande lista sammanfattas det här exemplet:

- 16 partitioner i händelsehubbar.
- 5 virtuella datorer, 1 konsumentapp (till exempel Consumer.exe) i varje virtuell dator.
- 5 EPH-instanser registrerade, 1 i varje virtuell dator av Consumer.exe.
- 16 `SimpleEventProcessor` objekt som skapats av 5 EPH-instanser.
- 1 Consumer.exe-appen kan `SimpleEventProcessor` innehålla 4 objekt, eftersom 1 EPH-instansen kan äga 4 partitioner.

## <a name="partition-ownership-tracking"></a>Spårning av partitionsägarskap

Ägarskap av en partition till en EPH-instans (eller en konsument) spåras via Azure Storage-kontot som tillhandahålls för spårning. Du kan visualisera spårningen som en enkel tabell enligt följande. Du kan se den faktiska implementeringen genom att undersöka blobbar under lagringskontot som tillhandahålls:

| **Konsumentgruppens namn** | **Partitions-ID** | **Värdnamn (ägare)** | **Leasing (eller ägande) förvärvad tid** | **Förskjutning i partition (kontrollpunkt)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | VM3 för konsumenter\_ | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Vm4 för konsumenter\_ | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Vm0 för konsumenter\_ | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | VM3 för konsumenter\_ | 2018-04-15T01:22:56 | 976 |

Här förvärvar varje värd ägarskap för en partition under en viss tid (lånetiden). Om en värd misslyckas (VM stängs av) upphör lånet att gälla. Andra värdar försöker få äganderätten till partitionen, och en av värdarna lyckas. Den här processen återställer lånet på partitionen med en ny ägare. På så sätt kan bara en enda läsare i taget läsa från en viss partition inom en konsumentgrupp.

## <a name="receive-messages"></a>Ta emot meddelanden

Varje anrop till [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) levererar en samling händelser. Det är ditt ansvar att hantera dessa händelser. Om du vill vara säker på att processorvärden bearbetar varje meddelande minst en gång måste du skriva din egen igenförsökande kod. Men var försiktig med förgiftade meddelanden.

Det rekommenderas att du gör saker relativt snabbt; det vill än, göra så lite bearbetning som möjligt. Använd i stället konsumentgrupper. Om du behöver skriva till lagring och göra en del routning är det bättre att använda två konsumentgrupper och har två [IEventProcessor-implementeringar](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) som körs separat.

Vid något tillfälle under bearbetningen kanske du vill hålla reda på vad du har läst och slutfört. Att hålla reda på är viktigt om du måste starta om läsningen, så att du inte återvänder till början av strömmen. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) förenklar spårningen med hjälp av *kontrollpunkter*. En kontrollpunkt är en plats, eller förskjutning, för en viss partition, inom en viss konsumentgrupp, då du är övertygad om att du har bearbetat meddelandena. Markera en kontrollpunkt i **EventProcessorHost** uppnås genom att anropa [metoden CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) på [PartitionContext-objektet.](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) Den här åtgärden görs inom [metoden ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) men kan även göras i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Kontrollpunkter

[CheckpointAsync-metoden](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) har två överbelastningar: den första, utan parametrar, kontrollpunkter till den högsta händelseförskjutningen i samlingen som returneras av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Denna offset är ett "högt vatten" märke; Det förutsätter att du har bearbetat alla senaste händelser när du ringer den. Om du använder den här metoden på det här sättet bör du vara medveten om att du förväntas anropa den när din andra händelsebearbetningskod har returnerats. Med den andra överbelastningen kan du ange en [EventData-instans](/dotnet/api/microsoft.azure.eventhubs.eventdata) som kontrollpunkt. Med den här metoden kan du använda en annan typ av vattenstämpel till kontrollpunkt. Med den här vattenstämpeln kan du implementera ett "lågt vatten"-märke: den lägsta sekvenserade händelsen som du är säker på har bearbetats. Denna överbelastning tillhandahålls för att möjliggöra flexibilitet i offsethantering.

När kontrollpunkten utförs skrivs en JSON-fil med partitionsspecifik information (särskilt förskjutningen) till lagringskontot som anges i konstruktorn till [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Filen uppdateras kontinuerligt. Det är viktigt att överväga kontrollpunkter i sitt sammanhang - det skulle vara oklokt att väga varje meddelande. Lagringskontot som används för kontrollpunkter skulle förmodligen inte hantera den här belastningen, men ännu viktigare är kontrollpunkter varje enskild händelse som är ett tecken på ett kömeddelandemönster för vilket en Service Bus-kö kan vara ett bättre alternativ än en händelsehubb. Tanken bakom Event Hubs är att du får "minst en gång" leverans i stor skala. Genom att göra dina nedströms system idempotenta, är det lätt att återställa från fel eller omstarter som resulterar i samma händelser som tas emot flera gånger.

## <a name="thread-safety-and-processor-instances"></a>Trådsäkerhets- och processorinstanser

Som standard är [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) trådsäkert och fungerar på ett synkront sätt med avseende på förekomsten av [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). När händelser anländer för en partition [anropas ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) på **IEventProcessor-instansen** för den partitionen och blockerar ytterligare anrop till **ProcessEventsAsync** för partitionen. Efterföljande meddelanden och anrop till **ProcessEventsAsync** kö upp bakom kulisserna som meddelandepumpen fortsätter att köras i bakgrunden på andra trådar. Denna tråd säkerhet tar bort behovet av trådsäkra samlingar och dramatiskt ökar prestanda.

## <a name="shut-down-gracefully"></a>Stäng av graciöst

Slutligen aktiverar [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) en ren avstängning av alla partitionsläsare och bör alltid anropas när en instans av [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)stängs av . Om du inte gör det kan det orsaka fördröjningar när andra instanser av **EventProcessorHost** startas på grund av att lånet upphör att gälla och Epoch-konflikter. Epokhantering behandlas i detalj i avsnittet [Epok](#epoch) i artikeln. 

## <a name="lease-management"></a>Leasinghantering
Om du registrerar en händelseprocessorklass med en instans av EventProcessorHost startas händelsebearbetning. Värdinstansen erhåller lån på vissa partitioner i Event Hub, eventuellt ta tag i några från andra värdinstanser, på ett sätt som konvergerar på en jämn fördelning av partitioner över alla värdinstanser. För varje leasad partition skapar värdinstansen en instans av den angivna händelseprocessorklassen, tar sedan emot händelser från den partitionen och skickar dem till händelseprocessorinstansen. När fler instanser läggs till och fler leasingavtal grips, balanserar EventProcessorHost så småningom belastningen bland alla konsumenter.

Som tidigare förklarats förenklar spårningstabellen i hög grad den automatiska skalningskaraktären för [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). När en instans av **EventProcessorHost** startar, förvärvar den så många lån som möjligt och börjar läsa händelser. Som lån nära förfallodatum, **EventProcessorHost** försöker förnya dem genom att placera en reservation. Om lånet är tillgängligt för förnyelse fortsätter processorn att läsa, men om det inte är det stängs läsaren och [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) anropas. **CloseAsync** är ett bra tillfälle att utföra en slutlig rensning för den partitionen.

**EventProcessorHost** innehåller [egenskapen PartitionManagerOptions.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) Den här egenskapen möjliggör kontroll över lånehantering. Ange dessa alternativ innan du registrerar implementeringen av [IEventProcessor.](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

## <a name="control-event-processor-host-options"></a>Alternativ för kontrollhändelsebehandlare

Dessutom tar en överbelastning av [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) ett [EventProcessorOptions-objekt](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) som en parameter. Använd den här parametern för att styra beteendet för [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) själv. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definierar fyra egenskaper och en händelse:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): Den maximala storleken på den samling som du vill ta emot i en anrop av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Denna storlek är inte den minsta, bara den maximala storleken. Om det finns färre meddelanden som ska tas emot körs **ProcessEventsAsync** med så många som var tillgängliga.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Ett värde som används av den underliggande AMQP-kanalen för att bestämma den övre gränsen för hur många meddelanden klienten ska ta emot. Det här värdet bör vara större än eller lika med [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Om den här parametern är **true** [anropas ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) när det underliggande anropet för att ta emot händelser på en partition timeout. Den här metoden är användbar för att vidta tidsbaserade åtgärder under perioder av inaktivitet på partitionen.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Aktiverar en funktionspekare eller lambda-uttryck som ska ställas in, vilket anropas för att ge den första förskjutningen när en läsare börjar läsa en partition. Utan att ange den här förskjutningen börjar läsaren vid den äldsta händelsen, såvida inte en JSON-fil med en förskjutning redan har sparats i lagringskontot som levereras till [EventProcessorHost-konstruktorn.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Den här metoden är användbar när du vill ändra beteendet för läsarens start. När den här metoden anropas innehåller objektparametern det partitions-ID som läsaren startas för.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Gör att du kan få meddelanden om eventuella underliggande undantag som inträffar i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Om saker och ting inte fungerar som du förväntar dig, är denna händelse ett bra ställe att börja leta.

## <a name="epoch"></a>Epok

Här är hur ta emot epok fungerar:

### <a name="with-epoch"></a>Med epok
Epok är en unik identifierare (epokvärde) som tjänsten använder för att framtvinga ägandet av partitioner/leasing. Du skapar en epokbaserad mottagare med metoden [CreateEpochReceiver.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) Den här metoden skapar en epochbaserad mottagare. Mottagaren skapas för en specifik händelsehubbpartition från den angivna konsumentgruppen.

Epokfunktionen ger användarna möjlighet att se till att det bara finns en mottagare på en konsumentgrupp när som helst, med följande regler:

- Om det inte finns någon befintlig mottagare i en konsumentgrupp kan användaren skapa en mottagare med valfritt epokvärde.
- Om det finns en mottagare med ett epokvärde e1 och en ny mottagare skapas med ett epokvärde e2 där e1 <= e2, kopplas mottagaren med E1 från automatiskt, mottagaren med E2 skapas.
- Om det finns en mottagare med ett epokvärde e1 och en ny mottagare skapas med ett epokvärde e2 där E1 > e2, då skapa e2 med fel med felet: En mottagare med epok e1 redan finns.

### <a name="no-epoch"></a>Ingen epok
Du skapar en icke-epochbaserad mottagare med metoden [CreateReceiver.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) 

Det finns vissa scenarier i dataflödesbearbetning där användare vill skapa flera mottagare på en enda konsumentgrupp. För att stödja sådana scenarier har vi möjlighet att skapa en mottagare utan epok och i det här fallet tillåter vi upp till 5 samtidiga mottagare på konsumentgruppen.

### <a name="mixed-mode"></a>Blandat läge
Vi rekommenderar inte programanvändning där du skapar en mottagare med epok och sedan växlar till no-epoch eller vice versa på samma konsumentgrupp. Men när detta inträffar hanterar tjänsten den med hjälp av följande regler:

- Om det redan finns en mottagare som redan har skapats med epok E1 och aktivt tar emot händelser och en ny mottagare skapas utan epok, misslyckas skapandet av ny mottagare. Epokmottagare har alltid företräde i systemet.
- Om det fanns en mottagare som redan skapats med epok E1 och fick kopplas bort, och en ny mottagare skapas utan epok på en ny MessagingFactory, kommer skapandet av ny mottagare lyckas. Det finns en varning här att vårt system kommer att upptäcka "mottagare frånkoppling" efter ~ 10 minuter.
- Om det finns en eller flera mottagare som skapats utan epok, och en ny mottagare skapas med epok e1, kopplas alla gamla mottagare bort.


> [!NOTE]
> Vi rekommenderar att du använder olika konsumentgrupper för program som använder epoker och för dem som inte använder epoker för att undvika fel. 


## <a name="next-steps"></a>Nästa steg

Nu när du känner till värden för händelsebehandlare läser du följande artiklar om du vill veta mer om händelsehubbar:

- Kom igång med händelsehubbar
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempel på eventhubbar på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
