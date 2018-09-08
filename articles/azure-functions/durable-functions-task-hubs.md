---
title: Uppgiftshubbar i varaktiga funktioner – Azure
description: Lär dig vilka en uppgift hub finns i tillägget varaktiga funktioner för Azure Functions. Lär dig hur du konfigurerar konfigurera uppgiftshubbar.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 53c70d4407222a80a9bc948db51294cd3e4e1bb4
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092843"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Uppgiftshubbar i varaktiga funktioner (Azure Functions)

En *uppgift hub* i [varaktiga funktioner](durable-functions-overview.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringar. Orchestrator och aktivitet kan bara interagera med varandra när de tillhör samma uppgift hubb.

Varje funktionsapp har en separat åtgärd-hubb. Om flera funktionsappar delar ett storage-konto, innehåller flera uppgiftshubbar i lagringskontot. Följande diagram illustrerar en uppgift hubb per funktionsappen i delade och dedikerade storage-konton.

![Diagram som visar delade och dedikerade storage-konton.](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-resurser

En uppgift hubb består av följande lagringsresurser: 

* En eller fler köer för kontrollen.
* En arbetsobjektet kön.
* En historiktabellen.
* En tabell med instanser.
* En storage-behållare som innehåller en eller flera blobbar för lånet.

Alla dessa resurser skapas automatiskt i Azure Storage-kontot när orchestrator eller Aktivitetsfunktioner kör eller är schemalagda att köras. Den [prestanda och skalning](durable-functions-perf-and-scale.md) artikeln förklarar hur dessa resurser används.

## <a name="task-hub-names"></a>Namn på uppgift

Uppgiftshubbar identifieras med ett namn som är deklarerad i den *host.json* filen enligt i följande exempel:

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

Namn på aktiviteten måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inte anges standardnamnet är **DurableFunctionsHub**.

> [!NOTE]
> Namnet är vad skiljer hub för en aktivitet från en annan när det finns flera uppgiftshubbar i en delad lagring-konto. Om du har flera funktionsappar som delar en delad lagring-konto kan du behöva konfigurera olika namn för varje uppgift hubben i den *host.json* filer.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versionshantering](durable-functions-versioning.md)
