---
title: "Vad är händelsehubbar i Azure? | Microsoft Docs"
description: "Översikt och beskrivning av händelsehubbar i Azure"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fcc3e2cb76a06607d0bfbbad810968b70d67e4f4


---
# <a name="what-is-azure-event-hubs"></a>Vad är händelsehubbar i Azure?
Händelsehubbar i Azure är en mycket skalbar tjänst för dataingång som kan mata in miljontals händelser per sekund så att du kan bearbeta och analysera de enorma mängder data som dina anslutna enheter och program producerar. Händelsehubbar fungerar som ”ytterdörren” för en händelsepipeline, och när data har samlats in i en händelsehubb kan du omvandla och lagra dessa data med hjälp av valfri leverantör av realtidsanalys eller med adaptrar för batchbearbetning/lagring. Händelsehubbar frikopplar produktionen av en händelseström från användningen av dessa händelser så att händelsekonsumenterna kan komma åt dem på sitt eget schema. Mer information och tekniska uppgifter finns i [Översikt av händelsehubbar](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Funktioner i händelsehubbar
Händelsehubbar är en tjänst för händelsebearbetning som ger händelse- och telemetribearbetning i massiv skala med kort svarstid och hög tillförlitlighet. Tjänsten är särskilt lämpad för:

* Programinstrumentering
* Bearbetning av användarupplevelser och arbetsflöden
* Scenarier i sakernas internet (IoT)

Andra viktiga funktioner i händelsehubbar är till exempel beteendespårning i mobilappar, trafikinformation från webbservergrupper, inspelning av spelhändelser i konsolspel eller telemetri som samlats in från industriella datorer eller anslutna fordon.

## <a name="next-steps"></a>Nästa steg
Mer utförlig information om händelsehubbar finns i följande avsnitt.

* [Event Hubs-översikt](event-hubs-overview.md)
* [Programmeringsguide för Event Hubs](event-hubs-programming-guide.md)
* [Vanliga frågor om tillgänglighet och support för Event Hubs](event-hubs-availability-and-support-faq.md)
* Kom igång med en [självstudiekurs om Event Hubs]
* Ett komplett [exempelprogram som använder Event Hubs]

[Event Hubs-självstudie]: event-hubs-csharp-ephcs-getstarted.md
[exempelprogram som använder Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Nov16_HO2-->


