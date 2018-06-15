---
title: Azure Batch Analytics | Microsoft Docs
description: Referens för Azure Batch Analytics.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: 4c81acb282b24bbd899227c4dcc449fed4ba6c7d
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312685"
---
# <a name="batch-analytics"></a>Batchanalys
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