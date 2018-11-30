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
ms.openlocfilehash: 7a6346e594c5a7cc4cf02f3ea658aac4977e641a
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642622"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Uppgiftshubbar i varaktiga funktioner (Azure Functions)

En *uppgift hub* i [varaktiga funktioner](durable-functions-overview.md) är en logisk behållare för Azure Storage-resurser som används för orkestreringar. Orchestrator och aktivitet kan bara interagera med varandra när de tillhör samma uppgift hubb.

Varje funktionsapp har en separat åtgärd-hubb. Om flera funktionsappar delar ett storage-konto, innehåller flera uppgiftshubbar i lagringskontot. Följande diagram illustrerar en uppgift hubb per funktionsappen i delade och dedikerade storage-konton.

![Diagram som visar delade och dedikerade storage-konton.](./media/durable-functions-task-hubs/task-hubs-storage.png)

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

### <a name="hostjson-functions-v1"></a>Host.JSON (Functions v1)
```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```
### <a name="hostjson-functions-v2"></a>Host.JSON (Functions v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "MyTaskHub"
    }
  }
}
```
Uppgiftshubbar kan också konfigureras med hjälp av appinställningarna, enligt följande *host.json* exempelfil:

### <a name="hostjson-functions-v1"></a>Host.JSON (Functions v1)
```json
{
  "durableTask": {
    "HubName": "%MyTaskHub%"
  }
}
```
### <a name="hostjson-functions-v2"></a>Host.JSON (Functions v2)
```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "HubName": "%MyTaskHub%"
    }
  }
}
```
Uppgiften hubbnamnet anges till värdet för den `MyTaskHub` appinställningen. Följande `local.settings.json` visar hur du definierar den `MyTaskHub` som `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" :  "samplehubname" 
  }
}
```

Namn på aktiviteten måste börja med en bokstav och bestå av endast bokstäver och siffror. Om inte anges standardnamnet är **DurableFunctionsHub**.

> [!NOTE]
> Namnet är vad skiljer hub för en aktivitet från en annan när det finns flera uppgiftshubbar i en delad lagring-konto. Om du har flera funktionsappar som delar en delad lagring-konto kan du behöva konfigurera olika namn för varje uppgift hubben i den *host.json* filer.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du hanterar versionshantering](durable-functions-versioning.md)
