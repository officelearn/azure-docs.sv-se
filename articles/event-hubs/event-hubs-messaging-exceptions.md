---
title: Felsökningsguide – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller en lista över undantag för Azure Event Hubs-meddelanden och föreslagna åtgärder.
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
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309786"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Felsökningsguide för Azure Event Hubs
Den här artikeln innehåller några av de .NET-undantag som genereras av Event Hubs .NET Framework API:er och även andra tips för felsökningsproblem. 

## <a name="event-hubs-messaging-exceptions---net"></a>Meddelandeundantag för eventhubbar - .NET
I det här avsnittet visas .NET-undantag som genereras av .NET Framework API:er. 

### <a name="exception-categories"></a>Undantagskategorier

Event Hubs .NET API:er genererar undantag som kan delas in i följande kategorier, tillsammans med den associerade åtgärd som du kan vidta för att försöka åtgärda dem.

1. Fel på användarkodning: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installations-/konfigurationsfel: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: granska konfigurationen och ändra om det behövs.
3. Tillfälliga undantag: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Allmän åtgärd: försök igen eller meddela användarna.
4. Andra undantag: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.sessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Allmän åtgärd: särskild för undantagstypen. se tabellen i följande avsnitt. 

### <a name="exception-types"></a>Undantagstyper
I följande tabell visas undantagstyper för meddelanden och deras orsaker, och förslag på anteckningar som du kan vidta.

| Undantagstyp | Beskrivning/orsak/exempel | Föreslagen åtgärd | Anmärkning om automatisk/omedelbar återförsök |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, som styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern kan ha slutfört den begärda åtgärden. Det här undantaget kan inträffa på grund av nätverksförseningar eller andra infrastrukturförseningar. |Kontrollera om det behövs konsekvens och försök igen om det behövs.<br /> Se [TimeoutException](#timeoutexception). | Försök igen kan hjälpa i vissa fall. lägga till logik för återförsök i koden. |
| [Ogiltigt OperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Den begärda användaråtgärden är inte tillåten i servern eller tjänsten. Mer information finns i undantagsmeddelandet. [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) genererar till exempel det här undantaget om meddelandet togs emot i [läget ReceiveAndDelete.](/dotnet/api/microsoft.servicebus.messaging.receivemode) | Kontrollera koden och dokumentationen. Kontrollera att den begärda åtgärden är giltig. | Försök igen hjälper inte. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Ett försök görs att anropa en åtgärd på ett objekt som redan har stängts, avbrutits eller avyttrats. I sällsynta fall är den omgivande transaktionen redan avyttrad. | Kontrollera koden och se till att den inte anropar åtgärder på ett bortskaffat objekt. | Försök igen hjälper inte. |
| [Obehörigt tillträdeUtfors](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider-objektet](/dotnet/api/microsoft.servicebus.tokenprovider) kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. | Kontrollera att tokenprovidern skapas med rätt värden. Kontrollera konfigurationen av åtkomstkontrolltjänsten. | Försök igen kan hjälpa i vissa fall. lägga till logik för återförsök i koden. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Ett eller flera argument som anges till metoden är ogiltiga. Den URI som anges till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller bansegment. URI-schemat som levereras till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltigt. Egenskapsvärdet är större än 32 kB. | Kontrollera den anropande koden och kontrollera att argumenten är korrekta. | Försök igen hjälper inte. |
| [Microsoft.ServiceBus.Messaging MeddelandenEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entiteten som är associerad med åtgärden finns inte eller så har den tagits bort. | Kontrollera att entiteten finns. | Försök igen hjälper inte. |
| [MeddelandekommunikationMeddelandeexception](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klienten kan inte upprätta en anslutning till Event Hub. |Kontrollera att det angivna värdnamnet är korrekt och att värden kan nås. | Ett nytt försök kan vara till hjälp om det finns tillfälliga anslutningsproblem. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Tjänsten kan inte behandla begäran just nu. | Klienten kan vänta en viss tid och sedan försöka igen. <br /> Se [ServerBusyException](#serverbusyexception). | Klienten kan försöka igen efter ett visst intervall. Om ett nytt försök resulterar i ett annat undantag kontrollerar du om du försöker utföra det undantaget. |
| [MeddelandeUtfors](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Allmänna meddelandeundantag som kan genereras i följande fall: Ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) med ett namn eller en sökväg som tillhör en annan entitetstyp (till exempel ett ämne). Ett försök görs att skicka ett meddelande som är större än 1 MB. Servern eller tjänsten påträffade ett fel under bearbetningen av begäran. Mer information finns i undantagsmeddelandet. Det här undantaget är vanligtvis ett tillfälligt undantag. | Kontrollera koden och se till att endast serialiserbara objekt används för meddelandetexten (eller använd en anpassad serialiserare). Kontrollera dokumentationen för de värdetyper som stöds för egenskaperna och använd endast typer som stöds. Kontrollera egenskapen [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Om det är **sant**kan du försöka igen. | Beteendet för återförsök är odefinierat och kanske inte hjälper. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Försök att skapa en entitet med ett namn som redan används av en annan entitet i tjänstnamnområdet. | Ta bort den befintliga entiteten eller välj ett annat namn för den entitet som ska skapas. | Försök igen hjälper inte. |
| [KvotExceedException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Meddelandeentiteten har nått sin högsta tillåtna storlek. Detta undantag kan inträffa om det maximala antalet mottagare (som är 5) redan har öppnats på en gruppnivå per konsument. | Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess underköer. <br /> Se [QuotaExceedException](#quotaexceededexception) | Ett nytt försök kan vara till hjälp om meddelanden har tagits bort under tiden. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Begäran om en körningsåtgärd på en inaktiverad entitet. |Aktivera entiteten. | Försök igen kan vara till hjälp om entiteten har aktiverats under tiden. |
| [Microsoft.serviceBus.Messaging MessageSizeExceedException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceedException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | En meddelandenyttolast överskrider gränsen på 1 MB. Den här gränsen på 1 MB är för det totala meddelandet, som kan innehålla systemegenskaper och eventuella .NET-omkostnader. | Minska storleken på meddelandenyttolasten och försök sedan igen. |Försök igen hjälper inte. |

### <a name="quotaexceededexception"></a>KvotExceedException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

Det här undantaget kan inträffa om det maximala antalet mottagare (5) redan har öppnats på en gruppnivå per konsument.

#### <a name="event-hubs"></a>Händelsehubbar
Event Hubs har en gräns på 20 konsumentgrupper per eventhubb. När du försöker skapa mer får du en [QuotaExceedException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en användarinitierad åtgärd tar längre tid än timeouten för åtgärden. 

För eventhubbar anges timeouten antingen som en del av anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Felmeddelandet i sig kan variera, men det innehåller alltid det timeout-värde som angetts för den aktuella åtgärden. 

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: felaktig konfiguration eller ett tillfälligt tjänstfel.

1. **Felaktig konfiguration** Timeouten för drift kan vara för liten för drifttillståndet. Standardvärdet för timeout för åtgärden i klientenSDK är 60 sekunder. Kontrollera om koden har värdet inställt på något för litet. Tillståndet för nätverks- och CPU-användningen kan påverka den tid det tar för en viss åtgärd att slutföras, så att timeout för åtgärden inte ska anges till ett litet värde.
2. **Fel vid tillfällig service** Ibland kan tjänsten Event Hubs uppleva förseningar i bearbetningen av begäranden. till exempel under perioder med hög trafik. I sådana fall kan du försöka igen efter en fördröjning tills åtgärden lyckas. Om samma åtgärd fortfarande misslyckas efter flera försök besöker du [azure-tjänststatuswebbplatsen](https://azure.microsoft.com/status/) för att se om det finns några kända avbrott i tjänsten.

### <a name="serverbusyexception"></a>ServerBusyException

En [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) eller [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) anger att en server är överbelastad. Det finns två relevanta felkoder för det här undantaget.

#### <a name="error-code-50002"></a>Felkod 50002

Det här felet kan uppstå av två orsaker:

1. Belastningen är inte jämnt fördelad över alla partitioner på händelsehubben och en partition träffar begränsningen för den lokala dataflödesenheten.
    
    Lösning: Det kan vara till hjälp att ändra partitionsdistributionsstrategin eller prova [EventHubClient.Send(eventDataWithOutPartitionKey).](/dotnet/api/microsoft.servicebus.messaging.eventhubclient)

2. Namnområdet Event Hubs har inte tillräckligt med dataflödesenheter (du kan kontrollera **fönstret Mått** i namnområdesfönstret Event Hubs i [Azure-portalen](https://portal.azure.com) för att bekräfta). Portalen visar aggregerad (1 minut) information, men vi mäter dataflödet i realtid - så det är bara en uppskattning.

    Lösning: Att öka dataflödesenheterna på namnområdet kan hjälpa. Du kan göra den här åtgärden på portalen i **fönstret Skala** på namnområdesskärmen för händelsehubbar. Du kan också använda [Automatisk uppblåsning](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Felkod 50001

Det här felet bör sällan uppstå. Det händer när behållaren som kör koden för ditt namnområde är låg på CPU - inte mer än några sekunder innan Event Hubs belastningsutjämnar börjar.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Begränsa antalet anrop till metoden GetRuntimeInformation
Azure Event Hubs stöder upp till 50 samtal per sekund till GetRuntimeInfo per sekund. Du kan få ett undantag som liknar följande när gränsen har nåtts:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Anslutnings-, certifikat- eller timeout-problem
Följande steg kan hjälpa dig att felsöka anslutnings-/certifikat-/timeout-problem för alla tjänster under *.servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Det hjälper med att kontrollera om du har IP-filtrering eller virtuellt nätverk eller certifikatkedjeproblem (vanligast när du använder java SDK).

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
- Kör följande kommando för att kontrollera om någon port är blockerad i brandväggen. Portar som används är 443 (HTTPS), 5671 (AMQP) och 9093 (Kafka). Beroende på vilket bibliotek du använder används även andra portar. Här är exempelkommandot som kontrollerar om 5671-porten är blockerad.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    På Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns återkommande anslutningsproblem kör du följande kommando för att kontrollera om det finns några ignorerade paket. Det här kommandot kommer att försöka upprätta 25 olika TCP-anslutningar var 1 sekund med tjänsten. Sedan kan du kontrollera hur många av dem som lyckades/misslyckades och även se TCP-anslutningsfördröjning. Du kan `psping` ladda ner verktyget [härifrån](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder `tnc` `ping`andra verktyg, till exempel , och så vidare. 
- Hämta en nätverksspårning om de föregående stegen inte hjälper och analyserar den med verktyg som [Wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
