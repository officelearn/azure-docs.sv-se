---
title: Azure Batch analys
description: Ämnena i batch Analytics innehåller referensinformation om de händelser och aviseringar som är tillgängliga för batch-tjänstens resurser.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: b07d362c3cc2d3b086cf0a82325025604907a393
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726867"
---
# <a name="batch-analytics"></a>Batchanalys
Ämnena i batch Analytics innehåller referensinformation om de händelser och aviseringar som är tillgängliga för batch-tjänstens resurser.

Mer information om hur du aktiverar och använder batch-diagnostikloggar finns i [Azure Batch diagnostisk loggning](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) .

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