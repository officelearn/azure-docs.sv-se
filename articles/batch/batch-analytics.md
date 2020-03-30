---
title: Azure Batch-analys
description: Avsnitten i Batch Analytics innehåller referensinformation för de händelser och aviseringar som är tillgängliga för batchtjänstresurser.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025970"
---
# <a name="batch-analytics"></a>Batchanalys
Avsnitten i Batch Analytics innehåller referensinformation för de händelser och aviseringar som är tillgängliga för batchtjänstresurser.

Mer information om hur du aktiverar och förbrukar diagnostikloggar för Azure Batch finns i [Azure Batch-diagnostikloggar.](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)

## <a name="diagnostic-logs"></a>Diagnostikloggar

Azure Batch-tjänsten avger följande diagnostiska logghändelser under livstiden för vissa batchresurser.

**Servicelogghändelser**
* [Skapa pool](batch-pool-create-event.md)
* [Start av poolborttagning](batch-pool-delete-start-event.md)
* [Borttagning av pool har slutförts](batch-pool-delete-complete-event.md)
* [Starta om poolen](batch-pool-resize-start-event.md)
* [Ändrar storlek på poolen färdigt](batch-pool-resize-complete-event.md)
* [Start av uppgift](batch-task-start-event.md)
* [Uppgiften har slutförts](batch-task-complete-event.md)
* [Aktiviteten misslyckas](batch-task-fail-event.md)