---
title: Mått som stöds av Azure Event Grid
description: Den här artikeln innehåller Azure Monitor mått som stöds av Azure Event Grids tjänsten.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 87c91077c8eeca2134da53774979c212a82e3b7d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042148"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Mått som stöds av Azure Event Grid
Den här artikeln innehåller listor över Event Grid mått som kategoriseras efter namn områden. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Avsnitt|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
| AdvancedFilterEvaluationCount | Yes | Avancerade filter utvärderingar | Antal | Totalt | Totalt antal avancerade filter som utvärderats över händelse prenumerationer | EventSubscriptionName |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|
| AdvancedFilterEvaluationCount | Yes | Avancerade filter utvärderingar | Antal | Totalt | Totalt antal avancerade filter som utvärderats över händelse prenumerationer | EventSubscriptionName |



## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|
| AdvancedFilterEvaluationCount | Yes | Avancerade filter utvärderingar | Antal | Totalt | Totalt antal avancerade filter som utvärderats över händelse prenumerationer | Ämne, EventSubscriptionName, DomainEventSubscriptionName |

## <a name="next-steps"></a>Nästa steg
Se följande artikel: [diagnostikloggar](diagnostic-logs.md)
