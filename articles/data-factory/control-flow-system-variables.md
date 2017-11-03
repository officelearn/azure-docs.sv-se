---
title: Systemvariabler i Azure Data Factory | Microsoft Docs
description: "Den här artikeln beskriver systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: shlo
ms.openlocfilehash: 274c003e0697ba08d010c3bf13724461a4b624ee
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="system-variables-supported-by-azure-data-factory"></a>Systemvariabler som stöds av Azure Data Factory
Den här artikeln beskriver systemvariabler som stöds av Azure Data Factory. Du kan använda dessa variabler i uttryck när du definierar Data Factory entiteter. 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [funktioner och variabler i Data Factory version 1](v1/data-factory-functions-variables.md).


## <a name="pipeline-scope"></a>Pipeline-scope:

| Variabelnamn | Beskrivning |
| --- | --- |
| @pipeline(). DataFactory |Namnet på data factory pipeline kör körs inom | 
| @pipeline(). Pipeline |Namnet på pipeline |
| @pipeline(). RunId | ID för specifika försäljningsförlopp kör | 
| @pipeline(). TriggerType | Typen av utlösare som anropade pipeline (manuell, Schemaläggaren) | 
| @pipeline(). Utlösarens ID| ID för utlösaren som anropar pipeline |
| @pipeline(). Utlösarnamn| Namnet på utlösaren som anropar pipeline |
| @pipeline(). TriggerTime| Tid då utlösaren som anropade pipeline. Utlösaren tid är den faktiska Eldad tiden inte den schemalagda tiden. Till exempel `13:20:08.0149599Z` returneras i stället för`13:20:00.00Z` |

## <a name="trigger-scope"></a>Utlösaren omfattning:

| Variabelnamn | Beskrivning |
| --- | --- |
| utlösa .scheduledTime) |Tid när utlösaren har schemalagts att anropa pipelinen körs. Till exempel för en utlösare som utlöses var 5 minuter, den här variabeln returnerar `2017-06-01T22:20:00Z`, `2017-06-01T22:25:00Z`, `2017-06-01T22:29:00Z` respektive.|
| utlösa .startTime) |Tid då utlösaren **faktiskt** utlöses för att anropa pipelinen körs. Till exempel för en utlösare som utlöses var 5 minuter, den här variabeln kan returnera ungefär så här `2017-06-01T22:20:00.4061448Z`, `2017-06-01T22:25:00.7958577Z`, `2017-06-01T22:29:00.9935483Z` respektive.|

## <a name="next-steps"></a>Nästa steg
Information om hur dessa variabler används i uttryck finns [Expression language & funktioner](control-flow-expression-language-functions.md). 
