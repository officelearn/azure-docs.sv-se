---
title: Azure Batch analys
description: Ämnena i batch Analytics innehåller referensinformation om de händelser och aviseringar som är tillgängliga för batch-tjänstens resurser.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: 9d0e5c90c7bade82d76c7dbdfd4276e9671c961c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958114"
---
# <a name="batch-analytics"></a>Batchanalys
Ämnena i batch Analytics innehåller referensinformation om de händelser och aviseringar som är tillgängliga för batch-tjänstens resurser.

Mer information om hur du aktiverar och använder batch-diagnostikloggar finns i [Azure Batch diagnostisk loggning](./batch-diagnostics.md) .

## <a name="diagnostic-logs"></a>Diagnostikloggar

Azure Batchs tjänsten genererar följande diagnostiska logg händelser under livstiden för vissa batch-resurser.

**Tjänst logg händelser**
* [Skapa pool](batch-pool-create-event.md)
* [Start för borttagning av pool](batch-pool-delete-start-event.md)
* [Borttagning av pool slutförd](batch-pool-delete-complete-event.md)
* [Start av poolens storleks ändring](batch-pool-resize-start-event.md)
* [Storleks ändring av pool slutförd](batch-pool-resize-complete-event.md)
* [Uppgiftens start](batch-task-start-event.md)
* [Uppgiften slutförd](batch-task-complete-event.md)
* [Åtgärden kunde inte utföras](batch-task-fail-event.md)
