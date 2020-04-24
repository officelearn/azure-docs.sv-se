---
title: Azure Batch analys
description: Ämnena i batch Analytics innehåller referensinformation om de händelser och aviseringar som är tillgängliga för batch-tjänstens resurser.
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: d35bf249a3ae5dd06f42b89c3d5c2773f9508603
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113076"
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