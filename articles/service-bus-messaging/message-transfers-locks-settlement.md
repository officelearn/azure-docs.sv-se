---
title: "Azure Service Bus-överföringar av meddelanden, lås och betalning | Microsoft Docs"
description: "Översikt över överföringar av Service Bus-meddelanden och betalning åtgärder"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 4789da3c84d52b2615bf4250a36093a74154e1d4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="message-transfers-locks-and-settlement"></a>Överföringar av meddelanden, lås och betalning

Central möjligheterna för förhandlare meddelande som Service Bus är att ta emot meddelanden i en kö eller ett ämne och hålla dem tillgängliga för hämtning av senare. *Skicka* är term som används ofta för överföring av ett meddelande i Service broker meddelandet. *Ta emot* är termen används ofta för överföring av ett meddelande till en klient som hämtades.

När en klient skickar ett meddelande, vill det vanligtvis veta om meddelandet korrekt har överförts till och accepteras av Service broker eller om någon form av fel uppstod. Den här positiv eller negativ bekräftelse reglerar klienten och Service broker förstå om tillståndet för överföringen av meddelandet och därför kallas *betalning*.

På samma sätt när Service broker Överför ett meddelande till en klient, broker och klienten vill upprätta en förståelse av om meddelandet har bearbetats och kan därför tas bort, eller om meddelandeleverans eller bearbetningen misslyckades, och därmed den meddelandet kan behöva levereras igen.

## <a name="settling-send-operations"></a>Reglera skicka åtgärder

Med hjälp av Service Bus-API-klienter som stöds, skicka åtgärder i Service Bus alltid uttryckligen är regleras, vilket innebär att API-åtgärd väntar på att ett godkännande resultat från Service Bus tas emot, och slutför sedan send-åtgärden.

Om meddelandet har avvisats av Service Bus, innehåller avslaget en felindikator och text med en ”spårnings-id” inuti den. Avslaget innehåller även information om huruvida åtgärden kan göras med alla förväntan att lyckas. I klienten informationen omvandlas till ett undantag och upphöjt till anroparen av send-åtgärden. Om meddelandet har accepterats åtgärden har slutförts tyst.

När du använder AMQP-protokollet, vilken är exklusiv protokoll för standardklient för .NET och Java-klienten och [som är ett alternativ för .NET Framework-klienten](service-bus-amqp-dotnet.md), överföringar av meddelanden och kvittningar pipeline och helt asynkront, och det rekommenderas att du använder asynkron programmering modellen API varianter.

En avsändare kan du publicera flera meddelanden på kabeln i snabb följd utan att behöva vänta på att varje meddelande som ska bekräftas som annars skulle vara fallet med protokollet SBMP eller HTTP 1.1. Dessa åtgärder för asynkron sändning Slutför som respektive meddelanden accepteras och lagras på partitionerade enheter eller när skicka åtgärden till olika enheter överlappar varandra. Slutföranden kan även inträffa utanför den ursprungliga Skicka ordern.

Strategi för hantering av resultatet av send-åtgärder kan ha omedelbar och avsevärd prestandapåverkan för ditt program. Exemplen i det här avsnittet är skrivna i C# och tillämpa equivalently för Java Futures.

Om programmet ger belastning av meddelanden, illustrationen med en vanlig loop och var i väntan på slutförande för var och en skicka-åtgärden innan du skickar nästa meddelande, synkron eller asynkron API-former både 10 meddelanden skickas endast slutförs efter 10 sekventiella fullständig sändningar för betalning.

Med en antagen 70 millisekund TCP roundtrip latens avståndet från en lokal plats till Service Bus och ge bara 10 ms för Service Bus för att acceptera och lagra varje meddelande tar loopen minst 8 sekunder och räknas inte nyttolast överföringstid eller hitta potentiella väg överbelastning effekter:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Om programmet startar 10 asynkron sändning åtgärder i direkt följd och väntar sedan sina respektive slutförande separat, överlappar tur och RETUR-tid för dessa 10 send-åtgärder. 10 meddelanden överförs i direkt följd som potentiellt även dela TCP ramar och varaktighet för överföring beror till stor del på nätverksrelaterade tiden det tar för att få meddelanden som överförts till Service broker.

Gör samma antaganden som tidigare loopen, kan den totala överlappande körningstiden för loopen förblir väl under en sekund:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

Det är viktigt att Observera att alla asynkrona programmeringsmodeller använda någon form av minnesbaserade, dold kön som innehåller väntande åtgärder. När [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) eller **skicka** (Java) return send-aktiviteten är i kö i arbetskön men protokollet gest påbörjas endast när det är aktiverat för aktiviteten ska köras. För kod som tenderar att push-belastning av meddelanden och där tillförlitlighet är viktigt, bör vara försiktig att inte för många meddelanden placeras ”som rör sig” på en gång, eftersom alla skickade meddelanden tar upp minnesutrymme förrän de formellt har införts på kabeln.

Semaforer, är enligt följande kodavsnitt i C#, synkronisering aktiverar dessa på programnivå som begränsning vid behov. Den här användningen av en semafor tillåter högst 10 meddelanden som rör sig på samma gång. En av 10 tillgängliga semafor Lås vidtas innan skicka och släpps som skicka är klar. 11 passerar loop väntar tills minst en av föregående skickar har slutförts och sedan gör dess Lås:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

Program ska **aldrig** initiera en asynkron send-åtgärden på ett sätt som ”brand och glömmer” utan att hämta resultatet av åtgärden. Då kan läsa in interna och osynliga uppgiften kön upp till minnet är förbrukat och programmet att identifiera skicka fel:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Med en låg nivå AMQP klienten accepterar Service Bus också ”före reglerade” överföringar. En före reglerade överföring är en fire-och glömmer åtgärd som utfall, oavsett hur inte har rapporterats tillbaka till klienten och meddelandet anses regleras när de skickas. Bristen på feedback till klienten innebär också att det finns ingen tillämplig data för diagnostik, vilket innebär att det här läget inte uppfyller kraven för att få hjälp via Azure-supporten.

## <a name="settling-receive-operations"></a>Reglera får åtgärder

För att få operations, Service Bus-API-klienter aktivera två olika lägen för explicit: *ta emot och ta bort* och *titt Lås*.

Den [ta emot och ta bort](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge talar om att tänka på alla meddelanden som skickas till den mottagande klienten som reglerade när Service broker skickas. Det innebär att meddelandet betraktas som används som Service broker har placerats på kabeln. Meddelandet går förlorad om meddelandeöverföringen misslyckas.

Upp-och i det här läget är att mottagaren behöver inte vidta ytterligare åtgärder för meddelandet och även inte långsammare genom att vänta på resultatet av lösande. Om data i enskilda meddelanden lågt värde och/eller är bara meningsfulla för mycket kort tid, är ett rimligt val i det här läget.

Den [titt Lås](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge anger Service broker att den mottagande klienten vill reglera mottagna meddelanden explicit. Meddelandet görs tillgänglig för mottagaren att bearbeta medan lagras under ett exklusivt lås i tjänsten så att andra, konkurrerande mottagarna inte kan se den. Låset definieras inledningsvis på kö eller prenumeration nivå och kan utökas genom klienten äger låset den [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) igen.

När ett meddelande är låst, andra klienter som tar emot från samma kö eller prenumeration ta på Lås och hämta nästa tillgängliga meddelanden inte under active Lås. När låset för ett meddelande släpps explicit eller låset upphör att gälla, visas meddelandet igen eller nära framför hämtning för redelivery.

När meddelandet släpps upprepade gånger av mottagare eller låter de låset förflyta för ett angivet antal gånger ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), meddelandet tas bort från kön eller prenumerationen och placeras i den associerade automatiskt kö för obeställbara meddelanden.

Den mottagande klienten initierar betalning för ett mottaget meddelande med en positiv bekräftelse när den anropar [Slutför](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) på API-nivå. Detta anger att Service broker att meddelandet har bearbetats och meddelandet tas bort från kön eller prenumeration. Service broker svarar mottagarens betalning avsikten med ett svar som anger om lösande kan utföras.

När den mottagande klienten misslyckas med att bearbeta meddelandet, men vill meddelandet att levereras, det explicit kan be inom vilken meddelandet måste släppas och låses upp direkt genom att anropa [Avbryt](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) eller gör ingenting och kan vara inaktiv låset.

Om ett mottagande klienten inte kan bearbeta ett meddelande och vet att redelivering meddelandet och försök hjälper inte, den kan avvisa meddelandet som flyttas till kön för obeställbara genom att anropa [Systemkön](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), vilket också gör det möjligt att ange en anpassad egenskap, inklusive en kod som kan hämtas med meddelandet från kön för obeställbara.

Ett specialfall avveckling av är avstängning som beskrivs i en separat artikel.

Den **Slutför** eller **Systemkön** åtgärder samt de **RenewLock** operations kan misslyckas på grund av nätverksproblem, om hölls låset har upphört att gälla eller så finns det andra villkor för tjänsten på klientsidan som hindrar betalning. I en av dessa fall kan skickar tjänsten en negativ bekräftelse som hämtar som ett undantag i API-klienter. Om orsaken är en bruten nätverksanslutning, har låset släppts eftersom Service Bus inte stöder återställning av befintliga AMQP länkar på en annan anslutning.

Om **Slutför** misslyckas, vilket sker vanligtvis i mycket slutet av meddelandehantering och i vissa fall efter minuter av bearbetning, mottagartillämpningen avgör om den bevarar tillståndet för arbetet och ignorerar samma meddelande när de skickas en gång eller om den raderar arbete resultatet och antal försök som meddelandet är levereras på nytt.

Vanliga mekanism för att identifiera dubbla meddelanden leveranser är genom att kontrollera meddelande-id som kan och bör anges av avsändaren att ett unikt värde som eventuellt justerad med en identifierare från den ursprungliga processen. En jobbschemat skulle förmodligen ange meddelande-id till identifieraren för det jobb som den försöker tilldela till en arbetsprocess med angivna arbetsprocessen och worker skulle ignorera den andra förekomsten av tilldelningen för jobbet om jobbet är redan klar.

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
