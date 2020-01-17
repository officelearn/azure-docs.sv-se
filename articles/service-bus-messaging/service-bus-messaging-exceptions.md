---
title: Fel söknings guide för Azure Service Bus | Microsoft Docs
description: Lista över Service Bus meddelande undantag och föreslagna åtgärder.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: eebbef25f2cd4539a5092f271c3944c24503f287
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156819"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Fel söknings guide för Azure Service Bus
Den här artikeln innehåller några av de .NET-undantag som genereras av Service Bus .NET Framework API: er och även andra tips för fel söknings problem. 

## <a name="service-bus-messaging-exceptions"></a>Service Bus – undantag för meddelanden
I det här avsnittet listas de .NET-undantag som genereras av .NET Framework API: er. 

### <a name="exception-categories"></a>Undantag kategorier
API: er för meddelanden genererar undantag som kan ingå i följande kategorier, tillsammans med den associerade åtgärden som du kan vidta för att försöka åtgärda dem. Innebörden och orsakerna till ett undantag kan variera beroende på typen av meddelande enhet:

1. Användar kodnings fel ([system. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [system. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [system. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [system. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installations-/konfigurations fel ([Microsoft. Service Bus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [system. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: granskar du konfigurationen och ändra vid behov.
3. Tillfälliga undantag ([Microsoft. Service Bus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. Service Bus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. Service Bus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allmän åtgärd: försök igen eller meddela användare. `RetryPolicy`-klassen i klient-SDK: n kan konfigureras för att hantera nya försök automatiskt. Mer information finns i [vägledning för återförsök](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Andra undantag ([system. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [system. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. Service Bus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. Service Bus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allmän åtgärd: särskilt för undantags typen; Se tabellen i följande avsnitt: 

### <a name="exception-types"></a>Undantagstyper
I följande tabell visas meddelanden undantagstyper och orsaker och anteckningar föreslagna åtgärder du kan vidta.

| **Undantags typ** | **Beskrivning/orsak/exempel** | **Föreslagen åtgärd** | **Observera vid automatisk/omedelbar återförsök** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, vilken styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern har slutfört den begärda åtgärden. Det kan inträffa på grund av fördröjningar i nätverket eller andra infrastrukturer. |Kontrollera systemtillstånd för att få konsekvens och försök igen om det behövs. Se [timeout-undantag](#timeoutexception). |Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Den begärda användar åtgärden tillåts inte i servern eller tjänsten. Se Undantagsmeddelandet mer information. Till exempel, [Slutför ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) genererar detta undantag om meddelandet togs emot i [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) -läge. |Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Det går inte att försöka igen. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Det görs ett försök att anropa en åtgärd på ett objekt som har redan stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. |Kontrol lera koden och se till att den inte anropar åtgärder på ett borttaget objekt. |Det går inte att försöka igen. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -objektet kunde inte hämta en token, token är ogiltig eller så innehåller token inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att Tokenleverantören har skapats med korrekta värden. Kontrol lera konfigurationen av Access Control Service. |Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Ett eller flera argument som skickades till metoden är ogiltiga.<br /> URI: N som har angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller sökvägen rörelsegren.<br /> URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltig. <br />Egenskapens värde är större än 32 KB. |Kontrollera den anropande koden och se till att argumenten är korrekta. |Det går inte att försöka igen. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entiteten som är kopplad till åtgärden finns inte eller har tagits bort. |Kontrollera att enheten finns. |Det går inte att försöka igen. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Försök att ta emot ett meddelande med ett visst ordnings nummer. Det här meddelandet hittades inte. |Se till att meddelandet inte har tagits emot redan. Kontrol lera obeställbara meddelanden kön-kön för att se om meddelandet har deadlettered. |Det går inte att försöka igen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klienten kan inte upprätta en anslutning till Service Bus. |Kontrollera att det angivna värdnamnet är rätt och värden kan nås. |Återförsök kan hjälpa dig att om det finns tillfälliga anslutningsproblem. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Det går inte att bearbeta begäran för tillfället i tjänsten. |Klienten kan vänta på en viss tidsperiod och försök igen. |Klienten kan försöka igen efter vissa intervall. Om ett nytt försök resulterar i en annan undantag, kontrollera funktionssätt för återförsök för undantaget. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Lås-token som är associerad med meddelandet har upphört att gälla eller också går det inte att hitta låset. |Ta bort meddelandet. |Det går inte att försöka igen. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Låset som är kopplat till den här sessionen förloras. |Avbryt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) -objektet. |Det går inte att försöka igen. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Allmänt meddelande undantag som kan uppstå i följande fall:<br /> Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) med ett namn eller en sökväg som tillhör en annan entitetstyp (till exempel ett ämne).<br />  Ett försök görs att skicka ett meddelande som är större än 256 KB. Servern eller -tjänsten påträffade ett fel under bearbetningen av begäran. Se Undantagsmeddelandet mer information. Det är vanligt vis ett tillfälligt undantag. |Kontrollera koden och se till att endast serialiserbara objekt används för meddelandets brödtext (eller använda en anpassad serialiserare). I dokumentationen för olika egenskaperna värde som stöds och endast använda stöds typer. Kontrollera den [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) egenskapen. Om det är **Sant**kan du försöka igen. |Funktionssätt för återförsök har inte definierats och kan inte. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Försöket att skapa en entitet med ett namn som redan används av en annan entitet i det tjänstnamnområdet. |Ta bort befintlig entitet eller välj ett annat namn för entitet som ska skapas. |Det går inte att försöka igen. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Meddelande enheten har uppnått maximalt tillåten storlek, eller så har det maximala antalet anslutningar till ett namn område överskridits. |Skapa utrymme i entiteten genom att ta emot meddelanden från enheten eller dess underköer. Se [QuotaExceededException](#quotaexceededexception). |Återförsök kan hjälpa dig att om meddelanden har tagits bort under tiden. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus returnerar detta undantag om du försöker skapa en ogiltig regel åtgärd. Service Bus bifogar detta undantag till ett deadlettered-meddelande om ett fel inträffar när regel åtgärden för meddelandet bearbetas. |Kontrol lera om regel åtgärden är korrekt. |Det går inte att försöka igen. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus returnerar detta undantag om du försöker skapa ett ogiltigt filter. Service Bus bifogar detta undantag till ett deadlettered-meddelande om ett fel uppstod under bearbetningen av filtret för meddelandet. |Kontrol lera om filtret är korrekt. |Det går inte att försöka igen. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Försök att godkänna en session med ett angivet sessions-ID, men sessionen är för närvarande låst av en annan klient. |Kontrol lera att sessionen är olåst av andra klienter. |Försök igen kan hjälpa om sessionen har frigjorts under tiden. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |För många åtgärder ingår i transaktionen. |Minska antalet åtgärder som ingår i den här transaktionen. |Det går inte att försöka igen. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Begäran om en åtgärd för körning på en inaktiverad enhet. |Aktivera entiteten. |Återförsök kan hjälpa dig att om entiteten har aktiverats under tiden. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus returnerar detta undantag om du skickar ett meddelande till ett ämne där för filtrering är aktiverat och ingen av filtren matchar. |Se till att minst ett filter matchar. |Det går inte att försöka igen. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |En meddelande nytto Last överskrider gränsen på 256 KB. Gränsen på 256 KB är den totala meddelande storleken, som kan omfatta system egenskaper och alla .NET-kostnader. |Minska storleken på meddelandet nyttolasten och försök sedan igen. |Det går inte att försöka igen. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Den omgivande transaktionen (*Transaction. Current*) är ogiltig. Den kan ha slutförts eller avbrutits. Inre undantag kan ge ytterligare information. | |Det går inte att försöka igen. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |En åtgärd görs på en transaktion som är tveksam, eller ett försök görs att genomföra transaktionen och transaktionen blir tveksam. |Ditt program måste hantera detta undantag (som ett specialfall) eftersom transaktionen kanske redan har genomförts. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

#### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen är det ofta storleken på kön. Fel meddelande egenskapen innehåller mer information, som i följande exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Meddelandet anger att avsnittet överskrider storleks gränsen, i det här fallet 1 GB (standard storleks gränsen). 

#### <a name="namespaces"></a>Namnområden

[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) kan ange att ett program har överskridit det maximala antalet anslutningar till ett namn område. Ett exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: kön för obeställbara meddelanden och icke fungerande mottagare.

1. **[Kö för obeställbara meddelanden](service-bus-dead-letter-queues.md)** En läsare kan inte slutföra meddelanden och meddelandena returneras till kön/ämnet när låset upphör att gälla. Det kan inträffa om läsaren påträffar ett undantag som förhindrar att den anropar [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). När ett meddelande har lästs 10 gånger flyttas det till kön för obeställbara meddelanden som standard. Detta beteende styrs av egenskapen [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) och har standardvärdet 10. När meddelanden sammanfattas i kön för obeställbara meddelanden tar de upp utrymme.
   
    Lös problemet genom att läsa och slutföra meddelandena från kön för obeställbara meddelanden, precis som i andra köer. Du kan använda metoden [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) för att formatera kön för obeställbara meddelanden.
2. **Mottagaren stoppades**. En mottagare har slutat ta emot meddelanden från en kö eller prenumeration. Sättet att identifiera detta är att titta på egenskapen [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , som visar den fullständiga detalj nivån av meddelandena. Om [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) -egenskapen är hög eller ökande, läses inte meddelandena som de ska när de skrivs.

### <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

Du bör kontrol lera värdet för egenskapen [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , så att den här gränsen kan även orsaka en [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen anges tids gränsen antingen i egenskapen [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) som en del av anslutnings strängen, eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Felmeddelandet själva kan variera, men den innehåller alltid timeout-värdet som angetts för den aktuella åtgärden. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problem med anslutning, certifikat eller tids gräns
Följande steg kan hjälpa dig med fel sökning av problem med anslutning/certifikat/tids gräns för alla tjänster under *. servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://sbwagn2.servicebus.windows.net/`. Det hjälper till med att kontrol lera om du har problem med IP-filtrering eller problem med virtuella nätverk eller certifikat kedjan (vanligt vis när du använder Java SDK).
- Kör följande kommando för att kontrol lera om någon port är blockerad i brand väggen. Beroende på vilket bibliotek du använder används även andra portar. Till exempel: 443, 5672, 9354.

    ```powershell
    tnc sbwagn2.servicebus.windows.net -port 5671
    ```

    I Linux:

    ```shell
    telnet sbwagn2.servicebus.windows.net 5671
    ```
- När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot försöker du upprätta 25 olika TCP-anslutningar varje 1 sekund med tjänsten och sedan kan du kontrol lera hur många lyckade/misslyckade som finns och även se TCP-anslutningssträngen. Du kan hämta verktyget `psping` [här](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q yournamespace.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc`, `ping`och så vidare. 
- Få en nätverks spårning om föregående steg inte hjälper och analysera det eller kontakta [Microsoft Support](https://support.microsoft.com/).


## <a name="next-steps"></a>Nästa steg

En fullständig Service Bus .NET API-referens finns i [referens för Azure .NET-API](/dotnet/api/overview/azure/service-bus).

Mer information om [Service Bus](https://azure.microsoft.com/services/service-bus/)finns i följande artiklar:

* [Översikt över Service Bus-meddelandetjänster](service-bus-messaging-overview.md)
* [Service Bus-arkitektur](service-bus-architecture.md)

