---
title: Fel söknings guide – Azure Event Hubs | Microsoft Docs
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
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: 26056e9b52ea319856505db837c67dc68b2f4aa6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157295"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Fel söknings guide för Azure Event Hubs
Den här artikeln innehåller några av de .NET-undantag som genereras av Event Hubs .NET Framework API: er och även andra tips för fel söknings problem. 

## <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs meddelande undantag – .NET
I det här avsnittet listas de .NET-undantag som genereras av .NET Framework API: er. 

### <a name="exception-categories"></a>Undantag kategorier

Event Hubs .NET-API: er genererar undantag som kan ingå i följande kategorier, tillsammans med den associerade åtgärden som du kan vidta för att försöka åtgärda dem.

1. Användaren kodningsfel: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
2. Installationen/konfigurationsfel: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: granskar du konfigurationen och ändra vid behov.
3. Tillfälliga undantag: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Allmän åtgärd: försök igen eller meddela användare.
4. Andra undantag: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Allmän åtgärd: specifika för typ av undantag; Se tabellen nedan. 

### <a name="exception-types"></a>Undantagstyper
I följande tabell visas meddelanden undantagstyper och orsaker och anteckningar föreslagna åtgärder du kan vidta.

| Undantagstyp | Exempel-beskrivning/orsak | Föreslagen åtgärd | Notera på automatisk/omedelbart återförsök |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, vilken styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern har slutfört den begärda åtgärden. Detta undantag kan inträffa på grund av fördröjningar i nätverket eller andra infrastrukturer. |Kontrollera systemtillstånd för att få konsekvens och försök igen om det behövs.<br /> Se [TimeoutException](#timeoutexception). | Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Den begärda användar åtgärden tillåts inte i servern eller tjänsten. Se Undantagsmeddelandet mer information. Till exempel [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) genererar det här undantaget om meddelandet har tagits emot i [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge. | Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. | Det går inte att göra ett nytt försök. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Det görs ett försök att anropa en åtgärd på ett objekt som har redan stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. | Kontrol lera koden och se till att den inte anropar åtgärder på ett borttaget objekt. | Det går inte att göra ett nytt försök. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -objektet kunde inte hämta en token, token är ogiltig eller så innehåller token inte de anspråk som krävs för att utföra åtgärden. | Kontrollera att Tokenleverantören har skapats med korrekta värden. Kontrol lera konfigurationen av Access Control Service. | Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Ett eller flera argument som skickades till metoden är ogiltiga. URI: N som har angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller sökvägen rörelsegren. URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltig. Egenskapens värde är större än 32 KB. | Kontrollera den anropande koden och se till att argumenten är korrekta. | Försök inte hjälper att. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entitet som är associerade med åtgärden finns inte eller har tagits bort. | Kontrollera att enheten finns. | Försök inte hjälper att. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klienten kan inte upprätta en anslutning till Event Hub. |Kontrollera att det angivna värdnamnet är rätt och värden kan nås. | Återförsök kan hjälpa dig att om det finns tillfälliga anslutningsproblem. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Tjänsten går inte att bearbeta begäran just nu. | Klienten kan vänta på en viss tidsperiod och försök igen. <br /> Se [ServerBusyException](#serverbusyexception). | Klienten kan försöka igen efter vissa intervall. Om ett nytt försök resulterar i en annan undantag, kontrollera funktionssätt för återförsök för undantaget. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Allmänna meddelanden undantag som kan uppkomma i följande fall: görs ett försök att skapa en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) med hjälp av ett namn eller sökväg som hör till en annan person-typ (till exempel ett ämne). Ett försök görs att skicka ett meddelande som är större än 1 MB. Servern eller -tjänsten påträffade ett fel under bearbetningen av begäran. Se Undantagsmeddelandet mer information. Det här undantaget är vanligtvis ett tillfälligt undantag. | Kontrollera koden och se till att endast serialiserbara objekt används för meddelandets brödtext (eller använda en anpassad serialiserare). I dokumentationen för olika egenskaperna värde som stöds och endast använda stöds typer. Kontrollera den [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) egenskapen. Om det är **SANT**, du kan försöka igen. | Funktionssätt för återförsök har inte definierats och kan inte. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Försöket att skapa en entitet med ett namn som redan används av en annan entitet i det tjänstnamnområdet. | Ta bort befintlig entitet eller välj ett annat namn för entitet som ska skapas. | Försök inte hjälper att. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Meddelandeentitet har nått maximal tillåten storlek. Detta undantag kan inträffa om det maximala antalet mottagare (vilket är 5) har redan öppnats i en per-consumer gruppnivå. | Skapa utrymme i entiteten genom att ta emot meddelanden från enheten eller dess underköer. <br /> Se [QuotaExceededException](#quotaexceededexception) | Återförsök kan hjälpa dig att om meddelanden har tagits bort under tiden. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Begäran om en åtgärd för körning på en inaktiverad enhet. |Aktivera entiteten. | Återförsök kan hjälpa dig att om entiteten har aktiverats under tiden. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | En meddelande nytto Last överskrider gränsen på 1 MB. Den här gränsen för 1 MB är för det totala meddelandet, som kan innehålla system egenskaper och alla .NET-kostnader. | Minska storleken på meddelandet nyttolasten och försök sedan igen. |Försök inte hjälper att. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

Detta undantag kan inträffa om det maximala antalet mottagare (5) har redan öppnats i en per-consumer gruppnivå.

#### <a name="event-hubs"></a>Händelsehubbar
Händelsehubbar har en gräns på 20 konsumentgrupper per Event Hub. När du försöker skapa mer får du en [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

För Event Hubs, timeout-värdet anges antingen som en del av anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Felmeddelandet själva kan variera, men den innehåller alltid timeout-värdet som angetts för den aktuella åtgärden. 

#### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: felaktig konfiguration eller ett tillfälligt tjänstfel.

1. **Felaktig konfiguration** tidsgränsen för åtgärden kanske för liten för den operativa villkoren. Standardvärdet för tidsgräns för åtgärd i klient-SDK är 60 sekunder. Kontrollera om du vill se om din kod har värdet inställt på något som är för liten. Villkoret för nätverket och CPU-användningen kan påverka hur lång tid det tar för en viss åtgärd att slutföras, så tids gränsen för åtgärden bör inte anges till ett litet värde.
2. **Tillfälliga tjänstfel** ibland händelsehubbtjänsten kan uppstår fördröjningar i bearbetning av begäranden, till exempel under perioder med hög belastning. I sådana fall kan du försöka utföra åtgärden efter en stund tills åtgärden har lyckats. Om samma åtgärden fortfarande misslyckas efter flera försök, gå till den [plats för Azure service-status](https://azure.microsoft.com/status/) att se om det finns några kända driftstopp.

### <a name="serverbusyexception"></a>ServerBusyException

En [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) eller [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) anger att en server är överbelastad. Det finns två relevanta felkoder för det här undantaget.

#### <a name="error-code-50002"></a>Felkod 50002

Det här felet kan bero på något av två skäl:

1. Belastningen fördelas inte jämnt över alla partitioner i händelsehubben, och en partition träffar den lokala begränsningen för data flödet.
    
    Lösning: Uppdatering distributionsstrategi partition eller försök [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kan hjälpa dig att.

2. Event Hubs-namnrymden har inte tillräckligt med data flödes enheter (du kan kontrol lera **måtten** på skärmen i Event Hubs namn områdes fönstret i [Azure Portal](https://portal.azure.com) för att bekräfta). Portalen visar sammanställd (1 minut) information, men vi mäter data flödet i real tid – så det är bara en uppskattning.

    Lösning: Öka dataflödesenheter på namnområdet kan. Du kan göra den här åtgärden på portalen, i den **skala** fönstret på skärmen för Event Hubs-namnområdet. Du kan också använda [automatisk ökning](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Felkod 50001

Det här felet ska skrivfel uppstår. Det händer när den behållare som körs koden för ditt namnområde har brist på CPU – inte mer än ett par sekunder innan Event Hubs belastningsutjämnare börjar.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Begränsning av anrop till metoden GetRuntimeInformation
Azure Event Hubs stöder upp till 50 anrop per sekund till GetRuntimeInfo per sekund. Du kan få ett undantag som liknar följande när gränsen nås:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

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

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
