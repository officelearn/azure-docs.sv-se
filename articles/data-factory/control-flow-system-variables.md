---
title: Systemvariabler i Azure Data Factory
description: I den här artikeln beskrivs systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar datafabrikentiteter.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/12/2018
ms.openlocfilehash: 2690ded0ac45719cb1082c85ab535c91ad491172
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417971"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systemvariabler som stöds av Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar datafabrikentiteter.

## <a name="pipeline-scope"></a>Pipeline-scope
Dessa systemvariabler kan refereras var som helst i rörledningen JSON.

| Variabelnamn | Beskrivning |
| --- | --- |
| @pipeline(). DataFactory (datafactory) |Namnet på den datafabrik som pipelinekörningen körs inom |
| @pipeline(). Rörledning |Rörledningens namn |
| @pipeline(). RunId (på) | ID för den specifika pipeline-körningen |
| @pipeline(). TriggerType | Typ av utlösare som anropade pipelinen (Manuell, Scheduler) |
| @pipeline(). TriggerId (triggerId)| ID för utlösaren som anropar pipelinen |
| @pipeline(). TriggerName (TriggerName)| Namnet på utlösaren som anropar pipelinen |
| @pipeline(). TriggerTime (TriggerTime)| Tid när utlösaren som anropade pipelinen. Utlösningstiden är den faktiska avfyrningstiden, inte den schemalagda tiden. `13:20:08.0149599Z` Returneras till exempel i stället för`13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Schemalägg utfångaromomfattning
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: "ScheduleTrigger".

| Variabelnamn | Beskrivning |
| --- | --- |
| @trigger().scheduledTime |Tid då utlösaren var schemalagd att anropa pipelinekörningen. Till exempel, för en utlösare som utlöses `2017-06-01T22:20:00Z`var `2017-06-01T22:25:00Z` `2017-06-01T22:30:00Z` 5 min, skulle denna variabel returnera , respektive.|
| @trigger().startTime |Tid då utlösaren **faktiskt** avfyrades för att anropa pipelinekörningen. Till exempel, för en utlösare som utlöses var 5 `2017-06-01T22:25:00.7958577Z` `2017-06-01T22:30:00.9935483Z` min, kan denna variabel returnera ungefär så här `2017-06-01T22:20:00.4061448Z`, respektive. (Obs: Tidsstämpeln är som standard i ISO 8601-format)|

## <a name="tumbling-window-trigger-scope"></a>Utlösaromfattning för tumlande fönster
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: "TumblingWindowTrigger".
(Obs: Tidsstämpeln är som standard i ISO 8601-format)

| Variabelnamn | Beskrivning |
| --- | --- |
| @trigger().outputs.windowStartTime |Start av fönstret när utlösaren var schemalagd att anropa pipelinekörningen. Om den tumlande fönsterutlösaren har en frekvens av "timme" skulle detta vara tiden i början av timmen.|
| @trigger().outputs.windowEndTime |Slutet av fönstret när utlösaren var schemalagd att anropa pipelinekörningen. Om den tumlande fönsterutlösaren har en frekvens av "timme" skulle detta vara tiden i slutet av timmen.|
## <a name="next-steps"></a>Nästa steg
Information om hur dessa variabler används i uttryck finns i [Uttrycksspråk & funktioner](control-flow-expression-language-functions.md).
