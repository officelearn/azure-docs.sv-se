---
title: Azure Event Hubs-exempel | Microsoft Docs
description: Azure Event Hubs-exempel
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: sethm
ms.openlocfilehash: 8e80587f7b3f6b0cb081fd963848c7aa3ab11e3e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431264"
---
# <a name="event-hubs-samples"></a>Event Hubs-exempel 
Du kan hitta exempel för Event Hubs på [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). De här exemplen visar viktiga funktioner i [Azure Event Hubs](/azure/event-hubs/). Den här artikeln kategoriserar och beskriver exempel som är tillgängliga med länkar till var och en.

## <a name="net-samples"></a>.NET-exempel

| Exemplet namn | Beskrivning | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Det här exemplet visar hur du skriva ett .NET Core-konsolprogram som skickar en uppsättning händelser till en händelsehubb. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Detta exempel visar hur du skriver en .NET Core-konsolprogram som tar emot en uppsättning händelser från en händelsehubb med hjälp av Event Processor Host-biblioteket.  | 

## <a name="java-samples"></a>Java-exempel

| Exemplet namn | Beskrivning | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Det här exemplet visar hur du kan mata in batchar av händelser i din event hub. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Det här exemplet visar hur du kan samla in händelser till event hub. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Det här exemplet illustrerar de olika alternativen som är tillgängliga med Event Hubs att samla in händelser. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Det här exemplet illustrerar hur du tar emot händelser från event hubs-partitionen med hjälp av en specifik datum / tid-förskjutning. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Det här exemplet illustrerar hur du tar emot händelser från event hubs-partitionen med hjälp av en viss förskjutning. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Det här exemplet illustrerar hur kan ta emot från en händelsenavspartitioner med hjälp av ett sekvensnummer. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Det här exemplet visar hur du kan ta emot händelser från en händelsehubb med värden för händelsebearbetning, vilket ger automatisk partition val och redundans i flera samtidiga mottagare. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Det här exemplet illustrerar hur en händelsehubb kan automatiskt skala upp hög belastning. Exemplet skickar händelser till en kostnad som bara överskrider den konfigurerade mängden en händelsehubb som orsakar händelsehubben för att skala upp. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Det här exemplet kan mäta ingress-priset. | 

## <a name="next-steps"></a>Nästa steg
Du kan läsa mer om Event Hubs i följande artiklar:

- [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
- [Event Hubs-funktioner](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)