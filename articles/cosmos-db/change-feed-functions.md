---
title: Använda Azure Cosmos DB ändra feed med Azure Functions
description: Använd Azure Cosmos DB ändra feed med Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 299799566f0d54cc3fa017b032e15b387fe8cdd5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467929"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Händelsebaserade arkitekturer utan server med Azure Cosmos DB och Azure Functions

Azure Functions är det enklaste sättet att ansluta till [ändrings flödet](change-feed.md). Du kan skapa små reaktiva Azure Functions som aktive ras automatiskt vid varje ny händelse i din Azure Cosmos-behållares ändrings flöde.

![Server lösa händelsebaserade funktioner som fungerar med Azure Functions utlösare för Cosmos DB](./media/change-feed-functions/functions.png)

Med Azure Functions-utlösaren [för Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)kan du utnyttja skalningen av [processen för byte](./change-feed-processor.md)och pålitlig händelse identifiering utan att behöva underhålla någon [arbets infrastruktur](./change-feed-processor.md). Fokusera bara på din Azure Function-logik utan att oroa dig över resten av pipeline för händelse-källa. Du kan till och med blanda utlösaren med andra [Azure Functions bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> För närvarande stöds Azure Functions utlösare för Cosmos DB endast för användning med Core-API: et (SQL).

## <a name="requirements"></a>Krav

Om du vill implementera ett Server lös händelsebaserat flöde måste du:

* **Den övervakade behållaren**: Den övervakade behållaren är den Azure Cosmos-behållare som övervakas, och den lagrar data som ändrings flödet genereras från. Eventuella infogningar och ändringar (t. ex. CRUD) till den övervakade behållaren visas i behållarens ändrings flöde.
* **Leasing container**: Lease-behållaren upprätthåller tillstånd för flera och dynamiska Azure Function-instanser och möjliggör dynamisk skalning. Denna Lease-behållare kan skapas manuellt eller skapas automatiskt av Azure Functions-utlösaren för Cosmos DB. Om du vill skapa en låne container automatiskt anger du flaggan *CreateLeaseCollectionIfNotExists* i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Partitionerade leasing behållare måste ha en `/id` definition av partitionsnyckel.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Skapa din Azure Functions-utlösare för Cosmos DB

Att skapa en Azure-funktion med en Azure Functions-utlösare för Cosmos DB stöds nu i alla Azure Functions IDE-och CLI-integrationer:

* [Visual Studio-tillägg](../azure-functions/functions-develop-vs.md) för Visual Studio-användare.
* [Visual Studio Core-tillägg](https://code.visualstudio.com/tutorials/functions-extension/create-function) för Visual Studio Code-användare.
* Och slutligen [Core CLI-verktyg](../azure-functions/functions-run-local.md#create-func) för en plattforms oberoende IDE oberoende-upplevelse.

## <a name="run-your-trigger-locally"></a>Kör din utlösare lokalt

Du kan köra din [Azure-funktion lokalt](../azure-functions/functions-develop-local.md) med [Azure Cosmos DB emulatorn](./local-emulator.md) för att skapa och utveckla serverbaserade händelsebaserade flöden utan en Azure-prenumeration eller utan kostnad.

Om du vill testa Live-scenarier i molnet kan du [prova Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan att något kredit kort eller någon Azure-prenumeration krävs.

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta att lära dig mer om ändrings flöden i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Sätt att läsa ändrings flöde](read-change-feed.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
* [Så här arbetar du med biblioteket Change feed processor](change-feed-processor.md)
* [Databas bearbetning utan server med hjälp av Azure Cosmos DB och Azure Functions](serverless-computing-database.md)
