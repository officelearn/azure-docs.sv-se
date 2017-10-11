---
title: Azure Batch Analytics | Microsoft Docs
description: "Referens för Azure Batch Analytics."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 7d634e1bb595a84b8af339e5bc5a483a7849e7f7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="batch-analytics"></a>Batch Analytics
Avsnitt i Batchanalyser innehåller referensinformation för händelser och varningar som är tillgängliga för Batch-tjänsten resurser.

Se [Azure Batch diagnostikloggning](https://azure.microsoft.com/documentation/articles/batch-diagnostics/) mer information om att aktivera och använda Batch diagnostikloggar.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Azure Batch-tjänsten skickar följande diagnostiska logghändelser under livslängden för vissa Batch-resurser.

**Tjänsten logghändelser**
* [Skapa poolen](batch-pool-create-event.md)
* [Poolen delete start](batch-pool-delete-start-event.md)
* [Poolen ta bort klar](batch-pool-delete-complete-event.md)
* [Poolen storleksändring start](batch-pool-resize-start-event.md)
* [Poolen storleksändring slutförd](batch-pool-resize-complete-event.md)
* [Uppgiften start](batch-task-start-event.md)
* [Uppgift slutförd](batch-task-complete-event.md)
* [Aktiviteten misslyckas](batch-task-fail-event.md)