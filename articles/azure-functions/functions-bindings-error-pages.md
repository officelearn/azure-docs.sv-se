---
title: Azure Functions felhantering vägledning | Microsoft Docs
description: Innehåller allmänna riktlinjer för att hantera fel som inträffar i när dina funktioner körs och länkar till ämnen för bindning-specifikt fel.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 5a8dae73c164b319b4c291685deff402f9798364
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091854"
---
# <a name="azure-functions-error-handling"></a>Azure Functions-felhantering

Det här avsnittet innehåller allmänna riktlinjer för att hantera fel som uppstår när dina funktioner körs. Det innehåller också länkar till avsnitt som beskriver bindning-specifika fel som kan uppstå. 

## <a name="handing-errors-in-functions"></a>Hantering av fel i functions
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Felkoder för bindning

När du integrerar med Azure-tjänster, kanske fel som kommer från API: er för underliggande tjänster. Länkar till felet code dokumentationen för dessa tjänster finns i den **undantag och returkoder** delen av följande utlösare och referensinformation för bindning:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
