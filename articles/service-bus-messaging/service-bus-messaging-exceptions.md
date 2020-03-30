---
title: Felsökningsguide för Azure Service Bus | Microsoft-dokument
description: Den här artikeln innehåller en lista över undantag från Azure Service Bus-meddelanden och föreslog åtgärder som ska vidtas när undantaget inträffar.
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
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: fb27befadcf8e6d201d020e758cfd1ef9b695f41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240811"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Felsökningsguide för Azure Service Bus
Den här artikeln innehåller några av de .NET-undantag som genereras av Service Bus .NET Framework API:er och även andra tips för felsökningsproblem. 

## <a name="service-bus-messaging-exceptions"></a>Undantag för Service Bus-meddelanden
I det här avsnittet visas .NET-undantag som genereras av .NET Framework API:er. 

### <a name="exception-categories"></a>Undantagskategorier
Meddelande-API:erna genererar undantag som kan delas in i följande kategorier, tillsammans med den associerade åtgärd som du kan vidta för att försöka åtgärda dem. Innebörden och orsakerna till ett undantag kan variera beroende på vilken typ av meddelandeentitet:

1. Fel på användarkodning ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installations-/konfigurationsfel ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: granska konfigurationen och ändra om det behövs.
3. Tillfälliga undantag ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Allmän åtgärd: försök igen eller meddela användarna. Klassen `RetryPolicy` i klienten SDK kan konfigureras för att hantera återförsök automatiskt. Mer information finns i [Vägledning för Försök igen](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Andra undantag ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.sessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Allmän åtgärd: särskild för undantagstypen. se tabellen i följande avsnitt: 

### <a name="exception-types"></a>Undantagstyper
I följande tabell visas undantagstyper för meddelanden och deras orsaker, och förslag på anteckningar som du kan vidta.

| **Undantagstyp** | **Beskrivning/orsak/exempel** | **Föreslagen åtgärd** | **Anmärkning om automatisk/omedelbar återförsök** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, som styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern kan ha slutfört den begärda åtgärden. Det kan hända på grund av nätverks- eller andra infrastrukturförseningar. |Kontrollera om det behövs konsekvens och försök igen om det behövs. Se [Timeout-undantag](#timeoutexception). |Försök igen kan hjälpa i vissa fall. lägga till logik för återförsök i koden. |
| [Ogiltigt OperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Den begärda användaråtgärden är inte tillåten i servern eller tjänsten. Mer information finns i undantagsmeddelandet. [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) genererar till exempel det här undantaget om meddelandet togs emot i [läget ReceiveAndDelete.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Kontrollera koden och dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Försök igen hjälper inte. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Ett försök görs att anropa en åtgärd på ett objekt som redan har stängts, avbrutits eller avyttrats. I sällsynta fall är den omgivande transaktionen redan avyttrad. |Kontrollera koden och se till att den inte anropar åtgärder på ett bortskaffat objekt. |Försök igen hjälper inte. |
| [Obehörigt tillträdeUtfors](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider-objektet](/dotnet/api/microsoft.servicebus.tokenprovider) kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att tokenprovidern skapas med rätt värden. Kontrollera konfigurationen av åtkomstkontrolltjänsten. |Försök igen kan hjälpa i vissa fall. lägga till logik för återförsök i koden. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Ett eller flera argument som anges till metoden är ogiltiga.<br /> Den URI som anges till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller bansegment.<br /> URI-schemat som levereras till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltigt. <br />Egenskapsvärdet är större än 32 kB. |Kontrollera den anropande koden och kontrollera att argumenten är korrekta. |Försök igen hjälper inte. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entiteten som är associerad med åtgärden finns inte eller så har den tagits bort. |Kontrollera att entiteten finns. |Försök igen hjälper inte. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Försök att ta emot ett meddelande med ett visst sekvensnummer. Det här meddelandet hittades inte. |Kontrollera att meddelandet inte redan har tagits emot. Kontrollera dödskön för att se om meddelandet har dött. |Försök igen hjälper inte. |
| [MeddelandekommunikationMeddelandeexception](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klienten kan inte upprätta en anslutning till Service Bus. |Kontrollera att det angivna värdnamnet är korrekt och att värden kan nås. |Ett nytt försök kan vara till hjälp om det finns tillfälliga anslutningsproblem. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Tjänsten kan inte behandla begäran just nu. |Klienten kan vänta en viss tid och sedan försöka igen. |Klienten kan försöka igen efter ett visst intervall. Om ett nytt försök resulterar i ett annat undantag kontrollerar du om du försöker utföra det undantaget. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Låstoken som är associerad med meddelandet har upphört att gälla eller så hittas inte låstoken. |Kassera meddelandet. |Försök igen hjälper inte. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Lås som är associerat med den här sessionen går förlorat. |Avbryt [MessageSession-objektet.](/dotnet/api/microsoft.servicebus.messaging.messagesession) |Försök igen hjälper inte. |
| [MeddelandeUtfors](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Allmänna meddelandeundantag som kan genereras i följande fall:<p>Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) med hjälp av ett namn eller en sökväg som tillhör en annan entitetstyp (till exempel ett ämne).</p><p>Ett försök görs att skicka ett meddelande som är större än 256 kB. </p>Servern eller tjänsten påträffade ett fel under bearbetningen av begäran. Mer information finns i undantagsmeddelandet. Det är oftast ett övergående undantag.</p><p>Begäran avbröts eftersom entiteten begränsas. Felkod: 50001, 50002, 50008. </p> | Kontrollera koden och se till att endast serialiserbara objekt används för meddelandetexten (eller använd en anpassad serialiserare). <p>Kontrollera dokumentationen för de värdetyper som stöds för egenskaperna och använd endast typer som stöds.</p><p> Kontrollera egenskapen [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Om det är **sant**kan du försöka igen. </p>| Om undantaget beror på begränsning väntar du några sekunder och försöker igen. Beteende för återförsök är odefinierat och kanske inte hjälper i andra scenarier.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Försök att skapa en entitet med ett namn som redan används av en annan entitet i tjänstnamnområdet. |Ta bort den befintliga entiteten eller välj ett annat namn för den entitet som ska skapas. |Försök igen hjälper inte. |
| [KvotExceedException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Meddelandeentiteten har nått sin högsta tillåtna storlek eller så har det maximala antalet anslutningar till ett namnområde överskridits. |Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess underköer. Se [QuotaExceedException](#quotaexceededexception). |Ett nytt försök kan vara till hjälp om meddelanden har tagits bort under tiden. |
| [RegelåtgärdUtåtgärd](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus returnerar det här undantaget om du försöker skapa en ogiltig regelåtgärd. Service Bus kopplar det här undantaget till ett obelett meddelande om ett fel uppstår vid bearbetning av regelåtgärden för det meddelandet. |Kontrollera om regelåtgärden är korrekt. |Försök igen hjälper inte. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus returnerar det här undantaget om du försöker skapa ett ogiltigt filter. Service Bus kopplar det här undantaget till ett obelett meddelande om ett fel uppstod vid bearbetning av filtret för det meddelandet. |Kontrollera att filtret är korrekt. |Försök igen hjälper inte. |
| [SessionKanotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Försök att acceptera en session med ett visst sessions-ID, men sessionen är för närvarande låst av en annan klient. |Kontrollera att sessionen låses upp av andra klienter. |Försök igen kan vara till hjälp om sessionen har släppts under tiden. |
| [TransactionSizeExceedException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |För många åtgärder ingår i transaktionen. |Minska antalet åtgärder som ingår i den här transaktionen. |Försök igen hjälper inte. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Begäran om en körningsåtgärd på en inaktiverad entitet. |Aktivera entiteten. |Försök igen kan vara till hjälp om entiteten har aktiverats under tiden. |
| [NoMatchingSubscriptionUtställning](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus returnerar det här undantaget om du skickar ett meddelande till ett ämne som har förfiltrering aktiverat och inget av filtren matchar. |Kontrollera att minst ett filter matchar. |Försök igen hjälper inte. |
| [MessageSizeExceedException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |En meddelandenyttolast överskrider gränsen på 256 KB. Gränsen på 256 KB är den totala meddelandestorleken, som kan innehålla systemegenskaper och eventuella .NET-omkostnader. |Minska storleken på meddelandenyttolasten och försök sedan igen. |Försök igen hjälper inte. |
| [TransaktionUtställning](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Den omgivande transaktionen (*Transaction.Current*) är ogiltig. Den kan ha slutförts eller avbrutits. Inre undantag kan ge ytterligare information. | |Försök igen hjälper inte. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |En åtgärd görs på en transaktion som är osäker, eller ett försök görs att genomföra transaktionen och transaktionen blir osäker. |Ditt program måste hantera det här undantaget (som ett specialfall), eftersom transaktionen redan har genomförts. |- |

### <a name="quotaexceededexception"></a>KvotExceedException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

#### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen är det ofta storleken på kön. Egenskapen error message innehåller ytterligare information, som i följande exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Meddelandet anger att ämnet överskridit dess storleksgräns, i det här fallet 1 GB (standardstorleksgränsen). 

#### <a name="namespaces"></a>Namnområden

För namnområden kan [QuotaExceedException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) ange att ett program har överskridit det maximala antalet anslutningar till ett namnområde. Ett exempel:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: kön för obeställbara meddelanden och icke-fungerande meddelandemottagare.

1. **[Kö för obeställbara meddelanden](service-bus-dead-letter-queues.md)** En läsare misslyckas med att slutföra meddelanden och meddelandena returneras till kön/ämnet när låset upphör att gälla. Det kan hända om läsaren stöter på ett undantag som hindrar den från att ringa [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). När ett meddelande har lästs 10 gånger flyttas det till kön för obeställbara meddelanden som standard. Det här beteendet styrs av egenskapen [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) och har standardvärdet 10. När meddelanden hopar sig i kön till döda bokstäver tar de plats.
   
    För att lösa problemet, läsa och slutföra meddelanden från kön för obeställbara meddelanden, på samma sätt som från någon annan kö. Du kan använda metoden [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) för att formatera kösökvägen för obeställbara meddelanden.
2. **Mottagaren har stoppats**. En mottagare har slutat ta emot meddelanden från en kö eller prenumeration. Sättet att identifiera detta är att titta på egenskapen [QueueDescription.MessageCountDetails,](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) som visar den fullständiga fördelningen av meddelandena. Om egenskapen [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) är hög eller växer läss inte meddelandena så snabbt som de skrivs.

### <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en användarinitierad åtgärd tar längre tid än timeouten för åtgärden. 

Du bör kontrollera värdet för egenskapen [ServicePointManager.DefaultConnectionLimit,](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) eftersom det också kan orsaka en [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Köer och ämnen
För köer och ämnen anges timeouten antingen i egenskapen [MessagingFactorySettings.OperationTimeout,](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) som en del av anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Felmeddelandet i sig kan variera, men det innehåller alltid det timeout-värde som angetts för den aktuella åtgärden. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Anslutnings-, certifikat- eller timeout-problem
Följande steg kan hjälpa dig att felsöka anslutnings-/certifikat-/timeout-problem för alla tjänster under *.servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Det hjälper med att kontrollera om du har IP-filtrering eller virtuellt nätverk eller certifikatkedjeproblem (vanligast när du använder java SDK).

    Ett exempel på lyckat meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på felmeddelande av fel:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrollera om någon port är blockerad i brandväggen. Portar som används är 443 (HTTPS), 5671 (AMQP) och 9354 (Net Messaging/SBMP). Beroende på vilket bibliotek du använder används även andra portar. Här är exempelkommandot som kontrollerar om 5671-porten är blockerad. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    På Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns återkommande anslutningsproblem kör du följande kommando för att kontrollera om det finns några ignorerade paket. Det här kommandot kommer att försöka upprätta 25 olika TCP-anslutningar var 1 sekund med tjänsten. Sedan kan du kontrollera hur många av dem som lyckades/misslyckades och även se TCP-anslutningsfördröjning. Du kan `psping` ladda ner verktyget [härifrån](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder `tnc` `ping`andra verktyg, till exempel , och så vidare. 
- Hämta en nätverksspårning om de föregående stegen inte hjälper och analyserar den med verktyg som [Wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problem som kan uppstå vid serviceuppgraderingar/omstarter
Uppgraderingar och omstarter av backend-tjänsten kan orsaka följande inverkan på dina program:

- Begäranden kan kort hållas strypt.
- Det kan finnas en minskning av inkommande meddelanden/begäranden.
- Loggfilen kan innehålla felmeddelanden.
- Programmen kan kopplas från tjänsten i några sekunder.

Om programkoden använder SDK är återförsöksprincipen redan inbyggd och aktiv. Programmet återansluter utan någon större påverkan på programmet/arbetsflödet.

## <a name="next-steps"></a>Nästa steg

Den fullständiga servicebussen .NET API-referens finns i [Azure .NET API-referensen](/dotnet/api/overview/azure/service-bus).

Mer information om [Service Bus](https://azure.microsoft.com/services/service-bus/)finns i följande artiklar:

* [Översikt över servicebussmeddelanden](service-bus-messaging-overview.md)
* [Service Bus-arkitektur](service-bus-architecture.md)

