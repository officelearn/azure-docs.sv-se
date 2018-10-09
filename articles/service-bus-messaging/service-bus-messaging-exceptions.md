---
title: Azure Service Bus – undantag för meddelanden | Microsoft Docs
description: Lista över undantag för Service Bus-meddelanden och föreslagna åtgärder.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: spelluru
ms.openlocfilehash: 26118d55d200bb4a67c7f9cfad3975e7e6e5b45b
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855331"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus – undantag för meddelanden
Den här artikeln innehåller vissa undantag som genereras av Microsoft Azure Service Bus-meddelanden API: er. Den här referensen kan komma att ändras, så kom tillbaka för uppdateringar.

## <a name="exception-categories"></a>Undantag kategorier
API: er som meddelandefunktioner generera undantag som kan delas in i följande kategorier, tillsammans med den associerade åtgärder som du kan vidta att åtgärda dem. Innebörd och orsakerna till ett undantag kan variera beroende på vilken typ av meddelandeentitet:

1. Användaren kodningsfel ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installationen/konfigurationsfel ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: granskar du konfigurationen och ändra vid behov.
3. Tillfälliga undantag ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allmän åtgärd: försök igen eller meddela användare.
4. Andra undantag ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allmän åtgärd: specifika för typ av undantag; Se tabellen i följande avsnitt: 

## <a name="exception-types"></a>Undantagstyper
I följande tabell visas meddelanden undantagstyper och orsaker och anteckningar föreslagna åtgärder du kan vidta.

| **Undantagstyp** | **Exempel-beskrivning/orsak** | **Föreslagen åtgärd** | **Notera på automatisk/omedelbart återförsök** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden som kontrolleras av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Servern har slutfört den begärda åtgärden. Detta kan inträffa på grund av nätverks- eller andra fördröjningar i infrastrukturen. |Kontrollera systemtillstånd för att få konsekvens och försök igen om det behövs. Se [tidsgränsfel](#timeoutexception). |Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Begärd användare-åtgärden tillåts inte i servern eller -tjänsten. Se Undantagsmeddelandet mer information. Till exempel [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) genererar det här undantaget om meddelandet har tagits emot i [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) läge. |Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Det hjälper inte försök igen. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Det görs ett försök att anropa en åtgärd på ett objekt som har redan stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. |Kontrollera koden och se till att den inte anropar åtgärder på ett borttaget objekt. |Det hjälper inte försök igen. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Den [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att Tokenleverantören har skapats med korrekta värden. Kontrollera konfigurationen av tjänsten Access Control. |Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Ett eller flera argument som skickades till metoden är ogiltiga.<br /> URI: N som har angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) innehåller sökvägen rörelsegren.<br /> URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) är ogiltig. <br />Egenskapens värde är större än 32 KB. |Kontrollera den anropande koden och se till att argumenten är korrekta. |Det hjälper inte försök igen. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entitet som är associerade med åtgärden finns inte eller har tagits bort. |Kontrollera att enheten finns. |Det hjälper inte försök igen. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Försök att ta emot ett meddelande med en viss sekvensnummer. Det här meddelandet hittades inte. |Kontrollera att meddelandet har tagits emot redan. Kontrollera den obeställbara meddelanden. kön om meddelandet har gått deadlettered. |Det hjälper inte försök igen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klienten kan inte upprätta en anslutning till Service Bus. |Kontrollera att det angivna värdnamnet är rätt och värden kan nås. |Återförsök kan hjälpa dig att om det finns tillfälliga anslutningsproblem. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Tjänsten går inte att bearbeta begäran just nu. |Klienten kan vänta på en viss tidsperiod och försök igen. |Klienten kan försöka igen efter vissa intervall. Om ett nytt försök resulterar i en annan undantag, kontrollera funktionssätt för återförsök för undantaget. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Låstoken som är associerad med meddelandet har gått ut eller låstoken finns inte. |Ta bort meddelandet. |Det hjälper inte försök igen. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Lås som är associerade med den här sessionen går förlorad. |Avbryt den [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt. |Det hjälper inte försök igen. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Allmänna meddelanden undantag som kan uppkomma i följande fall:<br /> Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) med hjälp av ett namn eller sökväg som hör till en annan person-typ (till exempel ett ämne).<br />  Ett försök görs att skicka ett meddelande som är större än 256 KB. Servern eller -tjänsten påträffade ett fel under bearbetningen av begäran. Se Undantagsmeddelandet mer information. Detta är vanligtvis ett tillfälligt undantag. |Kontrollera koden och se till att endast serialiserbara objekt används för meddelandets brödtext (eller använda en anpassad serialiserare). I dokumentationen för olika egenskaperna värde som stöds och endast använda stöds typer. Kontrollera den [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) egenskapen. Om det är **SANT**, du kan försöka igen. |Funktionssätt för återförsök har inte definierats och kan inte. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Försöket att skapa en entitet med ett namn som redan används av en annan entitet i det tjänstnamnområdet. |Ta bort befintlig entitet eller välj ett annat namn för entitet som ska skapas. |Det hjälper inte försök igen. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Meddelandeentitet har nått maximal tillåten storlek eller det maximala antalet anslutningar till ett namnområde har överskridits. |Skapa utrymme i entiteten genom att ta emot meddelanden från enheten eller dess underköer. Se [QuotaExceededException](#quotaexceededexception). |Återförsök kan hjälpa dig att om meddelanden har tagits bort under tiden. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus returnerar det här undantaget om du försöker skapa en ogiltig regel-åtgärd. Service Bus kopplar det här undantaget till ett deadlettered meddelande om ett fel inträffar vid bearbetning av åtgärden i för meddelandet. |Kontrollera Regelåtgärd är korrekt. |Det hjälper inte försök igen. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus returnerar det här undantaget om du försöker skapa ett ogiltigt filter. Service Bus kopplar det här undantaget till ett deadlettered meddelande om ett fel uppstod vid bearbetning av filtret för meddelandet. |Kontrollera filtret är korrekt. |Det hjälper inte försök igen. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Försöker att acceptera en session med en specifik sessions-ID, men sessionen är låst av en annan klient. |Kontrollera att sessionen låses upp av andra klienter. |Återförsök kan hjälpa dig att om sessionen har släppts under tiden. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |För många åtgärder är en del av transaktionen. |Minska antalet åtgärder som ingår i den här transaktionen. |Det hjälper inte försök igen. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Begäran om en åtgärd för körning på en inaktiverad enhet. |Aktivera entiteten. |Återförsök kan hjälpa dig att om entiteten har aktiverats under tiden. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus returnerar det här undantaget om du skickar ett meddelande till ett ämne som har före filtrering aktiverad och ingen av filter som matchar. |Kontrollera att minst ett filter matchar. |Det hjälper inte försök igen. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Nyttolast för ett meddelande är längre än 256 KB. 256 KB-gränsen är den totala meddelandestorleken, vilket kan innefatta Systemegenskaper och eventuella kostnader för .NET. |Minska storleken på meddelandet nyttolasten och försök sedan igen. |Det hjälper inte försök igen. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Den omgivande transaktionen (*Transaction.Current*) är ogiltig. Det har slutförts eller avbröts. Meddelandet om internt undantag kan ge ytterligare information. | |Det hjälper inte försök igen. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |En åtgärd utförs på en transaktion som inte är osäker eller försök att genomföra transaktionen och transaktionen blir är osäker. |Programmet måste hantera det här undantaget (i specialfall), eftersom transaktionen kanske redan har utförts. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen i det här är ofta storleken för kön. Meddelandeegenskapen fel innehåller ytterligare information, som i följande exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Meddelandet om att avsnittet överskridit storleksgränsen i det här fallet 1 GB (storleksgräns för standard). 

### <a name="namespaces"></a>Namnområden

För namnområden, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) kan tyda på att ett program har överskridit det maximala antalet anslutningar till ett namnområde. Exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: obeställbara meddelanden och icke-fungerande meddelande mottagare.

1. **[Obeställbara meddelanden](service-bus-dead-letter-queues.md)**  en läsare kan inte slutföra meddelanden och meddelanden som returneras queue/Topic när låset upphör att gälla. Detta kan inträffa om läsaren påträffar ett undantag som förhindrar den att anropa [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). När ett meddelande har lästs 10 gånger flyttas till kön för obeställbara meddelanden som standard. Detta styrs av den [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) egenskap och har ett standardvärde på 10. När meddelanden växer i kö för obeställbara, tar de upp utrymme.
   
    Lös problemet genom att läsa och slutföra meddelanden från kön för obeställbara meddelanden, inte andra kön. Du kan använda den [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metoden för att formatera sökvägen för obeställbara meddelanden.
2. **Mottagare som stoppats**. En mottagare har stoppats ta emot meddelanden från en kö eller prenumeration. Sätt att identifiera det här är att titta på den [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) egenskapen, som visar fullständig uppdelning av meddelanden. Om den [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) egenskapen är hög eller växande och meddelanden läses inte så fort de skrivs.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

Du bör kontrollera värdet för den [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) egenskap som nått den här gränsen kan även orsaka en [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Köer och ämnen
Timeout-värdet har angetts för köer och ämnen, antingen i den [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) egenskap som en del av anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Felmeddelandet själva kan variera, men den innehåller alltid timeout-värdet som angetts för den aktuella åtgärden. 



## <a name="next-steps"></a>Nästa steg

Läs den fullständiga Service Bus .NET API-referensen i [Azure .NET API-referens](/dotnet/api/overview/azure/service-bus).

Mer information om [Service Bus](https://azure.microsoft.com/services/service-bus/), finns i följande artiklar:

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Service Bus-arkitektur](service-bus-architecture.md)

