---
title: Vägledning för Azure Functions fel hantering
description: Lär dig att hantera fel i Azure Functions med länkar till vissa bindnings fel.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "77586197"
---
# <a name="azure-functions-error-handling"></a>Azure Functions fel hantering

Att hantera fel i Azure Functions är viktigt för att undvika förlorade data, missade händelser och för att övervaka hälso tillståndet för programmet.

I den här artikeln beskrivs allmänna strategier för fel hantering tillsammans med länkar till platsspecifika fel.

## <a name="handling-errors"></a>Hantera fel

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Fel koder för bindning

Vid integrering med Azure-tjänster kan fel komma från API: erna för underliggande tjänster. Information om platsspecifika fel finns i avsnittet **undantag och retur koder** i följande artiklar:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [IoT-hubbar](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
