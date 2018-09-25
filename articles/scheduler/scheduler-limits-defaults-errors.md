---
title: Gränser, kvoter och tröskelvärden i Azure Scheduler
description: Läs om gränser, kvoter, standardvärden och begränsa tröskelvärden för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 0c1e704a3bdec239c87d879ae1ef95e6e76d27fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966932"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Gränser, kvoter och tröskelvärden för begränsning i Azure Scheduler

> [!IMPORTANT]
> [Med Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras. Att schemalägga jobb, [prova Azure Logic Apps i stället](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Gränser, kvoter och tröskelvärden

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>rubrik x-ms-request-id

Varje begäran som görs mot tjänsten Scheduler returnerar ett svarshuvud med namnet **x-ms-request-id**. Den här rubriken innehåller ett täckande värde som unikt identifierar begäran. Så om en begäran misslyckas konsekvent och du har bekräftat begäran är korrekt formaterad, kan du rapportera felet till Microsoft genom att tillhandahålla den **x-ms-request-id** svar huvudvärde och, inklusive följande information: 

* Den **x-ms-request-id** värde
* Den ungefärliga tid när en begäran har gjorts 
* Identifierare för Azure-prenumeration, jobb-samling och jobb 
* Vilken typ av åtgärd som försök till begäran

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
