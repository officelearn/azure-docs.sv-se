---
title: Azure Service Bus meddelandeöverföringar, lås och kvittning
description: Den här artikeln innehåller en översikt över Azure Service Bus-meddelandeöverföringar, lås och kvittningsåtgärder.
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
ms.date: 01/24/2019
ms.author: aschhab
ms.openlocfilehash: a2c353d612280981a83b32463d34efdc70878495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261000"
---
# <a name="message-transfers-locks-and-settlement"></a>Överföringar av meddelanden, lås och uppgörelser

Den centrala möjligheten för en meddelandemäklare som Service Bus är att acceptera meddelanden i en kö eller ett ämne och hålla dem tillgängliga för senare hämtning. *Skicka* är den term som ofta används för överföring av ett meddelande till meddelandemäklaren. *Mottagning* är den term som vanligen används för överföring av ett meddelande till en hämtningsklient.

När en klient skickar ett meddelande, vill den vanligtvis veta om meddelandet har överförts korrekt till och accepterats av mäklaren eller om någon form av fel uppstod. Denna positiva eller negativa bekräftelse avgör klienten och mäklaren förståelse om överföringen tillstånd av meddelandet och kallas därmed *uppgörelse*.

På samma sätt, när mäklaren överför ett meddelande till en kund, mäklaren och kunden vill skapa en förståelse för om meddelandet har bearbetats och kan därför tas bort, eller om meddelandet leverans eller bearbetning misslyckades, och därmed meddelandet meddelandet kan behöva levereras igen.

## <a name="settling-send-operations"></a>Lösa sändningsåtgärder

Med hjälp av någon av de service bus API-klienter som stöds, skicka åtgärder till Service Bus alltid uttryckligen avvecklas, vilket innebär att API-åtgärden väntar på ett godkännanderesultat från Service Bus att komma fram och sedan slutför skicka-åtgärden.

Om meddelandet avvisas av Service Bus innehåller avslaget en felindikator och text med ett "spårnings-ID" inuti. Avslaget innehåller också information om huruvida åtgärden kan göras om med några förväntningar på framgång. I klienten omvandlas den här informationen till ett undantag och utlöses till anroparen av skicka-åtgärden. Om meddelandet har accepterats slutförs åtgärden tyst.

När du använder AMQP-protokollet, som är det exklusiva protokollet för .NET Standard-klienten och Java-klienten och [som är ett alternativ för .NET Framework-klienten,](service-bus-amqp-dotnet.md)pipelines och kvittningar är pipelined och helt asynkrona, och det rekommenderas att du använder asynkrona programmeringsmodell API-varianter.

En avsändare kan sätta flera meddelanden på tråden i snabb följd utan att behöva vänta på att varje meddelande ska bekräftas, vilket annars skulle vara fallet med SBMP-protokollet eller med HTTP 1.1. Dessa asynkrona skicka åtgärder slutföras som respektive meddelanden accepteras och lagras, på partitionerade entiteter eller när skicka operation till olika entiteter överlappar varandra. Slutförandena kan också ske utanför den ursprungliga sändningsordern.

Strategin för att hantera resultatet av sändningsåtgärder kan ha omedelbar och betydande prestandapåverkan för ditt program. Exemplen i det här avsnittet är skrivna i C# och gäller motsvarande för Java Futures.

Om programmet producerar skurar av meddelanden, illustreras här med en vanlig loop, och skulle vänta slutförandet av varje skicka operation innan du skickar nästa meddelande, synkrona eller asynkrona API former lika, skicka 10 meddelanden endast slutförs efter 10 sekventiella hela tur- och returresor för avveckling.

Med en förmodad 70 millisekunder TCP tur och retur latens avstånd från en lokal plats till Service Bus och ger bara 10 ms för Service Bus att acceptera och lagra varje meddelande, följande slinga tar upp minst 8 sekunder, inte räknar nyttolast överföringstid eller potential effekter på överbelastning:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Om ansökan startar de 10 asynkrona sändningsåtgärderna i omedelbar följd och väntar på att de ska slutföras separat, överlappar tur och retur för dessa 10 skicka åtgärder. De 10 meddelandena överförs i omedelbar följd, eventuellt även dela TCP ramar, och den totala överföringstiden beror till stor del på nätverksrelaterad tid det tar att få meddelandena överförs till mäklaren.

Om du gör samma antaganden som för den tidigare loopen kan den totala överlappande körningstiden för följande slinga stanna en bra under en sekund:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Det är viktigt att notera att alla asynkrona programmeringsmodeller använder någon form av minnesbaserad, dold arbetskö som innehåller väntande åtgärder. När [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) eller **Send** (Java) returneras köas sändningsuppgiften i arbetskön, men protokollgesten börjar först när aktiviteten är tur att köras. För kod som tenderar att driva skurar av meddelanden och där tillförlitlighet är ett problem, bör man vara noga med att inte alltför många meddelanden sätts "i flykt" på en gång, eftersom alla skickade meddelanden tar upp minnet tills de faktiskt har lagts på tråden.

Semaforer, som visas i följande kodavsnitt i C#, är synkroniseringsobjekt som aktiverar sådana begränsning på programnivå när det behövs. Denna användning av en semafor gör det möjligt för högst 10 meddelanden att vara i flykten på en gång. En av de 10 tillgängliga semaforlås tas innan skicka och det släpps när skicka slutförs. Den 11: e passera genom slingan väntar tills minst en av de tidigare skickar har slutförts, och sedan gör sitt lås tillgängligt:

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

Program bör **aldrig** inleda en asynkron skicka operation på ett "brand och glömma" sätt utan att hämta resultatet av operationen. Om du gör det kan den interna och osynliga aktivitetskön läsas upp till minnesutmattning och programmet förhindras att skicka fel identifieras:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Med en amqp-klient på låg nivå accepterar Service Bus även "förreglerade" överföringar. En förreglerad överföring är en brand-och-glöm-åtgärd för vilken resultatet, hur som helst, inte rapporteras tillbaka till klienten och meddelandet anses vara avgjort när det skickas. Bristen på feedback till klienten innebär också att det inte finns några användbara data tillgängliga för diagnostik, vilket innebär att det här läget inte uppfyller kraven för hjälp via Azure-support.

## <a name="settling-receive-operations"></a>Lösa mottagningsåtgärder

För mottagningsåtgärder aktiverar Service Bus *API-klienter* två olika explicita lägen: Ta emot och ta bort och *Peek-Lock*.

### <a name="receiveanddelete"></a>Ta emot OchDelete

[Mottagnings- och borttagningsläget](/dotnet/api/microsoft.servicebus.messaging.receivemode) talar om för mäklaren att ta hänsyn till alla meddelanden som skickas till den mottagande klienten som kvittade när de skickas. Det innebär att meddelandet anses konsumeras så snart mäklaren har lagt den på tråden. Om meddelandeöverföringen misslyckas går meddelandet förlorat.

Fördelen med detta läge är att mottagaren inte behöver vidta ytterligare åtgärder på meddelandet och inte heller bromsas av att vänta på resultatet av uppgörelsen. Om uppgifterna i de enskilda meddelandena har lågt värde och/eller endast är meningsfulla under en mycket kort tid, är detta läge ett rimligt val.

### <a name="peeklock"></a>PeekLock (PeekLock)

[Peek-Lock-läget](/dotnet/api/microsoft.servicebus.messaging.receivemode) talar om för mäklaren att den mottagande klienten uttryckligen vill lösa mottagna meddelanden. Meddelandet görs tillgängligt för mottagaren att bearbeta, medan det hålls under ett exklusivt lås i tjänsten så att andra konkurrerande mottagare inte kan se det. Låsets varaktighet definieras ursprungligen på kö- eller prenumerationsnivå och kan förlängas av klienten som äger låset, via [RenewLock-åtgärden.](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

När ett meddelande är låst kan andra klienter som tar emot från samma kö eller prenumeration ta på lås och hämta nästa tillgängliga meddelanden som inte är under aktivt lås. När låset på ett meddelande uttryckligen släpps eller när låset går ut, dyker meddelandet upp på eller nära framsidan av hämtningsordningen för omleverans.

När meddelandet släpps upprepade gånger av mottagare eller om de låter låset förflyta för ett definierat antal gånger ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)) tas meddelandet automatiskt bort från kön eller prenumerationen och placeras i den associerade kön för obeställbara meddelanden.

Den mottagande klienten initierar kvittning av ett mottaget meddelande med en positiv bekräftelse när den [anropar Slutför](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) på API-nivå. Detta indikerar för mäklaren att meddelandet har bearbetats och meddelandet tas bort från kön eller prenumerationen. Mäklaren svarar på mottagarens kvittningsavsikt med ett svar som anger om kvittningen kan utföras.

När den mottagande klienten inte bearbetar ett meddelande men vill att meddelandet ska levereras igen, kan den uttryckligen begära att meddelandet ska släppas och låsas upp direkt genom att ringa [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) eller det kan göra någonting och låta låset förflyta.

Om en mottagande klient inte bearbetar ett meddelande och vet att omleverans av meddelandet och försöker igen åtgärden inte kommer att hjälpa, kan den avvisa meddelandet, som flyttar det till obeställbara meddelanden kö genom att ringa [DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), vilket också gör det möjligt att ange en anpassad egenskap inklusive en orsakskod som kan hämtas med meddelandet från dödbokstavskön.

Ett specialt fall av bosättning är uppskov, som diskuteras i en separat artikel.

**Fullständig-** eller **Dödnings- eller dödningsoperationer** samt **RenewLock-åtgärderna** kan misslyckas på grund av nätverksproblem, om det kvarhållna låset har upphört att gälla eller om det finns andra servicevillkor som förhindrar kvittning. I ett av de senare fallen skickar tjänsten en negativ bekräftelse som ytor som ett undantag i API-klienterna. Om orsaken är en trasig nätverksanslutning tas låset bort eftersom Service Bus inte stöder återställning av befintliga AMQP-länkar på en annan anslutning.

Om **Complete** misslyckas, vilket vanligtvis inträffar i slutet av meddelandehanteringen och i vissa fall efter minuter av bearbetningsarbete, kan det mottagande programmet avgöra om det bevarar arbetets tillstånd och ignorerar samma meddelande när det levereras en andra gång, eller om det kastar ut arbetsresultatet och försöker om när meddelandet levereras om.

Den typiska mekanismen för att identifiera dubbla meddelandeleveranser är genom att kontrollera meddelande-id, som kan och bör ställas in av avsändaren till ett unikt värde, eventuellt i linje med en identifierare från ursprungsprocessen. En jobbschemaläggare skulle sannolikt ange meddelande-id till identifieraren för det jobb som den försöker tilldela en anställd med den angivna arbetaren, och arbetaren skulle ignorera den andra förekomsten av jobbtilldelningen om jobbet redan är gjort.

> [!IMPORTANT]
> Det är viktigt att notera att låset som PeekLock förvärvar på meddelandet är flyktigt och kan gå förlorat under följande förhållanden
>   * Uppdatering av tjänsten
>   * Uppdatering av operativsystemet
>   * Ändra egenskaper för entiteten (kö, ämne, prenumeration) medan du håller låset.
>
> När låset bryts genererar Azure Service Bus en LockLostException som kommer att visas på klientprogramkoden. I det här fallet ska klientens standardlogik för återförsök automatiskt aktiveras och försöka igen.

## <a name="next-steps"></a>Nästa steg

Mer information om Service Bus-meddelanden finns i följande avsnitt:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
