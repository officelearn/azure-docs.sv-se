---
title: Begränsningar, kvoter och tröskelvärden i Azure Scheduler
description: Läs mer om begränsningar, kvoter, standardvärden och begränsnings tröskelvärden för Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300912"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Begränsningar, kvoter och begränsnings gränser i Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersätter Azure Scheduler, som dras [tillbaka](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Om du vill fortsätta arbeta med de jobb som du konfigurerar i Scheduler, [migrera till Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) så snart som möjligt.

## <a name="limits-quotas-and-thresholds"></a>Gränser, kvoter och tröskelvärden

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>rubrik för x-MS-Request-ID

Varje begäran som görs mot Scheduler-tjänsten returnerar ett svars huvud med namnet **x-MS-Request-ID**. Den här rubriken innehåller ett ogenomskinligt värde som unikt identifierar begäran. Om en begäran däremot Miss lyckas och du har bekräftat att begäran är korrekt formaterad, kan du rapportera felet till Microsoft genom att ange värdet för svars huvudet **x-MS-Request-ID** och inkludera dessa uppgifter: 

* Värdet **x-MS-Request-ID**
* Ungefärlig tid då begäran gjordes 
* Identifierare för Azure-prenumerationen, jobb samlingen och jobbet 
* Den typ av åtgärd som begäran försökte utföra

## <a name="see-also"></a>Se också

* [Vad är Azure Scheduler?](scheduler-intro.md)
* [Begrepp, terminologi och entitetshierarki relaterade till Azure Scheduler](scheduler-concepts-terms.md)
