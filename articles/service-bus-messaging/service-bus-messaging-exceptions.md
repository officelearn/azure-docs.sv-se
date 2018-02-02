---
title: Azure Service Bus-meddelanden undantag | Microsoft Docs
description: "Lista över Service Bus-meddelanden undantag och föreslagna åtgärder."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: efcfad2834c2d6775c6693f5c705a0531b2650d6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus – undantag för meddelanden
Den här artikeln innehåller vissa undantag som genereras av Microsoft Azure Service Bus-meddelanden API: er. Den här referensen kan ändras, så kolla igen efter uppdateringar.

## <a name="exception-categories"></a>Undantag kategorier
Messaging API: er skapa undantag som kan delas in i följande kategorier tillsammans med den associerade åtgärden som du kan vidta att åtgärda dem. Observera att innebörd och orsakerna till ett undantag kan variera beroende på vilken typ av meddelandeentitet:

1. Användaren kodningsfel ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Allmän åtgärd: försök att åtgärda kod innan du fortsätter.
2. Installationen/konfigurationsfel ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: Granska konfigurationen och ändra vid behov.
3. Tillfälligt undantag ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allmän åtgärd: försök igen eller meddela användare.
4. Andra undantag ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allmän åtgärd: specifika för undantagstyp; Se tabellen nedan. 

## <a name="exception-types"></a>Typer av undantag
I följande tabell visas meddelanden undantag typer och orsaker och anteckningar föreslagna åtgärder du kan vidta.

| **Undantagstyp** | **Exempel-beskrivning/orsak** | **Föreslagen åtgärd** | **Tänk på automatisk omedelbara försök** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden som kontrolleras av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Servern har slutfört den begärda åtgärden. Det kan bero på nätverket eller andra infrastruktur fördröjningar. |Systemtillståndet för konsekvens och försök igen om det behövs. Se [tidsgränsfel](#timeoutexception). |Försök igen kan i vissa fall. Lägg till logik i koden. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Användar-åtgärden tillåts inte i servern eller tjänsten. Se Undantagsmeddelandet mer information. Till exempel [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) genererar det här undantaget om meddelandet togs emot i [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) läge. |Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Det hjälper inte försök igen. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Ett försök görs att anropa en åtgärd på ett objekt som redan har stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. |Kontrollera koden och kontrollera att den inte anropa åtgärder på ett borttaget objekt. |Det hjälper inte försök igen. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Den [TokenProvider](/dotnet/api/microsoft.azure.servicebus.tokenprovider) objektet kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att tokenleverantör har skapats med korrekta värden. Kontrollera konfigurationen av tjänsten åtkomstkontroll. |Försök igen kan i vissa fall. Lägg till logik i koden. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |En eller flera argument angavs för metoden är ogiltiga.<br /> URI som levererats till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) innehåller sökvägen segment.<br /> URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) är ogiltig. <br />Egenskapens värde är större än 32KB. |Kontrollera den anropande koden och kontrollera att argumenten är korrekta. |Det hjälper inte försök igen. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entitet som associeras med operationen finns inte eller har tagits bort. |Kontrollera att entiteten finns. |Det hjälper inte försök igen. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Försök att ta emot ett meddelande med ett visst sekvensnummer. Det här meddelandet hittades inte. |Kontrollera att meddelandet har tagits emot redan. Kontrollera för obeställbara meddelanden för att se om meddelandet har deadlettered. |Det hjälper inte försök igen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klienten kan inte upprätta en anslutning till Service Bus. |Kontrollera att det angivna värdnamnet är rätt och att värden kan nås. |Försök igen kan hjälpa om återkommande anslutningsproblem. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Tjänsten kan inte behandla begäran just nu. |Klienten kan vänta på en viss tidsperiod och försök igen. |Klienten kan försöka igen efter vissa intervall. Om ett nytt försök resulterar i en annan undantag, kontrollera försök beteendet för undantaget. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Lås token som är associerad med meddelandet har upphört att gälla eller Lås-token har inte hittats. |Ta bort meddelandet. |Det hjälper inte försök igen. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Lås som är associerade med den här sessionen går förlorad. |Avbryt den [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt. |Det hjälper inte försök igen. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Generisk messaging undantag som utlöses i följande fall:<br /> Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) med ett namn eller en sökväg som hör till en annan entitetstyp (till exempel ett ämne).<br />  Ett försök görs att skicka ett meddelande som är större än 256KB. Servern eller tjänsten påträffade ett fel under bearbetningen av begäran. Se Undantagsmeddelandet mer information. Detta är vanligtvis ett tillfälligt undantag. |Kontrollera koden och se till att endast serialiserbara objekt används för meddelandetexten (eller använda en anpassad serialisering). I dokumentationen för värdetyper som stöds av egenskaperna och endast använda stöds typer. Kontrollera den [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) egenskapen. Om det är **SANT**, du kan försöka igen. |Försök igen är odefinierad och inte kan hjälpa. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Försök att skapa en entitet med ett namn som redan används av en annan entitet i det namnområdet för tjänsten. |Ta bort den befintliga entiteten eller välj ett annat namn för enheten som ska skapas. |Det hjälper inte försök igen. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Meddelanden entiteten har uppnått den maximala storleken och det maximala antalet anslutningar till ett namnområde har överskridits. |Frigör utrymme i enheten genom att ta emot meddelanden från enheten eller dess underordnade köer. Se [QuotaExceededException](#quotaexceededexception). |Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus returnerar det här undantaget om du försöker skapa en ogiltig regel-åtgärd. Service Bus bifogar undantaget till ett deadlettered meddelande om ett fel uppstår under bearbetning av Regelåtgärd för meddelandet. |Kontrollera Regelåtgärden är korrekt. |Det hjälper inte försök igen. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus returnerar det här undantaget om du försöker skapa ett ogiltigt filter. Service Bus bifogar undantaget till ett deadlettered meddelande om ett fel uppstod under bearbetning av filtret för meddelandet. |Kontrollera filtret är korrekt. |Det hjälper inte försök igen. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Försök att ta emot en session med en specifik sessions-ID, men sessionen är låst av en annan klient. |Kontrollera att sessionen låses upp av andra klienter. |Försök igen kan hjälpa om sessionen har frisläppts under tiden. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |För många åtgärder är en del av transaktionen. |Minska antalet åtgärder som ingår i den här transaktionen. |Det hjälper inte försök igen. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Begäran om en körningsåtgärd på en inaktiverad entitet. |Aktivera entiteten. |Försök igen kan hjälpa om entiteten har aktiverats under tiden. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus returnerar det här undantaget om du skickar ett meddelande till en artikel som har före filtrering aktiverad och inget filter matchar. |Kontrollera att minst ett filter matchar. |Det hjälper inte försök igen. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Nyttolast för ett meddelande är längre än 256 KB. Observera att 256 KB-gränsen är totala meddelandelagringsstorlek, vilket kan innefatta Systemegenskaper och eventuella kostnader för .NET. |Minska storleken på nyttolast meddelande och försök igen. |Det hjälper inte försök igen. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Den omgivande transaktionen (*Transaction.Current*) är ogiltig. Det har slutförts eller avbrutits. I det interna undantaget kan innehålla ytterligare information. | |Det hjälper inte försök igen. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |En åtgärd utförs på en transaktion som är tveksam, eller ett försök görs att genomföra transaktionen och transaktionen blir osäker. |Programmet måste hantera detta undantag (som ett specialfall) som transaktionen kanske redan har utförts. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) anger en kvot för en viss enhet har överskridits.

### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen är det ofta storlek för kön. Fel meddelandeegenskap innehåller ytterligare information, som i följande exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Meddelandet om att avsnittet överskrider storleksgränsen i det här fallet 1 GB (Standardstorleksgränsen). 

### <a name="namespaces"></a>Namnområden

För namnområden, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) kan det tyda på att ett program har överskridit det maximala antalet anslutningar till ett namnområde. Exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till detta fel: kö med olevererade brev och icke-fungerande meddelandet mottagare.

1. **[Kö med olevererade brev](service-bus-dead-letter-queues.md)**  läsaren kan inte slutföra meddelanden och meddelanden returneras till kön/ämnet när låset upphör att gälla. Detta kan inträffa om läsaren påträffar ett undantag som förhindrar anropar [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). När ett meddelande har lästs 10 gånger flyttas till kön för obeställbara som standard. Det här beteendet styrs av den [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) egenskap och har standardvärdet 10. När meddelanden växer i kö med olevererade brev, ta plats.
   
    Lös problemet, Läs- och slutföra meddelanden från kön för obeställbara, precis som andra kön. Du kan använda den [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metoden för att formatera sökvägen för obeställbara meddelanden.
2. **Mottagaren stoppats** en mottagare har slutat att ta emot meddelanden från en kö eller prenumeration. Sätt att identifiera detta är att titta på den [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) -egenskap som visar fullständig uppdelning av meddelanden. Om den [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) egenskapen är hög eller växande sedan meddelanden läses inte lika snabbt som de skrivs.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

Du bör kontrollera värdet för den [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) egenskap som träffa den här gränsen kan också orsakas av en [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Köer och ämnen
Timeout-värdet har angetts för köer och ämnen, antingen i den [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) -egenskapen i anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Felmeddelandet själva kan variera, men innehåller alltid timeout-värdet som angetts för den aktuella åtgärden. 



## <a name="next-steps"></a>Nästa steg

Den fullständiga Service Bus .NET-API-referensen, finns det [Azure .NET API-referens för](/dotnet/api/overview/azure/service-bus).

Mer information om [Service Bus](https://azure.microsoft.com/services/service-bus/), finns i följande artiklar:

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-arkitektur](service-bus-architecture.md)

