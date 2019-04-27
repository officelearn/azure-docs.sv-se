---
title: Ta emot händelser med hjälp av Event Processor Host - Azure Event Hubs | Microsoft Docs
description: Den här artikeln beskriver värden för händelsebearbetning i Azure Event Hubs, vilket förenklar hanteringen av kontrollpunkter, leasing och läsa händelser med parallellt.
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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 26f0abb48ba268f79167ed5d00e4f96d8b5e5998
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821876"
---
# <a name="receive-events-from-azure-event-hubs-using-event-processor-host"></a>Ta emot händelser från Azure Event Hubs med värden för händelsebearbetning

Azure Event Hubs är en kraftfull telemetriintegreringstjänst som kan användas för att strömma miljontals händelser till en låg kostnad. Den här artikeln beskriver hur du använder insamlade händelser med hjälp av den *Eventprocessorhost* (EPH); en intelligent konsumentagent som förenklar hanteringen av kontrollpunkter, leasing och händelseläsare för parallell.  

Nyckeln kan skalas för Event Hubs är tanken med partitionerade konsumenter. Jämfört med den [konkurrerande konsumenter](https://msdn.microsoft.com/library/dn568101.aspx) mönstret partitionerat konsumentmönster möjliggör hög skala genom att ta bort flaskhalsen konkurrens och underlätta från slutpunkt till slutpunkt parallellitet.

## <a name="home-security-scenario"></a>Säkerhet hemma scenario

Ett exempelscenario bör du ett hemnätverk security-företag som övervakar 100 000 hemmet. Varje minut hämtar den data från olika sensorer en rörelseidentifiering, dörren/fönster öppna sensor, glas break detektor osv., installeras i varje hem. Företaget tillhandahåller en webbplats för boende övervakar aktiviteten hos sitt hem i nära realtid.

Varje sensor skickar data till en händelsehubb. Händelsehubben har konfigurerats med 16 partitioner. Du behöver en mekanism som kan läsa dessa händelser, konsolidera dem (filter, aggregera, etc.) och dumpa samlingen till en storage-blob som är sedan planerat till en sida som användarvänliga konsumerande End.

## <a name="write-the-consumer-application"></a>Skriva konsument-program

När du designar konsumenten i en distribuerad miljö, måste scenariot hantera följande krav:

1. **Skala:** Skapa flera konsumenter med varje konsument ägarskap av läsning från några Event Hubs-partitioner.
2. **Belastningsutjämna:** Öka eller minska konsumenterna dynamiskt. Till exempel när en ny typ av sensor (till exempel en kolmonoxid detektor) läggs till varje hem, ökar antalet händelser. I så fall ökar operatorn (en människa) antalet instanser av konsumenten. Sedan, pool med konsumenter kan balansera om antalet partitioner som de äger, dela belastningen med de nyligen tillagda konsumenterna.
3. **Sömlös återupptagande för fel:** Om en konsument (**konsument A**) misslyckas (till exempel den virtuella datorn som är värd konsumenten plötsligt kraschar), och andra användare måste kunna hämta de partitioner som ägs av **konsument A** och fortsätta. Dessutom fortsättning punkten, kallas en *kontrollpunkt* eller *offset*, ska vara den exakta tidpunkt då **konsument A** misslyckades, eller något innan dess.
4. **Konsumera händelser:** Även om föregående tre punkter handlar om hanteringen av konsumenten, måste det finnas en kod för att använda händelser och göra ett beskrivande där. till exempel sammanställa den och överför den till blob storage.

I stället för att skapa en egen lösning för det här Event Hubs tillhandahåller den här funktionen via den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) gränssnitt och [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) klass.

## <a name="ieventprocessor-interface"></a>Gränssnittet IEventProcessor

Först använder program implementera den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) gränssnitt, vilket har fyra metoder: [OpenAsync CloseAsync, ProcessErrorAsync- och ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Det här gränssnittet innehåller den faktiska kod för att använda de händelser som skickas av Event Hubs. Följande kod visar en enkel implementering:

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

Därefter skapa en instans av en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instans. Beroende på överbelastning, när du skapar den [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instans i konstruktorn används följande parametrar:

- **Värdnamn:** namnet på varje konsumentinstans. Varje instans av **EventProcessorHost** måste ha ett unikt värde för den här variabeln inom en konsumentgrupp, så att inte hårt code det här värdet.
- **eventHubPath:** Namnet på händelsehubben.
- **consumerGroupName:** Händelsehubbar använder **$Default** eftersom namnet på förinställd konsumentgrupp, men det är en bra idé att skapa en konsumentgrupp för din specifika aspekter av bearbetning.
- **eventHubConnectionString:** Anslutningssträngen till event hub, som kan hämtas från Azure-portalen. Den här anslutningssträngen måste ha **lyssna** behörigheter i event hub.
- **storageConnectionString:** Det lagringskonto som används för interna resurshantering.

Slutligen användare registrerar den [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -instansen med Event Hubs-tjänsten. Registrera en händelseklass-processor med en instans av EventProcessorHost startar bearbetning av händelser. Registrera instruerar Event Hubs-tjänsten kan förvänta sig att appen konsument förbrukar händelser från vissa av tabellens partitioner, och att anropa den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementeringskod när den skickar händelser för att använda. 


### <a name="example"></a>Exempel

Anta exempelvis att det inte finns 5 virtuella datorer (VM) dedikerade för att förbruka händelser och ett enkelt konsolprogram på varje virtuell dator, som den faktiska förbrukningen fungerar. Varje konsolprogram sedan skapar en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instans och registreras med Event Hubs-tjänsten.

I det här exemplet, kan anta att 16 partitioner allokeras till 5 **EventProcessorHost** instanser. Vissa **EventProcessorHost** instanser kan äga några fler partitioner än andra. För varje partition som en **EventProcessorHost** instans äger, skapas en instans av den `SimpleEventProcessor` klass. Det finns därför 16 instanser av `SimpleEventProcessor` övergripande med en tilldelad till varje partition.

I följande lista sammanfattas i det här exemplet:

- 16 partitioner i event Hubs.
- 5 virtuella datorer, 1 konsument app (till exempel Consumer.exe) på varje virtuell dator.
- 5 EPH instanser registrerad 1 på varje virtuell dator av Consumer.exe.
- 16 `SimpleEventProcessor` objekt som skapas av 5 EPH-instanser.
- 1 Consumer.exe app kan innehålla 4 `SimpleEventProcessor` objekt, eftersom 1 EPH-instans kan äga 4 partitioner.

## <a name="partition-ownership-tracking"></a>Partition ägarskap spårning

Ägarskap för en partition till en EPH-instans (eller en konsument) spåras via Azure Storage-konto som har angetts för spårning. Du kan visualisera spårning som en enkel tabell på följande sätt. Du kan se den faktiska implementeringen genom att undersöka blobar under Storage-konto som tillhandahålls:

| **Konsumenten gruppnamn** | **Partitions-ID** | **Värdnamn (ägare)** | **Lån (eller ägare) som har köpt tid** | **Offset i partitionen (kontrollpunkt)** |
| --- | --- | --- | --- | --- |
| $Standard | 0 | Konsumenten\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Standard | 1 | Konsumenten\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Standard | 2 | Konsumenten\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Standard | 15 | Konsumenten\_VM3 | 2018-04-15T01:22:56 | 976 |

Varje värd skaffar här ägarskapet för en partition för en viss varaktighet (lånetid). Om en värd misslyckas (virtuell dator stängs av), sedan lånet upphör att gälla. Andra värdar försöker hämta ägarskap för partitionen och en av värdarna lyckas. Den här processen återställer lånet på partitionen med en ny ägare. På så sätt kan bara en läsare i taget kan läsa från en given partition inom en konsumentgrupp.

## <a name="receive-messages"></a>Ta emot meddelanden

Varje anrop till [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) levererar en insamling av händelser. Det är ditt ansvar att hantera dessa händelser. Om du vill kontrollera värden för händelsebearbetning bearbetar alla meddelanden minst en gång som du behöver skriva din egen keep försöker kod. Men var försiktig om förgiftat meddelanden.

Vi rekommenderar att du gör saker som är relativt snabbt. det vill säga göra bearbetningen som möjligt. Använd istället konsumentgrupper. Om du behöver skriva till lagring och vissa routning, är det bättre att använda två konsumentgrupper och har två [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementeringar som kör separat.

Du kanske vill hålla reda på vad du har läst och slutfört någon gång under bearbetningen. Spåra är kritiskt om du måste starta om läsning, så att du inte gå tillbaka till början av strömmen. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) förenklar den här spårning med hjälp av *kontrollpunkter*. En kontrollpunkt är en plats eller offset för en given partition inom en viss konsumentgrupp, vid vilken tidpunkt som du är nöjd som du har bearbetat meddelandena. Markera en kontrollpunkt i **EventProcessorHost** åstadkoms genom att anropa den [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoden på den [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objekt. Den här åtgärden görs i den [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metoden men kan också göras [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Kontrollpunkter

Den [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoden har två överlagringar: första, utan parametrar, kontrollpunkter till högsta händelse förskjutning i samlingen som returneras av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Denna offset är en ”hög” vattenmärke; Det förutsätter att du har bearbetat alla de senaste händelserna när du anropar den. Om du använder den här metoden i det här sättet, Tänk på att du förväntas anropar det när andra händelsebearbetning koden har returnerats. Andra överlagringen kan du ange en [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) instans till kontrollpunkt. Den här metoden kan du använda en annan typ av vattenstämpel till kontrollpunkt. Du kan implementera en ”lågt vattenmärke” med den här vattenstämpel: lägsta sekvenserade händelsen du är säker på att har bearbetats. Den här överlagring är tillgänglig för att flexibilitet vid förskjutningen management.

När kontrollpunkten utförs en JSON-fil med partitions-specifik information (mer specifikt förskjutningen) skrivs till storage-konto som anges i konstruktorn för [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Den här filen uppdateras kontinuerligt. Det är viktigt att tänka på kontrollpunkter i kontexten – det vore bör inte kontrollpunkt alla meddelanden. Storage-kontot som används för kontrollpunkter förmodligen hanterar inte den här belastningen, men viktigast av allt kontrollpunkter varje händelse är en indikation på en köade meddelandemönster som Service Bus-kön kan vara ett bättre alternativ än en event hub. Tanken bakom Event Hubs är att du får ”minst en gång” leverans i stor skala. Genom att göra din underordnade system idempotenta, det är enkelt att återställa från fel eller startar om som resulterar i samma händelser tas emot flera gånger.

## <a name="thread-safety-and-processor-instances"></a>Tråd-instanser för säkerhet och processor

Som standard [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) är tråd säkert och fungerar på ett synkron sätt med avseende på instansen av [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). När händelser tas emot för en partition [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) anropas på den **IEventProcessor** instans för att partitionera och blockerar ytterligare anrop till **ProcessEventsAsync**för partitionen. Efterföljande meddelanden och anrop till **ProcessEventsAsync** kö i bakgrunden när meddelandet pump fortsätter att köras i bakgrunden på andra trådar. Den här tråden säkerhet eliminerar behovet av trådsäker samlingar och ökar dramatiskt prestanda.

## <a name="shut-down-gracefully"></a>Stäng av ett smidigt sätt

Slutligen [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) möjliggör en ren avstängning av alla läsare i partitionen och ska alltid anropas när du stänger en instans av [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Det gick inte att göra det kan ta längre tid att starta andra instanser av **EventProcessorHost** på grund av lånets förfallotid och Epoch konflikter. Hantering av epoch beskrivs i detalj i de [Epoch](#epoch) i artikeln. 

## <a name="lease-management"></a>Hantering av partitionsleasing
Registrera en händelseklass-processor med en instans av EventProcessorHost startar bearbetning av händelser. Värdinstans hämtar lån på vissa partitioner i Event Hub grabbing eventuellt vissa från andra ha instanser på ett sätt som konvergerar på en jämn fördelning av partitioner i alla värdinstanser. För varje utlånat partition värdinstans skapar en instans av klassen angivna event processor, sedan tar emot händelser från partitionen och skickar dem till event processor-instans. När fler instanser läggs och fler leasingar är gripit balanserar EventProcessorHost så småningom belastningen mellan alla konsumenter.

Som tidigare förklarats tabellen Uppföljning förenklar den Autoskala natur [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Som en instans av **EventProcessorHost** startar den skaffar så många lån som möjligt och börjar läsa händelser. Som lån snart går ut, **EventProcessorHost** försöker förnya dem genom att placera en reservation. Om lånet är tillgänglig för förnyelse, processorn fortsätter att läsa, men om den inte läsaren stängs och [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) anropas. **CloseAsync** är ett bra tillfälle att utföra de slutliga rensningar för partitionen.

**EventProcessorHost** innehåller en [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) egenskapen. Den här egenskapen gör kontroll över hantering av partitionsleasing. Ställ in alternativen innan du registrerar din [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementering.

## <a name="control-event-processor-host-options"></a>Ange alternativ för värd för händelsebearbetning

Dessutom kan en överlagring för [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) tar en [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objektet som en parameter. Använd den här parametern för att styra beteendet för [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) själva. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) fyra egenskaper och en händelse:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): Den maximala storleken för den samling som du vill ta emot i en körning av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Den här storleken är inte minst, endast den maximala storleken. Om det finns färre meddelanden som tas emot **ProcessEventsAsync** utför med så många som var tillgängliga.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Ett värde som används av den underliggande AMQP-kanalen för att fastställa den övre gränsen för hur många meddelanden som klienten ska använda. Det här värdet ska vara större än eller lika med [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Om den här parametern **SANT**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) anropas när tidsgränsen uppnås för underliggande anropet för att ta emot händelser på en partition. Den här metoden är användbar för att utföra åtgärder för tidsbaserade perioder av inaktivitet på partitionen.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Gör en pekare eller lambda funktionsuttryck anges, som kallas för att tillhandahålla den första förskjutningen när en läsare börjar läsa en partition. Utan att ange denna offset läsaren börjar vid den äldsta händelsen, såvida inte en JSON-fil med en förskjutning redan har sparats i storage-kontot som angetts för den [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktor. Den här metoden är användbar när du vill ändra beteendet för läsare-start. När den här metoden har anropats innehåller parametern objektet partitions-ID som läsaren startas.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Gör det möjligt att få meddelanden om alla underliggande undantag som uppstår i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Om allt inte fungerar som förväntat, är ett bra ställe att börja titta i den här händelsen.

## <a name="epoch"></a>Epoch

Så fungerar här receive epoch:

### <a name="with-epoch"></a>Med Epoch
Epoch är en unik identifierare (epoch värde) som tjänsten använder framtvinga ägarskap partitionen/lånet. Du skapar en Epoch-baserade mottagare med hjälp av den [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metod. Den här metoden skapar en Epoch-baserade mottagare. Mottagaren har skapats för en specifik händelse hub partition från den angivna konsumentgruppen.

Funktionen epoch ger användarna möjlighet att se till att det finns bara en mottagare på en konsumentgrupp när som helst i tid, med följande regler:

- Om det finns ingen befintlig mottagare i en konsumentgrupp, men användaren kan skapa en mottagare med ett epoch-värde.
- Om det finns en mottagare med en epok värdet e1 och en ny mottagare har skapats med en epok värdet e2 där e1 < = e2, kommer att kopplas från mottagaren med e1 automatiskt, mottagare med e2 har skapats.
- Om det finns en mottagare med en epok värdet e1 och en ny mottagare har skapats med en epok värdet e2 där e1 > e2 och sedan skapa e2 med misslyckas med fel: Det finns redan en mottagare med epoch e1.

### <a name="no-epoch"></a>Inga Epoch
Du skapar en icke-Epoch-baserade mottagare med hjälp av den [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metod. 

Det finns vissa scenarier i strömbearbetning där användare skulle vilja skapa flera olika mottagare på en enskild konsument-grupp. Vi har möjlighet att skapa en mottagare utan epoch för att stödja sådana scenarier, och i så fall tillåter vi upp till 5 samtidiga mottagare på konsumentgruppen.

### <a name="mixed-mode"></a>Blandat läge
Vi rekommenderar inte programanvändning där du skapar en mottagare med epoch och sedan växla till Nej epoch eller vice versa på samma konsumentgruppen. Men när detta inträffar kan hanteras tjänsten den med hjälp av följande regler:

- Om det finns en mottagare som redan har skapats med epoch e1 och aktivt tar emot händelser och en ny mottagare har skapats med inga epoch, misslyckas skapandet av nya mottagare. Epoch mottagare har alltid företräde i systemet.
- Om det var en mottagare som redan har skapats med epoch e1 och fick frånkopplad och en ny mottagare har skapats med inga epoch på en ny MessagingFactory, lyckas skapandet av nya mottagare. Det finns ett villkor här att vårt system identifierar ”mottagare frånkoppling” efter ~ 10 minuter.
- Om det finns en eller flera mottagare som skapats med inga epoch och en ny mottagare har skapats med epoch e1, gamla mottagarna få kopplas från.


## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med värden för händelsebearbetning, se följande artiklar för att lära dig mer om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Event Hubs-exempel på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
