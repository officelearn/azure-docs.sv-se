---
title: Meddelandeundantag – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en lista över Azure Event Hubs – undantag för meddelanden och föreslagna åtgärder.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: a6ebfc86a2489910d23faa96550f34cc979c0435
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203439"
---
# <a name="event-hubs-messaging-exceptions"></a>Event Hubs – undantag för meddelanden

Den här artikeln innehåller några av undantagen som genereras av Azure Service Bus messaging API biblioteket, vilket innefattar .NET Framework Event Hubs API: er. Den här referensen kan komma att ändras, så kom tillbaka för uppdateringar.

## <a name="exception-categories"></a>Undantag kategorier

API: er för Event Hubs generera undantag som kan delas in i följande kategorier, tillsammans med den associerade åtgärder som du kan vidta att åtgärda dem.

1. Användare kodningsfel: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installationen/konfigurationsfel: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: granskar du konfigurationen och ändra vid behov.
3. Tillfälliga undantag: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Allmän åtgärd: försök igen eller meddela användare.
4. Andra undantag: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [ Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Allmän åtgärd: specifika för typ av undantag; Se tabellen nedan. 

## <a name="exception-types"></a>Undantagstyper
I följande tabell visas meddelanden undantagstyper och orsaker och anteckningar föreslagna åtgärder du kan vidta.

| Undantagstyp | Exempel-beskrivning/orsak | Föreslagen åtgärd | Notera på automatisk/omedelbart återförsök |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden som kontrolleras av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern har slutfört den begärda åtgärden. Detta undantag kan inträffa på grund av nätverks- eller andra fördröjningar i infrastrukturen. |Kontrollera systemtillstånd för att få konsekvens och försök igen om det behövs.<br /> Se [TimeoutException](#timeoutexception). | Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Begärd användare-åtgärden tillåts inte i servern eller -tjänsten. Se Undantagsmeddelandet mer information. Till exempel [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) genererar det här undantaget om meddelandet har tagits emot i [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge. | Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. | Försök inte hjälper att. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Det görs ett försök att anropa en åtgärd på ett objekt som har redan stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. | Kontrollera koden och se till att den inte anropar åtgärder på ett borttaget objekt. | Försök inte hjälper att. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Den [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. | Kontrollera att Tokenleverantören har skapats med korrekta värden. Kontrollera konfigurationen av tjänsten Access Control. | Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Ett eller flera argument som skickades till metoden är ogiltiga. URI: N som har angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller sökvägen rörelsegren. URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltig. Egenskapens värde är större än 32 KB. | Kontrollera den anropande koden och se till att argumenten är korrekta. | Försök inte hjälper att. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entitet som är associerade med åtgärden finns inte eller har tagits bort. | Kontrollera att enheten finns. | Försök inte hjälper att. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klienten kan inte upprätta en anslutning till Event Hub. |Kontrollera att det angivna värdnamnet är rätt och värden kan nås. | Återförsök kan hjälpa dig att om det finns tillfälliga anslutningsproblem. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Tjänsten går inte att bearbeta begäran just nu. | Klienten kan vänta på en viss tidsperiod och försök igen. <br /> Se [ServerBusyException](#serverbusyexception). | Klienten kan försöka igen efter vissa intervall. Om ett nytt försök resulterar i en annan undantag, kontrollera funktionssätt för återförsök för undantaget. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Allmänna meddelanden undantag som kan uppkomma i följande fall: Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) med hjälp av ett namn eller sökväg som hör till en annan person-typ (till exempel ett ämne). Ett försök görs att skicka ett meddelande som är större än 1 MB. Servern eller -tjänsten påträffade ett fel under bearbetningen av begäran. Se Undantagsmeddelandet mer information. Det här undantaget är vanligtvis ett tillfälligt undantag. | Kontrollera koden och se till att endast serialiserbara objekt används för meddelandets brödtext (eller använda en anpassad serialiserare). I dokumentationen för olika egenskaperna värde som stöds och endast använda stöds typer. Kontrollera den [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) egenskapen. Om det är **SANT**, du kan försöka igen. | Funktionssätt för återförsök har inte definierats och kan inte. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Försöket att skapa en entitet med ett namn som redan används av en annan entitet i det tjänstnamnområdet. | Ta bort befintlig entitet eller välj ett annat namn för entitet som ska skapas. | Försök inte hjälper att. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Meddelandeentitet har nått maximal tillåten storlek. Detta undantag kan inträffa om det maximala antalet mottagare (vilket är 5) har redan öppnats i en per-consumer gruppnivå. | Skapa utrymme i entiteten genom att ta emot meddelanden från enheten eller dess underköer. <br /> Se [QuotaExceededException](#quotaexceededexception) | Återförsök kan hjälpa dig att om meddelanden har tagits bort under tiden. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Begäran om en åtgärd för körning på en inaktiverad enhet. |Aktivera entiteten. | Återförsök kan hjälpa dig att om entiteten har aktiverats under tiden. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Nyttolast för ett meddelande överskrider gränsen på 1 MB. Den här gränsen på 1 MB avser det totala meddelandet, vilket kan innefatta Systemegenskaper och eventuella kostnader för .NET. | Minska storleken på meddelandet nyttolasten och försök sedan igen. |Försök inte hjälper att. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

Detta undantag kan inträffa om det maximala antalet mottagare (5) har redan öppnats i en per-consumer gruppnivå.

### <a name="event-hubs"></a>Event Hubs
Händelsehubbar har en gräns på 20 konsumentgrupper per Event Hub. När du försöker skapa mer får du en [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

För Event Hubs, timeout-värdet anges antingen som en del av anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Felmeddelandet själva kan variera, men den innehåller alltid timeout-värdet som angetts för den aktuella åtgärden. 

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: felaktig konfiguration eller ett tillfälligt tjänstfel.

1. **Felaktig konfiguration** tidsgränsen för åtgärden kanske för liten för den operativa villkoren. Standardvärdet för tidsgräns för åtgärd i klient-SDK är 60 sekunder. Kontrollera om du vill se om din kod har värdet inställt på något som är för liten. Observera att villkoret för Nätverks- och CPU-användning kan påverka den tid det tar för en viss åtgärd ska slutföras, så att tidsgränsen för åtgärden inte ska ställas in till ett mindre värde.
2. **Tillfälliga tjänstfel** ibland händelsehubbtjänsten kan uppstår fördröjningar i bearbetning av begäranden, till exempel under perioder med hög belastning. I sådana fall kan du försöka utföra åtgärden efter en stund tills åtgärden har lyckats. Om samma åtgärden fortfarande misslyckas efter flera försök, gå till den [plats för Azure service-status](https://azure.microsoft.com/status/) att se om det finns några kända driftstopp.

## <a name="serverbusyexception"></a>ServerBusyException

En [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) eller [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) anger att en server är överbelastad. Det finns två relevanta felkoder för det här undantaget.

### <a name="error-code-50002"></a>Felkod 50002

Det här felet kan bero på något av två skäl:

1. Belastningen är inte jämnt fördelat över alla partitioner i event hub och en partition når begränsningen för enheter för lokal dataflöde.
    
    Lösning: Se över distributionsstrategi partition eller försök [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kan hjälpa dig att.

2. Event Hubs-namnområdet har inte tillräckligt med dataflödesenheter (du kan kontrollera den **mått** skärmen i Hubs namnrymdsfönstret i den [Azure-portalen](https://portal.azure.com) att bekräfta). Portalen visar aggregerade (1 minut) information, men vi mäter dataflöde i realtid – så att det är bara en uppskattning.

    Lösning: Du kan öka dataflödesenheter på namnområdet. Du kan göra den här åtgärden på portalen, i den **skala** fönstret på skärmen för Event Hubs-namnområdet. Du kan också använda [automatisk ökning](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>Felkod 50001

Det här felet ska skrivfel uppstår. Det händer när den behållare som körs koden för ditt namnområde har brist på CPU – inte mer än ett par sekunder innan Event Hubs belastningsutjämnare börjar.


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
