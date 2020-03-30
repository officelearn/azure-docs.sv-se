---
title: Vägledning för hantering av Azure Functions-fel
description: Lär dig att hantera fel i Azure Functions med länkar till specifika bindningsfel.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586197"
---
# <a name="azure-functions-error-handling"></a>Felhantering av Azure Functions

Hantering av fel i Azure Functions är viktigt för att undvika förlorade data, missade händelser och för att övervaka hälsotillståndet för ditt program.

I den här artikeln beskrivs allmänna strategier för felhantering tillsammans med länkar till bindningsspecifika fel.

## <a name="handling-errors"></a>Hantera fel

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Bindande felkoder

När du integrerar med Azure-tjänster kan fel komma från API:erna för de underliggande tjänsterna. Information om bindande specifika fel finns i avsnittet **Undantag och returkoder** i följande artiklar:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Händelsehubbar](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT-hubbar](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Lagring av köer](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
