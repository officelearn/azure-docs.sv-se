---
title: Vad är värd för Azure Event Hubs händelsebearbetning och varför ska jag använda den | Microsoft Docs
description: Översikt och introduktion till värd för Azure Event Hubs händelsebearbetning
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
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 4907004f4bb88cf0fe9fb799cab236ebf98bba7a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133014"
---
# <a name="azure-event-hubs-event-processor-host-overview"></a>Översikt över Azure Event Hubs värd för händelsebearbetning

Händelsehubbar i Azure är en kraftfull telemetri införandet tjänst som kan användas till strömmen miljoner händelser till låg kostnad. Den här artikeln beskriver hur du använder infogade händelser med hjälp av den *värd för händelsebearbetning* (EPH), en intelligent konsumentagent som förenklar hanteringen av kontrollpunkter leasing och parallella händelseläsare.  

Nyckeln till skala för Händelsehubbar är uppfattning om partitionerade konsumenter. Jämfört med den [konkurrerande konsumenter](http://msdn.microsoft.com/en-us/library/dn568101.aspx) mönster, partitionerat konsumentmönster aktiverar hög skala genom att ta bort konkurrens flaskhals och underlätta parallellitet för slutpunkt till slutpunkt.

## <a name="home-security-scenario"></a>Säkerhet hemma scenario

Ett exempelscenario du ett hemnätverk säkerhet för företag som övervakar hus som 100 000. Varje minut hämtar den data från olika sensorer som en rörelsedetektor, dörren/fönster öppna sensor, om break detektor, etc., installeras i varje hem. Företaget tillhandahåller en webbplats för boende att övervaka aktiviteten i hemmet i nära realtid.

Varje sensor skickar data till en händelsehubb. Händelsehubben har konfigurerats med 16 partitioner. Du behöver en metod som kan läsa dessa händelser, konsolidera dem (filter, sammanställd, etc.) och dump mängdfunktionen till en storage-blob projiceras sedan till en användarvänlig webbsida på konsumerande slutet.

## <a name="write-the-consumer-application"></a>Skriva konsument-program

När du designar konsumenten i en distribuerad miljö, hanterar scenariot följande krav:

1. **Skala:** skapa flera konsumenter med varje konsument ägarskap av läsning från några Händelsehubbar partitioner.
2. **Belastningsutjämna:** öka eller minska konsumenterna dynamiskt. Till exempel när en ny typ av sensor (till exempel en kolmonoxid detektor) läggs till varje startsida, ökar antalet händelser. I så fall ökar operatorn (en mänskliga) antalet konsumenten instanser. Sedan pool av konsumenterna kan balansera antalet partitioner som de äger att dela belastningen med de nyligen tillagda konsumenterna.
3. **Sömlös meritförteckning i fel:** om en konsument (**konsumenten A**) misslyckas (till exempel den virtuella datorn är värd konsumenten plötsligt kraschar), och andra användare måste kunna hämta de partitioner som ägs av **konsumenten A** och fortsätta. Dessutom fortsättning punkten, kallas en *kontrollpunkt* eller *offset*, bör visas på exakt samma punkt då **konsumenten A** misslyckades, eller något innan.
4. **Använda händelser:** medan föregående tre punkter handlar om hantering av konsumenten, måste det finnas en kod för att använda händelser och gör något användbart med den; exempelvis sammanställa den och överföra den till blob storage.

I stället för att skapa din egen lösning för den här Händelsehubbar ger denna funktion via den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) gränssnitt och [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) klass.

## <a name="ieventprocessor-interface"></a>IEventProcessor gränssnitt

Börja använda program implementera den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) -gränssnittet, som har fyra metoder: [OpenAsync, CloseAsync, ProcessErrorAsync och ProcessEventsAsnyc](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Det här gränssnittet innehåller faktiska koden för att använda de händelser som skickar Händelsehubbar. Följande kod visar en enkel implementering:

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

Därefter instansiera en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instans. Beroende på överbelastning, när du skapar den [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instans i konstruktorn, används följande parametrar:

- **Värdnamn:** namnet på varje konsument-instans. Varje instans av **EventProcessorHost** måste ha ett unikt värde för den här variabeln inom en konsumentgrupp så att det är bäst att inte hård code det här värdet.
- **eventHubPath:** namnet på händelsehubben.
- **consumerGroupName:** Händelsehubbar använder **$Default** som namnet på konsumentgrupp standard, men det är en bra idé att skapa en konsumentgrupp för en viss aspekt av bearbetning.
- **eventHubConnectionString:** anslutningssträngen till händelsehubben, som kan hämtas från Azure-portalen. Den här anslutningssträngen måste ha **lyssna** behörigheter på händelsehubben.
- **storageConnectionString:** storage-konto som används för hantering av interna resurser.

Slutligen användare registrerar den [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) -instans med händelsehubbtjänsten. Registrera instruerar händelsehubbtjänsten kan förvänta sig att appen konsumenten förbrukar händelser från några av dess partitioner och anropa den [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementeringskod när den skickar händelser för att använda.

### <a name="example"></a>Exempel

Anta exempelvis att det finns 5 virtuella maskiner (VMs) dedikerade till förbrukar händelser och ett enkelt konsolprogram på varje virtuell dator, som den faktiska förbrukningen fungerar. Varje konsolprogram sedan skapar en [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instansen och registrerar den med händelsehubbtjänsten.

I det här exemplet kan anta att 16 partitioner allokeras till 5 **EventProcessorHost** instanser. Vissa **EventProcessorHost** instanser kan äga några fler partitioner än andra. För varje partition som en **EventProcessorHost** instans äger, skapas en instans av den `SimpleEventProcessor` klass. Det finns därför 16 instanser av `SimpleEventProcessor` övergripande med en tilldelad till varje partition.

I följande lista sammanfattas det här exemplet:

- 16 Händelsehubbar partitioner.
- 5 virtuella datorer, 1 konsumenten app (till exempel Consumer.exe) på varje virtuell dator.
- 5 EPH instanser registrerats, 1 i varje virtuell dator av Consumer.exe.
- 16 `SimpleEventProcessor` objekt som skapas av 5 EPH-instanser.
- 1 Consumer.exe app kan innehålla 4 `SimpleEventProcessor` objekt, eftersom 1 EPH instansen kan äga 4 partitioner.

## <a name="partition-ownership-tracking"></a>Partitionen ägarskap spårning

Ägarskap för en partition på en EPH-instans (eller en konsument) spåras via Azure Storage-konto som har angetts för spårning. Du kan visualisera spårning som en enkel tabell på följande sätt. Du kan se den faktiska implementeringen genom att undersöka blobbar under lagringskonto som tillhandahållits:

| **Konsumenten gruppnamn** | **Partitions-ID** | **Värdnamn (ägare)** | **Lån (eller ägarskap) förvärvade tid** | **Offset i partitionen (kontrollpunkt)** |
| --- | --- | --- | --- | --- |
| $Standard | 0 | Konsumenten\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Standard | 1 | Konsumenten\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Standard | 2 | Konsumenten\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Standard | 15 | Konsumenten\_VM3 | 2018-04-15T01:22:56 | 976 |

Varje värd hämtar här, ägarskap för en partition för en viss varaktighet (lånetid). Om en värd misslyckas (VM stängs av), sedan lånet går ut. Andra värdar försöker hämta ägarskap för partitionen och en av värdarna lyckas. Den här processen återställs lånet på partitionen med en ny ägare. På så sätt kan bara en läsare i taget kan läsa från en given partition inom en konsumentgrupp.

## <a name="receive-messages"></a>Ta emot meddelanden

Varje anrop till [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) ger en uppsättning händelser. Det är ditt ansvar att hantera dessa händelser. Vi rekommenderar att du gör saker relativt snabbt. det vill säga göra bearbetningen som möjligt. Använd i stället konsumentgrupper. Om du behöver skriva till lagringsutrymme och vissa routning är det normalt bättre att använda två konsumentgrupper och har två [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementeringar köra separat.

Du kanske vill hålla reda på vad du har läs- och slutföra någon gång under din bearbetning. Spåra är kritiskt om du måste starta om läsning, så att du inte gå tillbaka till början av dataströmmen. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) förenklar detta spårning med hjälp av *kontrollpunkter*. En kontrollpunkt är en plats eller offset för en given partition inom en viss konsumentgrupp, vid vilken plats som du anser att du har bearbetat meddelanden. Markera en kontrollpunkt i **EventProcessorHost** görs genom att anropa den [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) -metoden i den [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) objekt. Den här åtgärden är vanligtvis utföras i den [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metod, men kan även utföras [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Kontrollpunkter

Den [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoden har två överlagringar: första, utan parametrar, kontrollpunkterna högsta händelse förskjutningen inom den samling som returneras av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Denna offset är en ”hög” vattenmärke; Det förutsätts att du har bearbetat alla nya händelser när du anropar den. Om du använder den här metoden i det här sättet kan du vara medveten om att du förväntas anropa det efter andra händelsebearbetning koden har returnerat. Andra överlagringen kan du ange en [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) instans att kontrollpunkten. Den här metoden kan du använda en annan typ av vattenstämpel att kontrollpunkten. Du kan implementera en ”lågvattenmärket” med den här vattenstämpel: lägsta sekvenserade händelsen du är säker har bearbetats. Den här överlagring tillhandahålls aktivera flexibilitet vid offset management.

När kontrollpunkten utförs en JSON-fil med partitions-specifik information (särskilt förskjutningen), skrivs till storage-konto som anges i konstruktören för att [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Den här filen uppdateras kontinuerligt. Det är viktigt att tänka på kontrollpunkter i kontexten – det vore bör inte kontrollpunkt varje meddelande. Storage-konto som används för att skapa kontrollpunkter förmodligen hanterar inte den här inläsningen, men viktigare kontrollpunkter varje händelse är indikation på ett köade meddelanden mönster som en Service Bus-kö kan vara ett bättre alternativ än en händelsehubb. Syftet med Händelsehubbar är att du får ”minst en gång” leverans i stor skala. Genom att göra din underordnade system idempotent, det är lätt att återställa från fel eller startar om som resulterar i samma händelser som tagits emot flera gånger.

## <a name="thread-safety-and-processor-instances"></a>Tråden säkerhet och processor instanser

Som standard [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) är trådsäker och fungerar på ett synkron sätt med avseende på instansen av [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). När händelser anländer för en partition [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) anropas på den **IEventProcessor** instansen för att partitionera och blockerar ytterligare anrop till **ProcessEventsAsync**för partitionen. Efterföljande meddelanden och anrop till **ProcessEventsAsync** i kö i bakgrunden eftersom meddelandet pump fortsätter att köras i bakgrunden på andra trådar. Den här tråden säkerhet slipper trådsäker samlingar och ökar kraftigt prestanda.

## <a name="shut-down-gracefully"></a>Avslutas på vanligt sätt

Slutligen [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) gör en ren avstängning av alla partition läsare och bör alltid anropas när du stänger en instans av [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Det gick inte att göra det kan orsaka fördröjningar när du startar andra instanser av **EventProcessorHost** på grund av lånet förfallodatum och epok konflikter. Hantering av epok beskrivs i detalj i detta [blogginlägget](https://blogs.msdn.microsoft.com/gyan/2014/09/02/event-hubs-receiver-epoch/)

## <a name="lease-management"></a>Hantering av partitionsleasing

Som tidigare förklarats tabellen Uppföljning förenklar Autoskala uppbyggnad [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Som en instans av **EventProcessorHost** startar den skaffar så många lån som möjligt och börjar läsa händelser. Som lån nära förfallodatum, **EventProcessorHost** försöker förnya dem genom att placera en reservation. Om lånet är tillgänglig för förnyelse, processorn fortsätter läsning, men om det inte läsaren är stängd och [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) anropas. **CloseAsync** är ett bra tillfälle att utföra alla slutliga rensning för den aktuella partitionen.

**EventProcessorHost** innehåller en [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) egenskapen. Den här egenskapen gör kontroll över hantering av partitionsleasing. Ställ in alternativen innan du registrerar din [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementering.

## <a name="control-event-processor-host-options"></a>Ange alternativ för värd för händelsebearbetning

Dessutom kan en överlagring av [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) tar en [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) objekt som parameter. Använd den här parametern för att styra beteendet för [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) sig själv. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) fyra egenskaper och en händelse:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): den maximala storleken för den samling som du vill ta emot i ett anrop av [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Den här storleken är inte minst bara den maximala storleken. Om det finns färre meddelanden ska tas emot **ProcessEventsAsync** körs med så många som var tillgängliga.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): ett värde som används av den underliggande AMQP-kanalen för att avgöra den övre gränsen för hur många meddelanden om klienten ska ta emot. Det här värdet ska vara större än eller lika med [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): om den här parametern **SANT**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) anropas när tidsgränsen uppnås för underliggande anropet för att ta emot händelser på en partition. Den här metoden är användbar för att utföra åtgärder för tidsbaserade under perioder av inaktivitet i partitionen.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): gör en pekare eller lambda funktionsuttryck anges, som kallas för att ange den första förskjutning när en läsare börjar läsa en partition. Utan att ange denna offset läsaren startar vid den äldsta händelsen om inte en JSON-fil med en förskjutning redan har sparats i storage-konto som angetts för den [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktor. Den här metoden är användbar när du vill ändra funktionssättet för läsare start. När den här metoden anropas innehåller parametern objektet partitions-ID som läsaren startas.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): gör att du kan få meddelanden om eventuella underliggande undantag som uppstår i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Om saker inte fungerar som förväntat, är den här händelsen en bra plats att börja söka.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med den värd för händelsebearbetning, se följande artiklar för att lära dig mer om Händelsehubbar:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)
* [Tillgänglighet och konsekvens i Event Hubs](event-hubs-availability-and-consistency.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Event Hubs exemplen på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)