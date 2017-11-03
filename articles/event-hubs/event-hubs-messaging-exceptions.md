---
title: "Händelsehubbar i Azure messaging undantag | Microsoft Docs"
description: "Lista med Händelsehubbar i Azure messaging-undantag och föreslagna åtgärder."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 1a5922506a0db4277b205ba3390c9c30034c177d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="event-hubs-messaging-exceptions"></a>Event Hubs – undantag för meddelanden
Den här artikeln innehåller några av de undantag som genereras av Azure Service Bus messaging API: er, bland annat Händelsehubbar. Den här referensen kan ändras, så kolla igen efter uppdateringar.

## <a name="exception-categories"></a>Undantag kategorier
API: er för Event Hubs skapa undantag som kan delas in i följande kategorier tillsammans med den associerade åtgärden som du kan vidta att åtgärda dem.

1. Användaren kodningsfel: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Allmän åtgärd: försök att åtgärda kod innan du fortsätter.
2. Installationen/konfigurationsfel: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Allmän åtgärd: Granska konfigurationen och ändra vid behov.
3. Tillfälligt undantag: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Allmän åtgärd: försök igen eller meddela användare.
4. Andra undantag: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Allmän åtgärd: specifika för undantagstyp; Se tabellen nedan. 

## <a name="exception-types"></a>Typer av undantag
I följande tabell visas meddelanden undantag typer och orsaker och anteckningar föreslagna åtgärder du kan vidta.

| **Undantagstyp** | **Exempel-beskrivning/orsak** | **Föreslagen åtgärd** | **Tänk på automatisk omedelbara försök** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden som kontrolleras av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Servern har slutfört den begärda åtgärden. Det kan bero på nätverket eller andra infrastruktur fördröjningar. |Systemtillståndet för konsekvens och försök igen om det behövs.<br /> Se [TimeoutException](#timeoutexception). |Försök igen kan i vissa fall. Lägg till logik i koden. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Användar-åtgärden tillåts inte i servern eller tjänsten. Se Undantagsmeddelandet mer information. Till exempel [Slutför](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) genererar det här undantaget om meddelandet togs emot i [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) läge. |Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Det hjälper inte försök igen. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Ett försök görs att anropa en åtgärd på ett objekt som redan har stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. |Kontrollera koden och kontrollera att den inte anropa åtgärder på ett borttaget objekt. |Det hjälper inte försök igen. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Den [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektet kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att tokenleverantör har skapats med korrekta värden. Kontrollera konfigurationen av tjänsten åtkomstkontroll. |Försök igen kan i vissa fall. Lägg till logik i koden. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |En eller flera argument angavs för metoden är ogiltiga. URI som levererats till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) innehåller sökvägen segment. URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) är ogiltig. Egenskapens värde är större än 32KB. |Kontrollera den anropande koden och kontrollera att argumenten är korrekta. |Det hjälper inte försök igen. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |Entitet som associeras med operationen finns inte eller har tagits bort. |Kontrollera att entiteten finns. |Det hjälper inte försök igen. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klienten kan inte upprätta en anslutning till Händelsehubb. |Kontrollera att det angivna värdnamnet är rätt och att värden kan nås. |Försök igen kan hjälpa om återkommande anslutningsproblem. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Tjänsten kan inte behandla begäran just nu. |Klienten kan vänta på en viss tidsperiod och försök igen. <br /> Se [ServerBusyException](#serverbusyexception). |Klienten kan försöka igen efter vissa intervall. Om ett nytt försök resulterar i en annan undantag, kontrollera försök beteendet för undantaget. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Generisk messaging undantag som utlöses i följande fall: ett försök görs att skapa en [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) med ett namn eller en sökväg som hör till en annan entitetstyp (till exempel ett ämne). Ett försök görs att skicka ett meddelande som är större än 256KB. Servern eller tjänsten påträffade ett fel under bearbetningen av begäran. Se Undantagsmeddelandet mer information. Detta är vanligtvis ett tillfälligt undantag. |Kontrollera koden och se till att endast serialiserbara objekt används för meddelandetexten (eller använda en anpassad serialisering). I dokumentationen för värdetyper som stöds av egenskaperna och endast använda stöds typer. Kontrollera den [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) egenskapen. Om det är **SANT**, du kan försöka igen. |Försök igen är odefinierad och inte kan hjälpa. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Försök att skapa en entitet med ett namn som redan används av en annan entitet i det namnområdet för tjänsten. |Ta bort den befintliga entiteten eller välj ett annat namn för enheten som ska skapas. |Det hjälper inte försök igen. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Meddelanden entiteten har uppnått den maximala storleken. Detta kan inträffa om det maximala antalet mottagare (som är 5) har redan öppnats i en per konsumenten gruppnivå. |Frigör utrymme i enheten genom att ta emot meddelanden från enheten eller dess underordnade köer. <br /> Se [QuotaExceededException](#quotaexceededexception) |Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Begäran om en körningsåtgärd på en inaktiverad entitet. |Aktivera entiteten. |Försök igen kan hjälpa om entiteten har aktiverats under tiden. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Nyttolast för ett meddelande är längre än 256 kB. Observera att 256 kB-gränsen är totala meddelandelagringsstorlek, vilket kan innefatta Systemegenskaper och eventuella kostnader för .NET. |Minska storleken på nyttolast meddelande och försök igen. |Det hjälper inte försök igen. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger en kvot för en viss enhet har överskridits.

Detta kan inträffa om det maximala antalet mottagare (5) har redan öppnats i en per konsumenten gruppnivå.

### <a name="event-hubs"></a>Händelsehubbar
Händelsehubbar är begränsad till 20 konsumentgrupper per Event Hub. När du försöker skapa flera får du en [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

Händelsehubbar, timeout-värdet har angetts för antingen som en del i anslutningssträngen eller via [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Felmeddelandet själva kan variera, men innehåller alltid timeout-värdet som angetts för den aktuella åtgärden. 

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till detta fel: felaktig konfiguration eller ett tillfälligt tjänstfel.

1. **Felaktig konfiguration** åtgärden kanske för liten för driftsvillkor. Standardvärdet för tidsgräns för åtgärd i klient-SDK är 60 sekunder. Kontrollera om din kod har värdet som angetts till något som är för liten. Observera att villkoret för nätverket och CPU-användning kan påverka den tid det tar för en viss åtgärd ska slutföras, så att åtgärden inte ska anges till ett mycket lågt värde.
2. **Tillfälligt tjänstfel** händelsehubbtjänsten kan ibland uppstå fördröjningar i bearbetning av begäranden, till exempel under perioder med hög trafik. I sådana fall kan du försöka utföra åtgärden efter en stund tills åtgärden har lyckats. Om samma åtgärd fortfarande misslyckas efter flera försök, går du till den [plats i Azure service status](https://azure.microsoft.com/status/) om det finns några kända serviceavbrott.

## <a name="serverbusyexception"></a>ServerBusyException

En [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) eller [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) anger att en server är överbelastad. Det finns två relevanta felkoder för det här undantaget.

### <a name="error-code-50002"></a>Felkoden 50002

Det här felet kan bero på något av två skäl:

1. Belastningen är inte jämnt fördelad över alla partitioner i Händelsehubben och en partition träffar lokala genomströmning enhet begränsning.
    
    Lösning: Ändra distributionsstrategi partition eller försöka [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) kan hjälpa.

2. Händelsehubbar namnområdet har inte tillräcklig genomflödesenheter (du kan kontrollera den **mått** blad i Händelsehubbar namnområde bladet i den [Azure-portalen](https://portal.azure.com) att bekräfta). Observera att portalen visar aggregerade (1 minut) information, men vi mäter genomflöde i realtid – så att det är endast en uppskattning.

    Lösning: Öka genomflödesenheter på namnområdet kan hjälpa. Du kan göra detta på portalen i den **skala** bladet av bladet Händelsehubbar namnområde.

### <a name="error-code-50001"></a>Felkoden 50001

Det här felet ska sällan ske. Det händer när behållaren köra kod för namnområdet är låg på CPU – inte fler än några sekunder innan den Händelsehubbar belastningsutjämnare börjar.


## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
