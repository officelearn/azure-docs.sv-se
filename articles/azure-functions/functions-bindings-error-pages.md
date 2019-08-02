---
title: Vägledning för Azure Functions fel hantering | Microsoft Docs
description: Innehåller allmän vägledning för att hantera fel som uppstår i när dina funktioner körs och länkar till specifika specifika fel ämnen.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: bc0ffec1bfe55ee81573feffc58e1adb11421eec
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667435"
---
# <a name="azure-functions-error-handling"></a>Azure Functions fel hantering

Det här avsnittet innehåller allmänna rikt linjer för hur du hanterar fel som uppstår när dina funktioner körs. Den innehåller också länkar till de avsnitt som beskriver de specifika specifika fel som kan uppstå. 

## <a name="handling-errors-in-functions"></a>Hantera fel i funktioner
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Fel koder för bindning

När du integrerar med Azure-tjänster kan du ha fel som uppstår som härstammar från API: erna för underliggande tjänster. Länkar till fel kods dokumentationen för dessa tjänster finns i avsnittet **undantag och retur koder** i följande utlösare och bindnings referens ämnen:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
