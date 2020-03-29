---
title: Så här använder du Azure Cosmos DB-ändringsflöde med Azure Functions
description: Använd Azure Functions för att ansluta till Azure Cosmos DB-ändringsfeed. Senare kan du skapa reaktiva Azure-funktioner som utlöses på varje ny händelse.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851364"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Serverlösa händelsebaserade arkitekturer med Azure Cosmos DB och Azure Functions

Azure Functions är det enklaste sättet att ansluta till [ändringsflödet](change-feed.md). Du kan skapa små reaktiva Azure-funktioner som automatiskt utlöses på varje ny händelse i din Azure Cosmos-behållarens ändringsflöde.

![Serverlösa händelsebaserade funktioner som arbetar med Azure Functions-utlösaren för Cosmos DB](./media/change-feed-functions/functions.png)

Med [Azure Functions-utlösaren för Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)kan du utnyttja skalnings- och tillförlitlig händelseidentifieringsfunktioner för [ändringsflödesbearbetning](./change-feed-processor.md)utan att behöva underhålla [någon arbetsinfrastruktur](./change-feed-processor.md). Fokusera bara på din Azure-funktions logik utan att oroa dig för resten av pipelinen för händelsekällor. Du kan även blanda utlösaren med andra [Azure Functions-bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> För närvarande stöds Azure Functions-utlösaren för Cosmos DB endast för användning med SQL-API:et (Core).

## <a name="requirements"></a>Krav

Om du vill implementera ett serverlöst händelsebaserat flöde behöver du:

* **Den övervakade behållaren**: Den övervakade behållaren är Azure Cosmos-behållaren som övervakas och lagrar data från vilka ändringsflödet genereras. Eventuella skär, uppdateringar av den övervakade behållaren återspeglas i behållarens ändringsflöde.
* **Lånebehållaren**: Lånebehållaren upprätthåller tillståndet i flera och dynamiska serverlösa Azure Function-instanser och möjliggör dynamisk skalning. Den här lånebehållaren kan skapas manuellt eller automatiskt av Azure Functions-utlösaren för Cosmos DB. Om du vill skapa lånebehållaren automatiskt anger du flaggan *CreateLeaseCollectionIfNotExists* i [konfigurationen](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Partitionerade lånebehållare måste ha `/id` en partitionsnyckeldefinition.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Skapa din Azure Functions-utlösare för Cosmos DB

Att skapa din Azure-funktion med en Azure Functions-utlösare för Cosmos DB stöds nu i alla Azure Functions IDE- och CLI-integreringar:

* [Visual Studio-tillägg](../azure-functions/functions-develop-vs.md) för Visual Studio-användare.
* [Visual Studio-kodtillägg](/azure/javascript/tutorial-vscode-serverless-node-01) för Användare av Visual Studio-kod.
* Och slutligen [Core CLI verktyg](../azure-functions/functions-run-local.md#create-func) för en plattformsoberoende IDE agnostiker upplevelse.

## <a name="run-your-trigger-locally"></a>Kör utlösaren lokalt

Du kan köra din [Azure-funktion lokalt](../azure-functions/functions-develop-local.md) med [Azure Cosmos DB Emulator](./local-emulator.md) för att skapa och utveckla dina serverlösa händelsebaserade flöden utan en Azure-prenumeration eller ådra sig några kostnader.

Om du vill testa livescenarier i molnet kan du [prova Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan att någon kreditkorts- eller Azure-prenumeration krävs.

## <a name="next-steps"></a>Nästa steg

Du kan nu fortsätta att lära dig mer om ändringsflödet i följande artiklar:

* [Översikt över ändringsflödet](change-feed.md)
* [Olika sätt att läsa ändringsflöde](read-change-feed.md)
* [Använda ändringsmatarprocessorbibliotek](change-feed-processor.md)
* [Så här arbetar du med ändringsflödesprocessorbibliotek](change-feed-processor.md)
* [Serverlös databasdatoranvändning med Azure Cosmos DB och Azure Functions](serverless-computing-database.md)
