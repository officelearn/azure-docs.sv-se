---
title: Azure Service Bus-överföringar av meddelanden, lås och uppgörelser | Microsoft Docs
description: Översikt över Service Bus överföringar av meddelanden och avräkning åtgärder
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: aschhab
ms.openlocfilehash: a78409a15acb4e60fc4200778d0f33b3fb566e85
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846455"
---
# <a name="message-transfers-locks-and-settlement"></a>Överföringar av meddelanden, lås och uppgörelser

Central möjligheterna för en asynkron meddelandekö, till exempel Service Bus är att acceptera meddelanden i en kö eller ämne och hålla dem tillgängliga för senare hämtning. *Skicka* är term som används för överföring av ett meddelande till den asynkrona meddelandekön. *Ta emot* är begrepp som ofta används för överföring av ett meddelande till en klient som hämtades.

När en klient skickar ett meddelande, företaget vanligtvis vill veta om meddelandet korrekt har överförts till och accepteras av Service broker eller om någon typ av fel inträffat. Den här positiv eller negativ bekräftelse cykelvärdena klienten och den asynkrona meddelandekön förstå om överföring tillståndet för meddelandet och är därför kallas *avräkning*.

På samma sätt när den asynkrona meddelandekön över ett meddelande till en klient, broker och klienten vill upprätta en bra uppfattning om meddelandet har bearbetats och kan därför tas bort eller om meddelandeleverans eller bearbetning misslyckades, och därmed den meddelandet kanske levereras igen.

## <a name="settling-send-operations"></a>Reglera skicka åtgärder

Med någon av de Service Bus-API-klienterna som stöds kan skicka åtgärder i Service Bus alltid uttryckligen har reglerats, vilket innebär att API-åtgärden väntar på att ett godkännande-resultat från Service Bus tas emot, och slutför sedan skicka igen.

Om meddelandet avvisades av Service Bus, innehåller avvisningen en felindikator och text med en ”spårnings-id” inuti den. Avvisningen innehåller även information om huruvida åtgärden kan göras med alla förväntan att lyckas. I klienten, är den här informationen omvandlas till ett undantag och upphöjt till anroparen av skicka igen. Om meddelandet har accepterats, åtgärden har slutförts tyst.

När du använder AMQP-protokollet som är exklusiva protokoll för .NET Standard-klienten och Java-klient och [som är ett alternativ för .NET Framework-klienten](service-bus-amqp-dotnet.md), överföringar av meddelanden och kvittningar är pipelined och helt asynkront, och vi rekommenderar att du använder de asynkrona programming model API varianterna.

En avsändare kan du placera flera meddelanden för anslutningen i snabb följd utan att behöva vänta på att varje meddelande som ska bekräftas innebär, som annars skulle vara fallet SBMP-protokollet eller med HTTP 1.1. De asynkrona skicka åtgärderna att slutföra eftersom respektive meddelanden accepteras och lagras på partitionerade enheter eller skicka när åtgärden till olika entiteter överlappar varandra. Slutföranden kan även inträffa utanför den ursprungliga ordningen för Skicka.

Strategi för hantering av resultatet av skicka åtgärder kan ha omedelbar och betydande prestandapåverkan för ditt program. Exemplen i det här avsnittet är skrivna i C# och tillämpa equivalently för Java Futures.

Om programmet genererar ökningar av meddelanden, illustreras här med en vanlig slinga och var i väntan på slutförande för var och en skickar igen innan du skickar nästa meddelande, synkron eller asynkron API former både, 10 meddelanden skickas endast slutförs efter 10 sekventiella fullständig tur och RETUR för betalning.

Med en antagen 70 Millisekunden TCP tur och RETUR svarstid avståndet från en lokal plats till Service Bus och får bara 10 ms för Service Bus för att acceptera och lagra varje meddelande, tar loopen upp minst 8 sekunder, inte räkna nyttolast överföringstiden eller hitta potentiella väg överbelastning effekter:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Om programmet startar 10 skicka asynkrona åtgärder i direkt följd och väntar på dig sina respektive slutförande separat, överlappar kommunikation tur och RETUR-tid för de 10 skicka åtgärderna. 10 meddelanden överförs i direkt följd som potentiellt även delning av TCP-ramar, och varaktighet för överföring beror huvudsakligen på nätverksrelaterade tiden det tar för att hämta meddelanden överförs till den asynkrona meddelandekön.

Gör samma antaganden som tidigare loopen, kan den totala överlappande körningstiden för loopen förblir bra under en sekund:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Det är viktigt att Observera att alla asynkrona programmeringsmodeller använda någon form av Minnesbaserad, dold arbetskö som innehåller väntande åtgärder. När [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) eller **skicka** (Java) returneras, skicka aktiviteten är i kö i den arbetskön men protocol-gest inleds endast när det är aktivitetens tur att köra. För kod som ofta push ökningar av meddelanden och där tillförlitlighet är viktigt, anledning att inte för många meddelanden placeras ”som rör sig” på samma gång eftersom alla skickade meddelanden ta upp minne tills de har formellt förts till ledningen.

Semaforer, är enligt följande kodavsnitt i C#, synkroniseringsobjekt som gör det möjligt att sådan på programnivå som begränsning när det behövs. Den här användningen av en semafor kan högst 10 meddelanden ska vara som rör sig på samma gång. En av 10 tillgängliga semafor låsen vidtas innan skicka och den släpps som skicka är klar. Den 11: e passerar den loop som väntar tills minst en av föregående skickar har slutförts och sedan gör låstiden:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Program bör **aldrig** initiera en asynkron sändningsåtgärden i ett ”utlöses och Glöm” sätt utan att hämta resultatet av åtgärden. Då kan du läsa in interna och osynliga aktivitetskö upp till minnet är förbrukat, och programmet att identifiera skicka fel:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Med en låg nivå AMQP-klient, Service Bus kan också användas ”före reglerade” överföringar. En förväg reglerade överföring är en fire-and-forget-åtgärd som resultatet i båda fallen inte rapporteras tillbaka till klienten och meddelandet anses reglerats när de skickas. Bristen på feedback till klienten innebär också att det finns inga användbara data för diagnostik, vilket innebär att det här läget inte är lämplig för att få hjälp via Azure-supporten.

## <a name="settling-receive-operations"></a>Reglera får operations

För att ta emot operations, Service Bus-API-klienter aktivera två olika lägen för explicit: *Ta emot och borttagning* och *Peek-Lock*.

Den [ta emot och ta bort](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge talar om för den asynkrona meddelandekön att tänka på alla meddelanden som skickas till den mottagande klienten som reglerade när skickas. Det innebär att meddelandet anses förbrukas så snart den asynkrona meddelandekön har satt till ledningen. Meddelandet går förlorad om meddelandeöverföringen misslyckas.

Upp-och av det här läget är att mottagaren behöver inte vidta ytterligare åtgärder för meddelandet och även inte långsammare genom resultatet av samband. Om data i enskilda meddelanden har lågt värde, eller endast är meningsfulla för mycket kort tid, är det rimliga valet i det här läget.

Den [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge meddelar den asynkrona meddelandekön att den mottagande klienten vill reglera mottagna meddelanden uttryckligen. Meddelandet görs tillgänglig för mottagaren att bearbeta, medan lagras under ett exklusivt lås i tjänsten så att andra, konkurrerande mottagarna inte kan se den. Varaktigheten för låset definieras inledningsvis på kö eller prenumeration och kan utökas genom klienten ägande låset, via den [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) igen.

När ett meddelande är låst kan andra klienter som tar emot från samma kö eller prenumeration ikläd Lås och hämta nästa tillgängliga meddelanden inte under active Lås. När låset på meddelandet har släppts explicit, eller när låset upphör att gälla, visas meddelandet tillbaka slut eller nästan framför hämtning ordningen för redelivery.

När meddelandet släpps flera gånger av mottagare eller de gör det låset förflyta för ett angivet antal gånger ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), meddelandet tas automatiskt bort från kö eller prenumeration och placeras i den associerade obeställbara meddelanden.

Mottagande klienten startar avräkning av ett mottaget meddelande med en positiv bekräftelse när anropas [Slutför](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) på API-nivå. Detta anger till den asynkrona meddelandekön att meddelandet har bearbetats och meddelandet tas bort från kö eller prenumeration. Den asynkrona meddelandekön svarar mottagarens avräkning avsikten med ett svar som anger om lösande kan utföras.

När den mottagande klienten inte kan bearbeta meddelandet, men vill meddelandet att levereras, det kan uttryckligen ställa för meddelandet att frigöra och olåst direkt genom att anropa [överge](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) eller den kan inte göra någonting och låta låset förflyta.

Om ett mottagande klienten kan inte bearbeta ett meddelande och vet att redelivering meddelandet och gör om åtgärden inte hjälper, den kan avvisa meddelandet, vilket flyttar det till kön för obeställbara meddelanden genom att anropa [Systemkön](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), vilket också kan du ange en anpassad egenskap, inklusive en orsakskod som kan hämtas med meddelandet från kön för obeställbara meddelanden.

Specialfall avveckling av är uppskjutna som beskrivs i en separat artikel.

Den **Slutför** eller **Systemkön** åtgärder samt de **RenewLock** kan misslyckas på grund av nätverksproblem, om hållas låset har upphört att gälla eller så finns det andra tjänstsidan villkor som förhindrar betalning. I en av de senare fallen skickar tjänsten en negativ bekräftelse som lyfter fram som ett undantag i API-klienter. Om orsaken är en bruten nätverksanslutning, har låset släppts eftersom Service Bus inte stöder återställning av befintliga AMQP länkar på en annan anslutning.

Om **Slutför** misslyckas, vilket inträffar normalt Setup.exe för meddelandehantering och i vissa fall efter minuter av bearbetning, det mottagande programmet kan bestämma om den bevarar tillståndet för arbetet och ignorerar samma meddelande när de skickas en gång, eller om det raderar work resultatet och försöker som meddelandet är once.

Vanliga mekanism för att identifiera dubblettmeddelandet leveranser är genom att kontrollera meddelande-id som kan och bör anges av avsändaren till ett unikt värde som eventuellt justeras med en identifierare från den ursprungliga processen. En jobbschemat skulle förmodligen ange meddelande-id till identifieraren för den försöker tilldela till en arbetare till den angivna worker-jobbet och worker skulle ignorera den andra förekomsten av jobbet tilldelningen om jobbet har redan gjorts.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Service Bus-meddelanden, finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
