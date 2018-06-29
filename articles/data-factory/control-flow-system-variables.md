---
title: Systemvariabler i Azure Data Factory | Microsoft Docs
description: Den här artikeln beskriver systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: shlo
ms.openlocfilehash: 43ea8703bdbfc23511c831a5f91c9461948cc254
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059184"
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systemvariabler som stöds av Azure Data Factory
Den här artikeln beskriver systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter.

## <a name="pipeline-scope"></a>Pipeline-scope
Dessa systemvariabler kan refereras var som helst i pipeline-JSON.

| Variabelnamn | Beskrivning |
| --- | --- |
| @pipeline().DataFactory |Namnet på data factory pipeline kör körs inom |
| @pipeline().Pipeline |Namnet på pipeline |
| @pipeline().RunId | ID för specifika försäljningsförlopp kör |
| @pipeline(). TriggerType | Typen av utlösare som anropade pipeline (manuell, Schemaläggaren) |
| @pipeline().TriggerId| ID för utlösaren som anropar pipeline |
| @pipeline().TriggerName| Namnet på utlösaren som anropar pipeline |
| @pipeline().TriggerTime| Tid då utlösaren som anropade pipeline. Utlösaren tid är den faktiska Eldad tiden inte den schemalagda tiden. Till exempel `13:20:08.0149599Z` returneras i stället för `13:20:00.00Z` |

## <a name="schedule-trigger-scope"></a>Schemalägga utlösaren omfång
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: ”ScheduleTrigger”.

| Variabelnamn | Beskrivning |
| --- | --- |
| @trigger.scheduledTime) |Tid när utlösaren har schemalagts att anropa pipelinen körs. Till exempel för en utlösare som utlöses var 5 minuter, den här variabeln returnerar `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` respektive.|
| @trigger.startTime) |Tid då utlösaren **faktiskt** utlöses för att anropa pipelinen körs. Till exempel för en utlösare som utlöses var 5 minuter, den här variabeln kan returnera ungefär så här `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` respektive.|

## <a name="tumbling-window-trigger-scope"></a>Rullande fönster utlösaren omfång
Dessa systemvariabler kan refereras var som helst i utlösaren JSON om utlösaren är av typen: ”TumblingWindowTrigger”.

| Variabelnamn | Beskrivning |
| --- | --- |
| @trigger(). outputs.windowStartTime |Starta fönstret när utlösaren har schemalagts att anropa pipeline för körning. Om rullande fönster utlösaren har en frekvens som ”varje timme” är detta tiden i början av en timme.|
| @trigger(). outputs.windowEndTime |Slutet på fönstret när utlösaren har schemalagts att anropa pipelinen körs. Om rullande fönster utlösaren har en frekvens som ”varje timme” är detta tiden i slutet av en timme.|
## <a name="next-steps"></a>Nästa steg
Information om hur dessa variabler används i uttryck finns [Expression language & funktioner](control-flow-expression-language-functions.md).
