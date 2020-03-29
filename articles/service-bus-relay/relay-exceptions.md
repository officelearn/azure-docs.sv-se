---
title: Azure Relay-undantag och hur du löser dem | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60749044"
---
# <a name="azure-relay-exceptions"></a>Azure Relay-undantag

I den här artikeln visas några undantag som kan genereras av Azure Relay API:er. Den här referensen kan komma att ändras, så kom tillbaka för uppdateringar.

## <a name="exception-categories"></a>Undantagskategorier

Relay API:er genererar undantag som kan delas in i följande kategorier. Det finns också förslag på åtgärder som du kan vidta för att hjälpa till att lösa undantagen.

*   **Fel vid användarkodning**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Allmän åtgärd**: Försök att åtgärda koden innan du fortsätter.
*   **Installations-/konfigurationsfel**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Allmän åtgärd**: Granska konfigurationen. Om det behövs ändrar du konfigurationen.
*   **Tillfälliga undantag**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Allmän åtgärd**: Försök igen eller meddela användarna.
*   **Andra undantag**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Allmän åtgärd**: Specifik för undantagstypen. Se tabellen i följande avsnitt. 

## <a name="exception-types"></a>Undantagstyper

I följande tabell visas undantagstyper för meddelanden och deras orsaker. Det noterar också föreslagna åtgärder som du kan vidta för att hjälpa till att lösa undantagen.

| **Undantagstyp** | **Beskrivning** | **Föreslagen åtgärd** | **Anmärkning om automatisk eller omedelbar återförsök** |
| --- | --- | --- | --- |
| [Timeout](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden, som styrs av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Servern kan ha slutfört den begärda åtgärden. Detta kan inträffa på grund av nätverksförseningar eller andra infrastrukturförseningar. |Kontrollera om det finns konsekvens i systemtillståndet och försök sedan igen om det behövs. Se [TimeoutException](#timeoutexception). |Försök igen kan hjälpa i vissa fall. lägga till logik för återförsök i koden. |
| [Ogiltig åtgärd](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Den begärda användaråtgärden är inte tillåten i servern eller tjänsten. Mer information finns i undantagsmeddelandet. |Kontrollera koden och dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Försök igen hjälper inte. |
| [Åtgärden avbröts](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Ett försök görs att anropa en åtgärd på ett objekt som redan har stängts, avbrutits eller avyttrats. I sällsynta fall är den omgivande transaktionen redan avyttrad. |Kontrollera koden och se till att den inte anropar åtgärder på ett bortskaffat objekt. |Försök igen hjälper inte. |
| [Obehörig åtkomst](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider-objektet](/dotnet/api/microsoft.servicebus.tokenprovider) kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Kontrollera att tokenprovidern skapas med rätt värden. Kontrollera konfigurationen av åtkomstkontrolltjänsten. |Försök igen kan hjälpa i vissa fall. lägga till logik för återförsök i koden. |
| [Undantag för argument](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument utanför intervallet](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Ett eller flera av följande har inträffat:<br />Ett eller flera argument som anges till metoden är ogiltiga.<br /> Den URI som anges till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) innehåller ett eller flera sökvägssegment.<br />URI-schemat som levereras till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) är ogiltigt. <br />Egenskapsvärdet är större än 32 kB. |Kontrollera den anropande koden och kontrollera att argumenten är korrekta. |Försök igen hjälper inte. |
| [Server upptagen](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Tjänsten kan inte behandla begäran just nu. |Klienten kan vänta en viss tid och sedan försöka igen. |Klienten kan försöka igen efter ett visst intervall. Om ett nytt försök resulterar i ett annat undantag kontrollerar du hur undantaget ska försöka igen. |
| [Kvoten har överskridits](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Meddelandeentiteten har nått sin högsta tillåtna storlek. |Skapa utrymme i entiteten genom att ta emot meddelanden från entiteten eller dess underköer. Se [QuotaExceedException](#quotaexceededexception). |Ett nytt försök kan vara till hjälp om meddelanden har tagits bort under tiden. |
| [Meddelandestorleken har överskridits](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |En meddelandenyttolast överskrider gränsen på 256 KB. Observera att gränsen på 256 KB är den totala meddelandestorleken. Den totala meddelandestorleken kan innehålla systemegenskaper och eventuella Omkostnader för Microsoft .NET. |Minska storleken på meddelandenyttolasten och försök sedan igen. |Försök igen hjälper inte. |

## <a name="quotaexceededexception"></a>KvotExceedException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger att en kvot för en specifik enhet har överskridits.

För Relay sveper det här undantaget [system.servicemodel.quotaExceedException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), vilket anger att det maximala antalet lyssnare har överskridits för den här slutpunkten. Detta anges i värdet **MaximumListenersPerEndpoint** för undantagsmeddelandet.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en användarinitierad åtgärd tar längre tid än timeouten för åtgärden. 

Kontrollera värdet för egenskapen [ServicePointManager.DefaultConnectionLimit.](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) Om du når den här gränsen kan det också orsaka en [timeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

För Relay kan du få timeout-undantag när du öppnar en relayeranslutning för första gången. Det finns två vanliga orsaker till det här undantaget:

*   [OpenTimeout-värdet](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) kan vara för litet (om även med en bråkdel av en sekund).
* En lokal relay-lyssnare kanske inte svarar (eller så kan det stöta på problem med brandväggsregler som hindrar lyssnare från att acceptera nya klientanslutningar) och [OpenTimeout-värdet](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) är mindre än cirka 20 sekunder.

Exempel:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet:

*   **Felaktig konfiguration**
    
    Timeouten för drift kan vara för liten för drifttillståndet. Standardvärdet för timeout för åtgärden i klientenSDK är 60 sekunder. Kontrollera om värdet i koden är inställt på något för litet. Observera att CPU-användning och nätverkets tillstånd kan påverka den tid det tar för en åtgärd att slutföras. Det är en bra idé att inte ställa in timeout för åtgärden till ett mycket litet värde.
*   **Fel vid tillfällig service**

    Ibland kan Relay-tjänsten uppleva förseningar i bearbetningen av begäranden. Detta kan till exempel inträffa under perioder med hög trafik. Om detta inträffar, försök igen åtgärden efter en fördröjning, tills åtgärden lyckas. Om samma åtgärd fortsätter att misslyckas efter flera försök kontrollerar du [azure-tjänststatusplatsen](https://azure.microsoft.com/status/) för att se om det finns kända avbrott i tjänsten.

## <a name="next-steps"></a>Nästa steg
* [Vanliga frågor om Azure Relay](relay-faq.md)
* [Skapa ett relänamnområde](relay-create-namespace-portal.md)
* [Komma igång med Azure Relay och .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Komma igång med Azure Relay och Node](relay-hybrid-connections-node-get-started.md)

