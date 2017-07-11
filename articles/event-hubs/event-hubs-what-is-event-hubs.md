---
title: "Vad är Azure Event Hubs och varför ska jag använda det? | Microsoft Docs"
description: "En översikt och introduktion till Azure Event Hubs – telemetriinmatning i molnskala från webbplatser, appar och enheter"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: sethm; babanisa
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 1a6bf0a0352e6d9e3a22586ac825558d12e1307a
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017


---
<a id="what-is-event-hubs" class="xliff"></a>

# Vad är Event Hubs?

Azure Event Hubs är en mycket skalbar dataströmningsplattform och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. Event Hubs har [funktioner för att publicera och prenumerera](https://msdn.microsoft.com/library/aa560414.aspx) med kort svarstid och i massiv skala och tjänar därmed som inkörsvägen för stordata.

<a id="why-use-event-hubs" class="xliff"></a>

## Varför ska jag använda Event Hubs?

Händelse- och telemetrihanteringsfunktionerna gör Event Hubs särskilt lämpligt för:

* Programinstrumentering
* Bearbetning av användarupplevelser och arbetsflöden
* Scenarier i sakernas internet (IoT)

Andra funktioner i Event Hubs är till exempel beteendespårning i mobilappar, trafikinformation från webbservergrupper, inspelning av spelhändelser i konsolspel eller telemetri som samlats in från industriella datorer, anslutna fordon eller andra enheter.

<a id="azure-event-hubs-overview" class="xliff"></a>

## Översikt av händelsehubbar i Azure

I lösningsarkitekturer fungerar händelsehubbar ofta som ”dörren” för en händelsepipeline, ofta kallad en *händelseinmatare*. En händelseinmatare är en komponent eller tjänst som placeras mellan händelseutgivare och -konsumenter och frikopplar produktion av en händelseström från användningen av de händelserna. Arkitekturen visas på följande bild:

![Händelsehubbar](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Event Hubs tillhandahåller funktioner för hantering av meddelandeströmmar men har egenskaper som skiljer sig från traditionell meddelandehantering för företag. Funktionerna i Event Hubs är byggda för scenarier med högt genomflöde och intensiv händelsebearbetning. Event Hubs skiljer sig därmed från [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)-meddelanden och implementerar inte vissa av de funktioner som är tillgängliga för [Service Bus](/azure/service-bus-messaging/)-meddelandeentiteter, till exempel avsnitt.

<a id="event-hubs-features" class="xliff"></a>

## Event Hubs-funktioner

Event Hubs innehåller följande viktiga element:

- [**Händelseskapare/utfärdare**](event-hubs-features.md#event-publishers): En entitet som skickar data till en händelsehubb. En händelse publiceras via AMQP 1.0 eller HTTPS.
- [**Avbildning**](event-hubs-features.md#capture): Gör att du kan avbilda strömmande data från Event Hubs och lagra dem på ett Azure Blob Storage-konto.
- [**Partitioner**](event-hubs-features.md#partitions): Gör att varje konsument endast kan läsa en viss delmängd, eller partition, i händelseströmmen.
- [**SAS-token**](event-hubs-features.md#sas-tokens): Identifierar och autentiserar händelseutfärdaren.
- [**Händelsekonsument**](event-hubs-features.md#event-consumers): En enhet som läser händelsedata från en händelsehubb. Händelsekonsumenter ansluter via AMQP 1.0. 
- [**Konsumentgrupper**](event-hubs-features.md#consumer-groups): Ger varje flerkonsumerande program en separat vy över händelseströmmen, vilket gör att konsumenterna kan agera oberoende av varandra.
- [**Genomflödesenheter**](event-hubs-features.md#capacity): Förköpta kapacitetsenheter. En enskild partition har en maximal skala på en genomflödesenhet.

Teknisk information om de här och andra Event Hubs-funktioner finns i [översikten över Event Hubs-funktioner](event-hubs-features.md). 

<a id="next-steps" class="xliff"></a>

## Nästa steg

Utförlig prisinformation för Event Hubs finns i [Priser för Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 


