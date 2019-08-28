---
title: Azure Batch analys | Microsoft Docs
description: Referens för Azure Batch analys.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 03cb6231a42e27c474e20f4c6ae91095156bb766
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095518"
---
# <a name="batch-analytics"></a>Batchanalys
Ämnena i batch Analytics innehåller referensinformation om de händelser och aviseringar som är tillgängliga för batch-tjänstens resurser.

Mer information om hur du aktiverar och använder batch-diagnostikloggar finns i [Azure Batch diagnostisk loggning](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) .

## <a name="diagnostic-logs"></a>Diagnostikloggar

Azure Batchs tjänsten genererar följande diagnostiska logg händelser under livstiden för vissa batch-resurser.

**Tjänsten logghändelser**
* [Skapande av pool](batch-pool-create-event.md)
* [Start för borttagning av pool](batch-pool-delete-start-event.md)
* [Pool ta bort klar](batch-pool-delete-complete-event.md)
* [Start för storleksändring av pool](batch-pool-resize-start-event.md)
* [Pool resize klar](batch-pool-resize-complete-event.md)
* [Uppgiften start](batch-task-start-event.md)
* [Uppgift slutförd](batch-task-complete-event.md)
* [Aktiviteten misslyckas](batch-task-fail-event.md)