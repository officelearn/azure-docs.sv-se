---
title: Fel söknings guide – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller en lista över undantag och föreslagna åtgärder för Azure Event Hubs Messaging.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309786"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Fel söknings guide för Azure Event Hubs
Den här artikeln innehåller några av de .NET-undantag som genereras av Event Hubs .NET Framework API: er och även andra tips för fel söknings problem. 

## <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs meddelande undantag – .NET
I det här avsnittet listas de .NET-undantag som genereras av .NET Framework API: er. 

### <a name="exception-categories"></a>Undantags kategorier

Event Hubs .NET-API: er genererar undantag som kan ingå i följande kategorier, tillsammans med den associerade åtgärden som du kan vidta för att försöka åtgärda dem.

1. Användar kodnings fel: [system. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [system. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [system. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [system. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installations-/konfigurations fel: [Microsoft. Service Bus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [system. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: Granska konfigurationen och ändra om det behövs.
3. Tillfälliga undantag: [Microsoft. Service Bus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. Service Bus. Messaging. ServerBusyException](#serverbusyexception), [Microsoft. Azure. EventHubs. ServerBusyException](#serverbusyexception), [Microsoft. Service Bus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Allmän åtgärd: försök utföra åtgärden på nytt eller meddela användare.
4. Andra undantag: [system. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [system. TimeoutException](#timeoutexception), [Microsoft. Service Bus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft. Service Bus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Allmän åtgärd: särskilt för undantags typen; Se tabellen i följande avsnitt. 

### <a name="exception-types"></a>Undantags typer
I följande tabell visas meddelande undantags typer och deras orsaker, samt förslag på åtgärder som du kan vidta.

| Undantags typ | Beskrivning/orsak/exempel | Föreslagen åtgärd | Observera vid automatisk/omedelbar återförsök |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, vilken styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern kan ha slutfört den begärda åtgärden. Detta undantag kan inträffa på grund av fördröjningar i nätverket eller andra infrastrukturer. |Kontrol lera system tillstånd för konsekvens och försök igen om det behövs.<br /> Se [TimeoutException](#timeoutexception). | Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Den begärda användar åtgärden tillåts inte i servern eller tjänsten. Mer information finns i undantags meddelandet. Till [exempel genererar detta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) undantag om meddelandet togs emot i [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -läge. | Kontrol lera koden och dokumentationen. Se till att den begärda åtgärden är giltig. | Det går inte att göra ett nytt försök. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Ett försök gjordes att anropa en åtgärd för ett objekt som redan har stängts, avbrutits eller tagits bort. I sällsynta fall tas den omgivande transaktionen redan bort. | Kontrol lera koden och se till att den inte anropar åtgärder på ett borttaget objekt. | Det går inte att göra ett nytt försök. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -objektet kunde inte hämta en token, token är ogiltig eller så innehåller token inte de anspråk som krävs för att utföra åtgärden. | Kontrol lera att token-providern har skapats med rätt värden. Kontrol lera konfigurationen av Access Control Service. | Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Ett eller flera argument som angavs för metoden är ogiltiga. URI: n som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller Sök vägs segment (n). URI-schemat som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltigt. Egenskap svärdet är större än 32 KB. | Kontrol lera anrops koden och se till att argumenten är korrekta. | Det går inte att göra ett nytt försök. |
| [Microsoft. Service Bus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entiteten som är kopplad till åtgärden finns inte eller har tagits bort. | Kontrol lera att entiteten finns. | Det går inte att göra ett nytt försök. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klienten kan inte upprätta en anslutning till Event Hub. |Kontrol lera att det angivna värd namnet är rätt och att värden kan kontaktas. | Försök igen kan vara till hjälp om det finns tillfälliga anslutnings problem. |
| [Microsoft. Service Bus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Tjänsten kan för närvarande inte bearbeta begäran. | Klienten kan vänta en stund och sedan försöka igen. <br /> Se [ServerBusyException](#serverbusyexception). | Klienten kan försöka igen efter ett visst intervall. Om ett återförsök resulterar i ett annat undantag, kontrol lera beteendet för återförsök i detta undantag. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Allmänt meddelande undantag som kan uppstå i följande fall: ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) med ett namn eller en sökväg som tillhör en annan entitetstyp (till exempel ett ämne). Ett försök görs att skicka ett meddelande som är större än 1 MB. Ett fel påträffades av servern eller tjänsten under bearbetningen av begäran. Mer information finns i undantags meddelandet. Detta undantag är vanligt vis ett tillfälligt undantag. | Kontrol lera koden och se till att endast serialiserbara objekt används för meddelande texten (eller Använd en anpassad serialiserare). Kontrol lera dokumentationen för de värde typer som stöds och Använd endast typer som stöds. Kontrol lera egenskapen [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Om det är **Sant**kan du försöka igen. | Beteendet för återförsök är odefinierat och kanske inte kan hjälpa dig. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Försök att skapa en entitet med ett namn som redan används av en annan entitet i tjänstens namnrymd. | Ta bort den befintliga entiteten eller Välj ett annat namn för entiteten som ska skapas. | Det går inte att göra ett nytt försök. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Meddelande enheten har uppnått maximalt tillåten storlek. Detta undantag kan inträffa om det maximala antalet mottagare (som är 5) redan har öppnats på en grupp per konsument nivå. | Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess under köer. <br /> Se [QuotaExceededException](#quotaexceededexception) | Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Begäran om körnings åtgärder på en inaktive rad entitet. |Aktivera entiteten. | Försök igen kan hjälpa om entiteten har Aktiver ATS i Interim. |
| [Microsoft. Service Bus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | En meddelande nytto Last överskrider gränsen på 1 MB. Den här gränsen för 1 MB är för det totala meddelandet, som kan innehålla system egenskaper och alla .NET-kostnader. | Minska storleken på meddelandets nytto last och försök sedan igen. |Det går inte att göra ett nytt försök. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

Detta undantag kan inträffa om det maximala antalet mottagare (5) redan har öppnats på en grupp nivå per konsument.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs har en gräns på 20 konsument grupper per Event Hub. När du försöker skapa fler får du en [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initierats av användaren tar längre tid än tids gränsen för åtgärden. 

För Event Hubs anges tids gränsen antingen som en del av anslutnings strängen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Själva fel meddelandet kan variera, men det innehåller alltid det timeout-värde som har angetts för den aktuella åtgärden. 

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: felaktig konfiguration eller tillfälligt tjänst fel.

1. **Felaktig konfiguration** Åtgärdens tids gräns kan vara för liten för drifts villkoret. Standardvärdet för åtgärds tids gränsen i klient-SDK är 60 sekunder. Kontrol lera om din kod har värdet inställt på för litet. Villkoret för nätverket och CPU-användningen kan påverka hur lång tid det tar för en viss åtgärd att slutföras, så tids gränsen för åtgärden bör inte anges till ett litet värde.
2. **Tillfälligt tjänst fel** Ibland kan Event Hubs tjänsten uppleva fördröjningar i bearbetnings begär Anden. till exempel under perioder med hög trafik. I sådana fall kan du försöka utföra åtgärden igen efter en fördröjning tills åtgärden har slutförts. Om samma åtgärd fortfarande Miss lyckas efter flera försök kan du gå till [Azure-tjänstens status webbplats](https://azure.microsoft.com/status/) för att se om det finns några kända drift avbrott.

### <a name="serverbusyexception"></a>ServerBusyException

En [Microsoft. Service Bus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) eller [Microsoft. Azure. EventHubs. ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) anger att en server är överbelastad. Det finns två relevanta fel koder för det här undantaget.

#### <a name="error-code-50002"></a>Felkod 50002

Felet kan bero på en av följande orsaker:

1. Belastningen fördelas inte jämnt över alla partitioner i händelsehubben, och en partition träffar den lokala begränsningen för data flödet.
    
    Lösning: att ändra distributions strategin för partitionen eller testa [EventHubClient. send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kan hjälpa dig.

2. Event Hubs-namnrymden har inte tillräckligt med data flödes enheter (du kan kontrol lera **måtten** på skärmen i Event Hubs namn områdes fönstret i [Azure Portal](https://portal.azure.com) för att bekräfta). Portalen visar sammanställd (1 minut) information, men vi mäter data flödet i real tid – så det är bara en uppskattning.

    Lösning: att öka data flödes enheterna i namn området kan hjälpa dig. Du kan utföra den här åtgärden på portalen i fönstret **skala** på skärmen Event Hubs namn område. Du kan också använda [Automatisk](event-hubs-auto-inflate.md)ökning.

#### <a name="error-code-50001"></a>Felkod 50001

Det här felet bör sällan uppstå. Det inträffar när behållaren som kör kod för namn området har ont om CPU – inte mer än några sekunder innan den Event Hubs belastningsutjämnaren börjar.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Begränsning av anrop till metoden GetRuntimeInformation
Azure Event Hubs stöder upp till 50 anrop per sekund till GetRuntimeInfo per sekund. Du kan få ett undantag som liknar följande när gränsen nås:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problem med anslutning, certifikat eller tids gräns
Följande steg kan hjälpa dig med fel sökning av problem med anslutning/certifikat/tids gräns för alla tjänster under *. servicebus.windows.net. 

- Bläddra till eller [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Det hjälper till med att kontrol lera om du har problem med IP-filtrering eller problem med virtuella nätverk eller certifikat kedjan (vanligt vis när du använder Java SDK).

    Ett exempel på ett slutfört meddelande:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Ett exempel på fel meddelande:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Kör följande kommando för att kontrol lera om någon port är blockerad i brand väggen. Portarna som används är 443 (HTTPS), 5671 (AMQP) och 9093 (Kafka). Beroende på vilket bibliotek du använder används även andra portar. Här är exempel kommandot som kontrollerar om 5671-porten är blockerad.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    I Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- När det finns tillfälliga anslutnings problem kör du följande kommando för att kontrol lera om det finns några paket som ignoreras. Med det här kommandot görs ett försök att upprätta 25 olika TCP-anslutningar varje sekund med tjänsten. Sedan kan du kontrol lera hur många av dem som har lyckats/misslyckats och även se TCP-anslutningssträngen. Du kan hämta `psping` verktyget [härifrån.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Du kan använda motsvarande kommandon om du använder andra verktyg som `tnc`, `ping`och så vidare. 
- Få en nätverks spårning om föregående steg inte hjälper och analyseras med verktyg som [wireshark](https://www.wireshark.org/). Kontakta [Microsoft Support](https://support.microsoft.com/) om det behövs. 

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en Händelsehubben](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
