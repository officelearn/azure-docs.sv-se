---
title: "Azure Relay-undantag och hur du löser dem | Microsoft Docs"
description: "Lista över Azure Relay-undantag och föreslagna åtgärder du kan vidta för att lösa dem."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: 1dbe73dac0d09db96ab902909125869959963e6f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2017
---
# <a name="azure-relay-exceptions"></a>Azure Relay-undantag

Den här artikeln innehåller vissa undantag som genereras av Azure Relay-API: er. Den här referensen kan ändras, så kolla igen efter uppdateringar.

## <a name="exception-categories"></a>Undantag kategorier

Relay-API: er skapa undantag som kan delas in i följande kategorier. Listan finns även föreslagna åtgärder som du kan vidta för att lösa undantagen.

*   **Användaren kodningsfel**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Allmän åtgärd**: försök att åtgärda koden innan du fortsätter.
*   **Installationen/konfigurationsfel**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Allmän åtgärd**: Granska din konfiguration. Om det behövs ändrar du konfigurationen.
*   **Tillfälligt undantag**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Allmän åtgärd**: försök igen eller meddela användare.
*   **Andra undantag**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Allmän åtgärd**: specifika undantag. Se tabellen nedan. 

## <a name="exception-types"></a>Typer av undantag

I följande tabell visas meddelanden undantag typer och deras orsaker. Den visas också föreslagna åtgärder du kan vidta för att lösa undantagen.

| **Undantagstyp** | **Beskrivning** | **Föreslagen åtgärd** | **Tänk på automatisk eller omedelbara försök igen** |
| --- | --- | --- | --- |
| [Timeout](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Servern svarade inte på den begärda åtgärden inom den angivna tiden som kontrolleras av [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Servern kan slutföra den begärda åtgärden. Det kan bero på nätverket eller andra infrastruktur fördröjningar. |Kontrollera systemtillståndet för konsekvens och försök sedan igen, om det behövs. Se [TimeoutException](#timeoutexception). |Försök igen kan i vissa fall. Lägg till logik i koden. |
| [Ogiltig åtgärd](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Användar-åtgärden tillåts inte i servern eller tjänsten. Se Undantagsmeddelandet mer information. |Kontrollera koden och i dokumentationen. Kontrollera att den begärda åtgärden är giltig. |Det hjälper inte försök igen. |
| [Åtgärden avbröts](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Ett försök görs att anropa en åtgärd på ett objekt som har redan stängts, avbröts eller tagits bort. I sällsynta fall kan den omgivande transaktionen har redan tagits bort. |Kontrollera koden och kontrollera att den inte anropa åtgärder på ett borttaget objekt. |Det hjälper inte försök igen. |
| [Obehörig åtkomst](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Den [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objektet kunde inte hämta en token, token är ogiltig eller token innehåller inte de anspråk som krävs för att utföra åtgärden. |Se till att tokenleverantör har skapats med korrekta värden. Kontrollera konfigurationen av tjänsten åtkomstkontroll. |Försök igen kan i vissa fall. Lägg till logik i koden. |
| [Argumentet undantag](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argumentet är Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumentet utanför intervallet](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |En eller flera av följande har inträffat:<br />En eller flera argument angavs för metoden är ogiltiga.<br /> URI som levererats till [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) innehåller en eller flera sökvägssegment.<br />URI-schemat som angetts för [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) eller [skapa](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) är ogiltig. <br />Egenskapens värde är större än 32 KB. |Kontrollera den anropande koden och kontrollera att argumenten är korrekta. |Det hjälper inte försök igen. |
| [Servern är upptagen](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Tjänsten kan inte behandla begäran just nu. |Klienten kan vänta på en viss tidsperiod och försök igen. |Klienten kan försöka igen efter en viss tid. Om en försök resulterar i en annan undantag Kontrollera försök beteendet för undantaget. |
| [Kvoten överskreds](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Meddelanden entiteten har uppnått den maximala storleken. |Frigör utrymme i enheten genom att ta emot meddelanden från enheten eller dess underköer. Se [QuotaExceededException](#quotaexceededexception). |Försök igen kan hjälpa om meddelanden har tagits bort under tiden. |
| [Meddelandestorlek överskrider](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Nyttolast för ett meddelande är längre än 256 KB. Observera att den totala meddelandestorleken 256 KB-gränsen. Totalt antal meddelandelagringsstorlek kan innehålla Systemegenskaper och eventuella kostnader för Microsoft .NET. |Minska storleken på nyttolast meddelande och försök igen. |Det hjälper inte försök igen. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) anger en kvot för en viss enhet har överskridits.

För relä, undantaget radbryts i [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), vilket anger att det maximala antalet lyssnare har överskridits för den här slutpunkten. Detta visas i den **MaximumListenersPerEndpoint** värdet för meddelandet.

## <a name="timeoutexception"></a>TimeoutException
En [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) anger att en åtgärd som initieras av användaren tar längre tid än tidsgränsen för åtgärden. 

Kontrollera värdet för den [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) egenskapen. Ansluter till den här gränsen kan även orsaka en [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

För Relay, kan du få tidsgränsfel första gången du öppnar en avsändare relay-anslutning. Det finns två vanliga orsaker till detta undantag:

*   Den [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) värde kanske för liten (om jämnt genom att en del av en sekund).
* En lokal relay lyssnare kanske inte svarar (eller brandväggen regler problem som förhindrar att lyssnare godtar nya klientanslutningar kan uppstå) och [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) värdet är mindre än 20 sekunder.

Exempel:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Vanliga orsaker
Det finns två vanliga orsaker till det här felet:

*   **Felaktig konfiguration**
    
    Tidsgräns för åtgärd kanske för liten för driftsvillkor. Standardvärdet för tidsgräns för åtgärd i klient-SDK är 60 sekunder. Kontrollera om värdet i din kod har angetts till något som är för liten. Observera att CPU-användning och nätverkets tillstånd kan påverka den tid det tar för en åtgärd ska slutföras. Det är en bra idé inte att ange tidsgränsen för åtgärden på ett litet värde.
*   **Tillfälligt tjänstfel**

    Den vidarebefordrande tjänsten kan ibland uppstå fördröjningar i bearbetning av begäranden. Detta kan inträffa, till exempel under perioder med hög trafik. Om detta inträffar, försök igen efter en stund tills åtgärden har lyckats. Om samma åtgärden fortfarande misslyckas efter flera försök, kontrollera den [plats i Azure service status](https://azure.microsoft.com/status/) att se om det finns kända tjänstavbrott.

## <a name="next-steps"></a>Nästa steg
* [Azure Relay vanliga frågor och svar](relay-faq.md)
* [Skapa en relay-namnområde](relay-create-namespace-portal.md)
* [Kom igång med Azure Relay och .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Kom igång med Azure Relay och nod](relay-hybrid-connections-node-get-started.md)

