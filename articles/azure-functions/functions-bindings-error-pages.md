---
title: Vägledning för Azure Functions fel hantering
description: Lär dig att hantera fel i Azure Functions med länkar till vissa bindnings fel.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155298"
---
# <a name="azure-functions-error-handling"></a>Azure Functions fel hantering

Att hantera fel i Azure Functions är viktigt för att undvika förlorade data, missade händelser och för att övervaka hälso tillståndet för programmet.

I den här artikeln beskrivs allmänna strategier för fel hantering tillsammans med länkar till platsspecifika fel.

## <a name="handling-errors"></a>Hantera fel

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Fel koder för bindning

Vid integrering med Azure-tjänster kan fel komma från API: erna för underliggande tjänster. Information om platsspecifika fel finns i avsnittet **undantag och retur koder** i följande artiklar:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
