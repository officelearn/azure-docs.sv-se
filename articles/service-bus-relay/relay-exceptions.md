---
title: Azure Relay-undantag och hur du löser dem | Microsoft Docs
description: Lista över Azure Relay-undantag och föreslagna åtgärder som du kan vidta för att lösa dem.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749044"
---
# <a name="azure-relay-exceptions"></a>Azure Relay-undantag

Den här artikeln innehåller vissa undantag som kan genereras av API: er för Azure Relay. Den här referensen kan komma att ändras, så kom tillbaka för uppdateringar.

## <a name="exception-categories"></a>Undantag kategorier

Relay-API: er skapa undantag som kan delas in i följande kategorier. Listan finns även föreslagna åtgärder som du kan vidta för att lösa undantagen.

*   **Användaren kodningsfel**: System.ArgumentException, [System.InvalidOperationException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Allmän åtgärd**: Försök att åtgärda koden innan du fortsätter.
*   **Installationen/konfigurationsfel**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Allmän åtgärd**: Granska din konfiguration. Om det behövs kan du ändra konfigurationen.
*   **Tillfälliga undantag**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Allmän åtgärd**: Försök igen eller meddela användare.
*   **Andra undantag**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Allmän åtgärd**: Specifika för typ av undantag. Se tabellen nedan. 

## <a name="exception-types"></a>Undantagstyper

I följande tabell visas meddelanden undantagstyper och deras orsaker. Den också noterar föreslagna åtgärder som du kan vidta för att lösa undantagen.

| **Undantagstyp** | **Beskrivning** | **Föreslagen åtgärd** | **Observera vid automatisk eller omedelbara återförsök** |
| --- | --- | --- | --- |
| [Timeout](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden som kontrolleras av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Servern kan slutföra den begärda åtgärden. Detta kan inträffa på grund av nätverks- eller andra fördröjningar i infrastrukturen. |Kontrollera system-tillstånd för att få konsekvens och försök sedan, om det behövs. Se [TimeoutException](#timeoutexception). |Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [Ogiltig åtgärd](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Begärd användare-åtgärden tillåts inte i servern eller -tjänsten. Se Undantagsmeddelandet mer information. |Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Försök inte hjälper att. |
| [Åtgärden avbröts](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Det görs ett försök att anropa en åtgärd på ett objekt som har redan stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. |Kontrollera koden och se till att den inte anropar åtgärder på ett borttaget objekt. |Försök inte hjälper att. |
| [Obehörig åtkomst](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Den [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att Tokenleverantören har skapats med korrekta värden. Kontrollera konfigurationen av tjänsten Access Control. |Återförsök kan hjälpa dig att i vissa fall. Lägg till logik för omprövning i kod. |
| [Undantagsfel för argumentet](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argumentet Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumentet utanför intervallet](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |En eller flera av följande har inträffat:<br />Ett eller flera argument som skickades till metoden är ogiltiga.<br /> URI: N som har angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) innehåller en eller flera sökvägssegment.<br />URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) är ogiltig. <br />Egenskapens värde är större än 32 KB. |Kontrollera den anropande koden och se till att argumenten är korrekta. |Försök inte hjälper att. |
| [Servern är upptagen](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Tjänsten går inte att bearbeta begäran just nu. |Klienten kan vänta på en viss tidsperiod och försök igen. |Klienten kan försök efter en viss tid. Om ett återförsök resultat i en annan undantag, kontrollera återförsöksbeteendet av detta undantag. |
| [Kvoten har överskridits](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Meddelandeentitet har nått maximal tillåten storlek. |Skapa utrymme i entiteten genom att ta emot meddelanden från enheten eller dess underköer. Se [QuotaExceededException](#quotaexceededexception). |Återförsök kan hjälpa dig att om meddelanden har tagits bort under tiden. |
| [Meddelandestorlek har överskridits](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Nyttolast för ett meddelande är längre än 256 KB. Observera att den totala meddelandestorleken 256 KB-gränsen. Totalt antal meddelandestorleken kan innehålla Systemegenskaper och eventuella kostnader för Microsoft .NET. |Minska storleken på meddelandet nyttolasten och försök sedan igen. |Försök inte hjälper att. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

För relä, det här undantaget omsluter den [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), vilket betyder att det maximala antalet lyssnare har överskridits för den här slutpunkten. Detta anges i den **MaximumListenersPerEndpoint** värdet för Undantagsmeddelandet.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

Kontrollera värdet för den [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) egenskapen. Når den här gränsen kan även orsaka en [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

För Relay, kan du få undantag där tidsgränsen överskridits när du först öppnar en avsändare relay-anslutning. Det finns två vanliga orsaker till det här undantaget:

*   Den [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) värdet kan vara för litet (om du även är som en del av en sekund).
* En lokal relälyssnaren kanske inte svarar (eller det kan uppstå brandväggsproblem för regler som förhindrar att lyssnare godtar nya klientanslutningar) och [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) värdet är mindre än ungefär 20 sekunder.

Exempel:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet:

*   **Felaktig konfiguration**
    
    Tidsgränsen för åtgärden kanske för liten för den operativa villkoren. Standardvärdet för tidsgräns för åtgärd i klient-SDK är 60 sekunder. Kontrollera om värdet i din kod har angetts till något som är för liten. Observera att CPU-användning och villkoret för nätverket kan påverka den tid det tar för en åtgärd ska slutföras. Det är en bra idé inte att ange tidsgränsen för åtgärden till ett värde som är mycket liten.
*   **Tillfälliga tjänstfel**

    Den vidarebefordrande tjänsten kan ibland uppstå fördröjningar i bearbetning av begäranden. Detta kan inträffa, till exempel under perioder med hög belastning. Om detta inträffar, försök igen efter en stund tills åtgärden har lyckats. Om samma åtgärden fortfarande misslyckas efter flera försök, kontrollerar du den [plats för Azure service-status](https://azure.microsoft.com/status/) att se om det finns kända tjänstavbrott.

## <a name="next-steps"></a>Nästa steg
* [Azure Relay vanliga frågor och svar](relay-faq.md)
* [Skapa ett relay-namnområde](relay-create-namespace-portal.md)
* [Kom igång med Azure Relay och .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Azure Relay och nod](relay-hybrid-connections-node-get-started.md)

