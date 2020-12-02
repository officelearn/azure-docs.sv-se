---
title: Systemvariabler i Azure Data Factory
description: I den här artikeln beskrivs systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 1780b4a64de349c1e272158fe6bfde9cab6f8369
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486054"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systemvariabler som stöds av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter.

## <a name="pipeline-scope"></a>Pipeline-omfång
Dessa systemvariabler kan refereras var som helst i pipeline-JSON.

| Variabelnamn | Description |
| --- | --- |
| @pipeline(). DataFactory |Namnet på den data fabrik som pipelinen körs i |
| @pipeline(). Pipeline |Namn på pipelinen |
| @pipeline(). RunId | ID för den aktuella pipeline-körningen |
| @pipeline(). TriggerType | Typ av utlösare som anropade pipelinen (manuell, Scheduler) |
| @pipeline(). TriggerId| ID för utlösaren som anropar pipelinen |
| @pipeline(). TriggerName| Namnet på utlösaren som anropar pipelinen |
| @pipeline(). TriggerTime| Tid när utlösaren som anropade pipelinen. Utlösnings tiden är den faktiska utlösta tiden, inte den schemalagda tiden. Returneras till exempel `13:20:08.0149599Z` istället för `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Intervall för schema utlösare
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: "ScheduleTrigger".

| Variabelnamn | Description |
| --- | --- |
| @trigger().scheduledTime |Tid när utlösaren schemalades för att anropa pipeline-körningen. För en utlösare som utlöses var femte minut skulle den här variabeln till exempel returneras `2017-06-01T22:20:00Z` `2017-06-01T22:25:00Z` `2017-06-01T22:30:00Z` .|
| @trigger(). StartTime |Tid när utlösaren **faktiskt** startade för att anropa pipeline-körningen. För en utlösare som utlöses var femte minut kan den här variabeln till exempel returnera något som liknar detta `2017-06-01T22:20:00.4061448Z` `2017-06-01T22:25:00.7958577Z` `2017-06-01T22:30:00.9935483Z` . (Obs: tidsstämpeln är som standard i ISO 8601-format)|

## <a name="tumbling-window-trigger-scope"></a>Utlösare för rullande fönster
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: "TumblingWindowTrigger".
(Obs: tidsstämpeln är som standard i ISO 8601-format)

| Variabelnamn | Description |
| --- | --- |
| @trigger(). outputs. windowStartTime |Början av fönstret när utlösaren schemalades för att anropa pipeline-körningen. Om utlösaren för rullande Window har en frekvens på "varje timme" blir tiden i början av timmen.|
| @trigger(). outputs. windowEndTime |Slutet av fönstret när utlösaren schemalades för att anropa pipeline-körningen. Om utlösaren för rullande Window har en frekvens på "varje timme" blir tiden i slutet av timmen.|
## <a name="next-steps"></a>Nästa steg
Information om hur dessa variabler används i uttryck finns i [Expression language & Functions](control-flow-expression-language-functions.md).
