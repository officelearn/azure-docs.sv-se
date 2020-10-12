---
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 10/13/2019
ms.author: glenga
ms.openlocfilehash: 85c107451c8199f981ca742b998c6a4bdf15c366
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "72303544"
---
> [!NOTE]  
> Standard tids zonen som används för att köra CRON uttryck är Coordinated Universal Time (UTC). Om du vill att ditt CRON-uttryck ska köras baserat på en annan tidszon skapar du en app-inställning för din Function-app med namnet WEBSITE_TIME_ZONE. Läs mer i [NCRONTAB tids zoner](../articles/azure-functions/functions-bindings-timer.md#ncrontab-time-zones).
