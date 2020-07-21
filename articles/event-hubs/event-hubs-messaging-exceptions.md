---
title: Azure-Event Hubs – undantag
description: Den här artikeln innehåller en lista över undantag och föreslagna åtgärder för Azure Event Hubs Messaging.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a93daa88c468a22838a6f9012f0c4622447f5555
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512375"
---
# <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs meddelande undantag – .NET
I det här avsnittet listas de .NET-undantag som genereras av .NET Framework API: er. 

## <a name="exception-categories"></a>Undantags kategorier

Event Hubs .NET-API: er genererar undantag som kan ingå i följande kategorier, tillsammans med den associerade åtgärden som du kan vidta för att försöka åtgärda dem:

 - Användar kodnings fel: 
 
   - [System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1)
   - [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1)
   - [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1)
   - [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1)
   
   Allmän åtgärd: försök att åtgärda koden innan du fortsätter.
 
 - Installations-/konfigurations fel: 
 
   - [Microsoft. Service Bus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1)
   
   Allmän åtgärd: Granska konfigurationen och ändra om det behövs.
   
 - Tillfälliga undantag: 
 
   - [Microsoft. Service Bus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft. Service Bus. Messaging. ServerBusyException](#serverbusyexception)
   - [Microsoft. Azure. EventHubs. ServerBusyException](#serverbusyexception)
   - [Microsoft. Service Bus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Allmän åtgärd: försök utföra åtgärden på nytt eller meddela användare.
 
 - Andra undantag: 
 
   - [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1)
   - [System. TimeoutException](#timeoutexception)
   - [Microsoft. Service Bus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft. Service Bus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Allmän åtgärd: särskilt för undantags typen; Se tabellen i följande avsnitt. 

## <a name="exception-types"></a>Undantags typer
I följande tabell visas meddelande undantags typer och deras orsaker, samt förslag på åtgärder som du kan vidta.

| Undantags typ | Beskrivning/orsak/exempel | Föreslagen åtgärd | Observera vid automatisk/omedelbar återförsök |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, vilken styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Servern kan ha slutfört den begärda åtgärden. Detta undantag kan inträffa på grund av fördröjningar i nätverket eller andra infrastrukturer. |Kontrol lera system tillstånd för konsekvens och försök igen om det behövs.<br /> Se [TimeoutException](#timeoutexception). | Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1) |Den begärda användar åtgärden tillåts inte i servern eller tjänsten. Mer information finns i undantags meddelandet. Till [exempel genererar detta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) undantag om meddelandet togs emot i [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) -läge. | Kontrol lera koden och dokumentationen. Se till att den begärda åtgärden är giltig. | Det går inte att göra ett nytt försök. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1) | Ett försök gjordes att anropa en åtgärd för ett objekt som redan har stängts, avbrutits eller tagits bort. I sällsynta fall tas den omgivande transaktionen redan bort. | Kontrol lera koden och se till att den inte anropar åtgärder på ett borttaget objekt. | Det går inte att göra ett nytt försök. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) -objektet kunde inte hämta en token, token är ogiltig eller så innehåller token inte de anspråk som krävs för att utföra åtgärden. | Kontrol lera att token-providern har skapats med rätt värden. Kontrol lera konfigurationen av Access Control Service. | Försök kan i vissa fall hjälpa dig. Lägg till logik för omprövning till kod. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1) | Ett eller flera argument som angavs för metoden är ogiltiga. URI: n som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) innehåller Sök vägs segment (n). URI-schemat som angavs för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) är ogiltigt. Egenskap svärdet är större än 32 KB. | Kontrol lera anrops koden och se till att argumenten är korrekta. | Det går inte att göra ett nytt försök. |
| [Microsoft. Service Bus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entiteten som är kopplad till åtgärden finns inte eller har tagits bort. | Kontrol lera att entiteten finns. | Det går inte att göra ett nytt försök. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klienten kan inte upprätta en anslutning till Event Hub. |Kontrol lera att det angivna värd namnet är rätt och att värden kan kontaktas. | Försök igen kan vara till hjälp om det finns tillfälliga anslutnings problem. |
| [Microsoft. Service Bus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Tjänsten kan för närvarande inte bearbeta begäran. | Klienten kan vänta en stund och sedan försöka igen. <br /> Se [ServerBusyException](#serverbusyexception). | Klienten kan försöka igen efter ett visst intervall. Om ett återförsök resulterar i ett annat undantag, kontrol lera beteendet för återförsök i detta undantag. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Allmänt meddelande undantag som kan uppstå i följande fall: ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) med ett namn eller en sökväg som tillhör en annan entitetstyp (till exempel ett ämne). Ett försök görs att skicka ett meddelande som är större än 1 MB. Ett fel påträffades av servern eller tjänsten under bearbetningen av begäran. Mer information finns i undantags meddelandet. Detta undantag är vanligt vis ett tillfälligt undantag. | Kontrol lera koden och se till att endast serialiserbara objekt används för meddelande texten (eller Använd en anpassad serialiserare). Kontrol lera dokumentationen för de värde typer som stöds och Använd endast typer som stöds. Kontrol lera egenskapen [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Om det är **Sant**kan du försöka igen. | Beteendet för återförsök är odefinierat och kanske inte kan hjälpa dig. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Försök att skapa en entitet med ett namn som redan används av en annan entitet i tjänstens namnrymd. | Ta bort den befintliga entiteten eller Välj ett annat namn för entiteten som ska skapas. | Det går inte att göra ett nytt försök. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Meddelande enheten har uppnått maximalt tillåten storlek. Detta undantag kan inträffa om det maximala antalet mottagare (som är 5) redan har öppnats på en grupp per konsument nivå. | Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess under köer. <br /> Se [QuotaExceededException](#quotaexceededexception) | Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Begäran om körnings åtgärder på en inaktive rad entitet. |Aktivera entiteten. | Försök igen kan hjälpa om entiteten har Aktiver ATS i Interim. |
| [Microsoft. Service Bus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | En meddelande nytto Last överskrider gränsen på 1 MB. Den här gränsen för 1 MB är för det totala meddelandet, som kan innehålla system egenskaper och alla .NET-kostnader. | Minska storleken på meddelandets nytto last och försök sedan igen. |Det går inte att göra ett nytt försök. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

Detta undantag kan inträffa om det maximala antalet mottagare (5) redan har öppnats på en grupp nivå per konsument.

### <a name="event-hubs"></a>Event Hubs
Event Hubs har en gräns på 20 konsument grupper per Event Hub. När du försöker skapa fler får du en [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) anger att en åtgärd som initierats av användaren tar längre tid än tids gränsen för åtgärden. 

För Event Hubs anges tids gränsen antingen som en del av anslutnings strängen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Själva fel meddelandet kan variera, men det innehåller alltid det timeout-värde som har angetts för den aktuella åtgärden. 

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet: felaktig konfiguration eller tillfälligt tjänst fel.

- **Felaktig konfiguration** Åtgärdens tids gräns kan vara för liten för drifts villkoret. Standardvärdet för åtgärds tids gränsen i klient-SDK är 60 sekunder. Kontrol lera om din kod har värdet inställt på för litet. Villkoret för nätverket och CPU-användningen kan påverka hur lång tid det tar för en viss åtgärd att slutföras, så tids gränsen för åtgärden bör inte anges till ett litet värde.
- **Tillfälligt tjänst fel** Ibland kan Event Hubs tjänsten uppleva fördröjningar i bearbetnings begär Anden. till exempel under perioder med hög trafik. I sådana fall kan du försöka utföra åtgärden igen efter en fördröjning tills åtgärden har slutförts. Om samma åtgärd fortfarande Miss lyckas efter flera försök kan du gå till [Azure-tjänstens status webbplats](https://azure.microsoft.com/status/) för att se om det finns några kända drift avbrott.

## <a name="serverbusyexception"></a>ServerBusyException

En [Microsoft. Service Bus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) eller [Microsoft. Azure. EventHubs. ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) anger att en server är överbelastad. Det finns två relevanta fel koder för det här undantaget.

### <a name="error-code-50002"></a>Felkod 50002
Felet kan bero på en av följande orsaker:

- Belastningen fördelas inte jämnt över alla partitioner i händelsehubben, och en partition träffar den lokala begränsningen för data flödet.
    
    **Lösning**: att ändra distributions strategin för partitionen eller testa [EventHubClient. send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) kan hjälpa dig.

- Event Hubs-namnrymden har inte tillräckligt med data flödes enheter (du kan kontrol lera **måtten** på skärmen i Event Hubs namn områdes fönstret i [Azure Portal](https://portal.azure.com) för att bekräfta). Portalen visar sammanställd (1 minut) information, men vi mäter data flödet i real tid – så det är bara en uppskattning.

    **Lösning**: att öka data flödes enheterna i namn området kan hjälpa dig. Du kan utföra den här åtgärden på portalen i fönstret **skala** på skärmen Event Hubs namn område. Du kan också använda [Automatisk](event-hubs-auto-inflate.md)ökning.

### <a name="error-code-50001"></a>Felkod 50001

Det här felet bör sällan uppstå. Det inträffar när behållaren som kör kod för namn området har ont om CPU – inte mer än några sekunder innan den Event Hubs belastningsutjämnaren börjar.

**Lösning**: begränsa anrop till GetRuntimeInformation-metoden. Azure Event Hubs stöder upp till 50 anrop per sekund till GetRuntimeInfo per sekund. Du kan få ett undantag som liknar följande när gränsen nås:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](./event-hubs-about.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
