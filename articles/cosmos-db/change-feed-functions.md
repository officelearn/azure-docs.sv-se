---
title: Använda Azure Cosmos DB ändra feed med Azure Functions
description: Använd Azure Cosmos DB ändra feed med Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: db3f1b6657ae455ae049eaffd6758fc7e6944fb9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001027"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Händelsebaserade arkitekturer utan server med Azure Cosmos DB och Azure Functions

Azure Functions är det enklaste sättet att ansluta till [ändrings flödet](change-feed.md). Du kan skapa små reaktiva Azure Functions som aktive ras automatiskt vid varje ny händelse i din Azure Cosmos-behållares ändrings flöde.

![Server lösa händelsebaserade funktioner som fungerar med den Azure Cosmos DB utlösaren](./media/change-feed-functions/functions.png)

Med [Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)-utlösaren kan du utnyttja skalningen av processen för förändrings matnings [processorn](./change-feed-processor.md)och tillförlitliga funktioner för händelse identifiering utan att behöva underhålla någon [arbets infrastruktur](./change-feed-processor.md). Fokusera bara på din Azure Function-logik utan att oroa dig över resten av pipeline för händelse-källa. Du kan till och med blanda utlösaren med andra [Azure Functions bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> För närvarande stöds Azure Cosmos DB-utlösaren för användning med Core-API: t (SQL).

## <a name="requirements"></a>Krav

Om du vill implementera ett Server lös händelsebaserat flöde måste du:

* **Den övervakade behållaren**: Den övervakade behållaren är den Azure Cosmos-behållare som övervakas, och den lagrar data som ändrings flödet genereras från. Eventuella infogningar och ändringar (t. ex. CRUD) till den övervakade behållaren visas i behållarens ändrings flöde.
* **Leasing container**: Lease-behållaren upprätthåller tillstånd för flera och dynamiska Azure Function-instanser och möjliggör dynamisk skalning. Denna Lease-behållare kan skapas manuellt eller skapas automatiskt av Azure Cosmos DB Trigger.To skapa en container container automatiskt, och ange flaggan *CreateLeaseCollectionIfNotExists* i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Partitionerade leasing behållare måste ha en `/id` definition av partitionsnyckel.

## <a name="create-your-azure-cosmos-db-trigger"></a>Skapa din Azure Cosmos DB-utlösare

Att skapa en Azure-funktion med en Azure Cosmos DB-utlösare stöds nu i alla Azure Functions IDE-och CLI-integrationer:

* [Visual Studio-tillägg](../azure-functions/functions-develop-vs.md) för Visual Studio-användare.
* [Visual Studio Core-tillägg](https://code.visualstudio.com/tutorials/functions-extension/create-function) för Visual Studio Code-användare.
* Och slutligen [Core CLI-verktyg](../azure-functions/functions-run-local.md#create-func) för en plattforms oberoende IDE oberoende-upplevelse.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Kör din Azure Cosmos DB-utlösare lokalt

Du kan köra din [Azure-funktion lokalt](../azure-functions/functions-develop-local.md) med [Azure Cosmos DB emulatorn](./local-emulator.md) för att skapa och utveckla serverbaserade händelsebaserade flöden utan en Azure-prenumeration eller utan kostnad.

Om du vill testa Live-scenarier i molnet kan du [prova Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) utan att något kredit kort eller någon Azure-prenumeration krävs.

## <a name="next-steps"></a>Nästa steg

Nu kan du fortsätta att lära dig mer om ändrings flöden i följande artiklar:

* [Översikt över ändra feed](change-feed.md)
* [Sätt att läsa ändrings flöde](read-change-feed.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
* [Så här arbetar du med biblioteket Change feed processor](change-feed-processor.md)
* [Databas bearbetning utan server med hjälp av Azure Cosmos DB och Azure Functions](serverless-computing-database.md)
