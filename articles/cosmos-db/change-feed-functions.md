---
title: Hur du använder Azure Cosmos DB-ändringsflödet med Azure Functions
description: Använd Azure Cosmos DB-ändringsflödet med Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 18780deba1910b3ea77f7313bfb6d204dacabd82
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692763"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Serverlös händelsebaserad arkitektur med Azure Cosmos DB och Azure Functions

Azure Functions erbjuder det enklaste sättet att ansluta till den [ändringsflödet](change-feed.md). Du kan skapa små reaktiv Azure Functions som ska utlösas automatiskt på varje ny händelse i din Azure Cosmos-behållare ändringsfeed.

![Funktioner utan Server händelsebaserad arbeta med Azure Cosmos DB-utlösare](./media/change-feed-functions/functions.png)

Med den [Azure Cosmos DB-utlösare](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), kan du utnyttja den [Change Feed Processor](./change-feed-processor.md)är skalning och tillförlitlig händelse identifiering funktioner utan att behöva underhålla någon [worker infrastruktur](./change-feed-processor.md#implementing-the-change-feed-processor-library). Bara fokusera på din Azure-funktion logic utan att oroa resten av händelsekällor pipelinen. Du kan även blanda utlösaren med andra [Azure Functions-bindings](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Azure Cosmos DB-utlösare stöds för närvarande för användning med de grundläggande SQL-API endast.

## <a name="requirements"></a>Krav

För att implementera en serverlös händelsebaserad flödet, måste du:

* **Övervakade behållaren**: Övervakade behållaren är Azure Cosmos-behållaren som övervakas, och den lagrar data som genereras ändringsflöde. Alla infogningar och ändringar (t.ex. CRUD) till behållaren för övervakade återspeglas i ändringsflödet på behållaren.
* **Behållaren lånet**: Behållaren lånet upprätthåller tillstånd på flera och dynamiska vår serverlösa Azure Function instanser och gör att dynamisk skalning. Den här behållaren för lånet kan manuellt eller automatiskt skapas med Azure Cosmos DB Trigger.To automatiskt skapa lånet behållaren genom att ange den *CreateLeaseCollectionIfNotExists* flagga i den [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Partitionerade lånet behållare är måste ha en `/id` partitions-definitionen.

## <a name="create-your-azure-cosmos-db-trigger"></a>Skapa din Azure Cosmos DB-utlösare

Skapa din Azure-funktion med en Azure Cosmos DB-utlösare stöds nu i alla Azure Functions IDE och CLI integreringar:

* [Visual Studio-tillägget](../azure-functions/functions-develop-vs.md) för Visual Studio-användare.
* [Visual Studio Core-tillägget](https://code.visualstudio.com/tutorials/functions-extension/create-function) för Visual Studio Code-användare.
* Och slutligen [Core CLI-verktyg](../azure-functions/functions-run-local.md#create-func) för en plattformsoberoende IDE oberoende upplevelse.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Kör Azure Cosmos DB-utlösare lokalt

Du kan köra dina [Azure-funktion lokalt](../azure-functions/functions-develop-local.md) med den [Azure Cosmos DB-emulatorn](./local-emulator.md) du skapar och utvecklar dina serverlösa händelsebaserad flöden utan en Azure-prenumeration och utan kostnad.

Om du vill testa live scenarier i molnet, kan du [testa Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan kreditkort eller Azure-prenumeration krävs.

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsfeed i följande artiklar:

* [Översikt över ändringsfeed](change-feed.md)
* [Sätt att läsa ändringsflödet](read-change-feed.md)
* [Med hjälp av ändringen feed processor-biblioteket](change-feed-processor.md)
* [Hur du arbetar med ändringen feed processor-biblioteket](change-feed-processor.md)
* [Databas utan Server databehandling med Azure Cosmos DB och Azure Functions](serverless-computing-database.md)
