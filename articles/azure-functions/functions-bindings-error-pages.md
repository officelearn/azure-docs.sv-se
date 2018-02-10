---
title: "Azure Functions felhantering vägledning | Microsoft Docs"
description: "Innehåller allmänna riktlinjer för att hantera fel som uppstår i när köra dina funktioner och länkar till ämnen för bindning-specifikt fel."
services: functions
cloud: 
documentationcenter: 
author: ggailey777
manager: cfowler
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-error-handling"></a>Azure Functions felhantering

Det här avsnittet innehåller allmänna riktlinjer för att hantera fel som uppstår när du kör dina funktioner. Det finns även länkar till avsnitt som beskriver bindning-specifika problem som kan uppstå. 

## <a name="handing-errors-in-functions"></a>Lämna fel i funktioner
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Felkoder för bindning

När du integrerar med Azure-tjänster, kanske fel inträffar som kommer från API: er för tjänsterna underliggande. Länkar till fel code dokumentationen för dessa tjänster kan hittas i den **undantag och returkoder** avsnitt i följande utlösare och referensinformation för bindning:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
