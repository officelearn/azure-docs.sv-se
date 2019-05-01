---
title: Systemvariabler i Azure Data Factory | Microsoft Docs
description: Den här artikeln beskriver systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory-entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 183c6aea5aaf84c9d0ed635cac55074a5256851e
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872548"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systemvariabler som stöds av Azure Data Factory
Den här artikeln beskriver systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory-entiteter.

## <a name="pipeline-scope"></a>Pipeline-omfång
Dessa systemvariabler kan refereras var som helst i JSON-pipelinen.

| Variabelnamn | Beskrivning |
| --- | --- |
| @pipeline().DataFactory |Namnet på datafabriken pipelinekörningen körs |
| @pipeline().Pipeline |Namn på pipelinen |
| @pipeline().RunId | ID för den specifika pipelinekörningen |
| @pipeline().TriggerType | Typ av utlösare som anropas pipelinen (manuell, Scheduler) |
| @pipeline().TriggerId| ID för utlösare som anropar pipelinen |
| @pipeline().TriggerName| Namn på utlösare som anropar pipelinen |
| @pipeline().TriggerTime| Tid då utlösaren som anropas pipelinen. Tid för utlösaren är den faktiska utlösta tiden, inte den schemalagda tiden. Till exempel `13:20:08.0149599Z` returneras i stället för `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Schemalägga utlösaren omfång
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: ”ScheduleTrigger”.

| Variabelnamn | Beskrivning |
| --- | --- |
| @trigger().scheduledTime |Tid när utlösaren har schemalagts att anropa pipelinekörningen. Till exempel för en utlösare som utlöses varje 5 minuter, den här variabeln skulle returnera `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` respektive.|
| @trigger().startTime |Tid då utlösaren **faktiskt** utlöstes för att anropa pipelinekörningen. Till exempel för en utlösare som utlöses varje 5 minuter, den här variabeln kan returnera ungefär så här `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` respektive. (Obs: Tidsstämpeln är som standard i ISO 8601-format)|

## <a name="tumbling-window-trigger-scope"></a>Rullande fönster omfång
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: ”TumblingWindowTrigger”.

| Variabelnamn | Beskrivning |
| --- | --- |
| @trigger().outputs.windowStartTime |Början av fönstret när utlösaren har schemalagts att anropa pipeline-körning. Om utlösare för rullande fönster har en frekvens på ”varje timme” är detta tid i början av timmen.|
| @trigger().outputs.windowEndTime |Slutet av fönstret när utlösaren har schemalagts att anropa pipelinekörningen. Om utlösare för rullande fönster har en frekvens på ”varje timme” är detta tid i slutet av timmen.|
## <a name="next-steps"></a>Nästa steg
Information om hur dessa variabler används i uttryck finns i [Uttrycksspråk & funktioner](control-flow-expression-language-functions.md).
