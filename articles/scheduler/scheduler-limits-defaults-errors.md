---
title: Begränsningar, kvoter och tröskelvärden i Azure Scheduler
description: Lär dig mer om begränsningar, kvoter, standardvärden och tröskelvärden för begränsning för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898532"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Tröskelvärden för begränsningar, kvoter och begränsning i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som [dras tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du har konfigurerat i Scheduler [migrerar](../scheduler/migrate-from-scheduler-to-logic-apps.md) du till Azure Logic Apps så snart som möjligt. 
>
> Scheduler är inte längre tillgängligt i Azure-portalen, men [REST API-](/rest/api/scheduler) och [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) förblir tillgängliga just nu så att du kan hantera dina jobb och jobbsamlingar.

## <a name="limits-quotas-and-thresholds"></a>Gränser, kvoter och tröskelvärden

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id-huvud

Varje begäran som görs mot Scheduler-tjänsten returnerar ett svarshuvud med namnet **x-ms-request-id**. Det här huvudet innehåller ett ogenomskinligt värde som unikt identifierar begäran. Så om en begäran konsekvent misslyckas, och du bekräftade att begäran är korrekt formaterad, kan du rapportera felet till Microsoft genom att ange svarshuvudet för **x-ms-request-id** och inkludera följande information: 

* Värdet **x-ms-request-id**
* Den ungefärliga tiden då begäran gjordes 
* Identifierarna för Azure-prenumeration, jobbsamling och jobb 
* Den typ av åtgärd som begäran försökte

## <a name="next-steps"></a>Nästa steg

* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
* [Abonnemang och fakturering för Azure Scheduler](scheduler-plans-billing.md)
* [Referens för REST-API:et för Azure Scheduler](/rest/api/scheduler)
* [Referens för PowerShell-cmdlets för Azure Scheduler](scheduler-powershell-reference.md)