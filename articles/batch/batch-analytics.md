---
title: Azure Batch Analytics | Microsoft Docs
description: Referens för Azure batchanalys.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 999c3037196044250b8a12d6b6b380553e58c6ba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55460190"
---
# <a name="batch-analytics"></a>Batchanalys
Batchanalys innehåller referensinformation för händelser och varningar som är tillgängliga för Batch-tjänstresurser.

Se [Diagnostisk loggning för Azure Batch](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) mer information om att aktivera och använda Batch-diagnostikloggar.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Azure Batch-tjänsten genererar följande diagnostiklogg händelser under livslängden för vissa Batch-resurser.

**Tjänsten logghändelser**
* [Skapande av pool](batch-pool-create-event.md)
* [Start för borttagning av pool](batch-pool-delete-start-event.md)
* [Pool ta bort klar](batch-pool-delete-complete-event.md)
* [Start för storleksändring av pool](batch-pool-resize-start-event.md)
* [Pool resize klar](batch-pool-resize-complete-event.md)
* [Uppgiften start](batch-task-start-event.md)
* [Uppgift slutförd](batch-task-complete-event.md)
* [Aktiviteten misslyckas](batch-task-fail-event.md)