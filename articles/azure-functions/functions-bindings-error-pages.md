---
title: Azure Functions felhantering vägledning | Microsoft Docs
description: Innehåller allmänna riktlinjer för att hantera fel som inträffar i när dina funktioner körs och länkar till ämnen för bindning-specifikt fel.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 02/01/2018
ms.date: 11/22/2018
ms.author: v-junlch
ms.openlocfilehash: bf54d312de5625a7fa44cea4d5107e83cf15583c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105516"
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

<!-- Update_Description: update metedata properties -->