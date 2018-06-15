---
title: Uppgiften hubbar i varaktiga funktioner – Azure
description: Lär dig vilka ett nav för aktiviteten är i tillägget varaktiga funktioner för Azure Functions. Lär dig hur du konfigurerar konfigurera aktiviteten hubs.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762334"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Uppgiften hubbar i varaktiga funktioner (Azure-funktioner)

En *aktivitet hubb* i [varaktiga funktioner](durable-functions-overview.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringarna. Orchestrator- och aktivitetshantering funktioner kan endast interagera med varandra när de hör till samma aktivitet hubb.

Varje funktionsapp har en separat åtgärd hubb. Om flera funktionen appar delar ett lagringskonto, innehåller flera uppgiften NAV storage-konto. Följande diagram illustrerar en aktivitet hubb per funktionsapp i delade och dedikerad storage-konton.

![Diagram som visar delade och särskilda lagringskonton.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-resurser

En uppgift hubb består av följande lagringsresurser: 

* En eller fler köer för kontrollen.
* Ett arbetsobjekt kö.
* För en historiktabell.
* En tabell instanser.
* Storage-behållare som innehåller en eller flera lease-BLOB.

Alla resurser skapas automatiskt i Azure lagringskonto när orchestrator eller funktioner för aktiviteten kör eller är schemalagda att köras. Den [prestanda och skalning](durable-functions-perf-and-scale.md) artikeln förklaras hur du använder dessa resurser.

## <a name="task-hub-names"></a>Aktivitetsnamn hub

Uppgiften hubbar identifieras med ett namn som har deklarerats i den *host.json* -fil, som visas i följande exempel:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Hubb aktivitetsnamn måste börja med en bokstav och endast bestå av bokstäver och siffror. Om inget anges är standardnamnet **DurableFunctionsHub**.

> [!NOTE]
> Namnet är vad särskiljer hubb för en uppgift från en annan när det finns flera nav för aktiviteten i ett konto med delad lagring. Om du har flera funktionen appar som delar ett konto med delad lagring kan du behöva konfigurera olika namn för varje aktivitet hubben i den *host.json* filer.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versionshantering](durable-functions-versioning.md)
