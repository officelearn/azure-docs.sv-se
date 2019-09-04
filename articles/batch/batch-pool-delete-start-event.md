---
title: Start händelse för borttagning av Azure Batch pool | Microsoft Docs
description: Referens för start händelse för borttagning av batch-pool.
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
ms.openlocfilehash: 76f3a15d88166160f28461c34ee03ff7c8880a59
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258420"
---
# <a name="pool-delete-start-event"></a>Starthändelse för borttagning av pool

 Den här händelsen genereras när en borttagning av en pool har startat. Eftersom poolen ta bort är en asynkron händelse kan du förvänta en borttagnings händelse för poolen som ska genereras när borttagnings åtgärden har slutförts.

 I följande exempel visas innehållet i en start händelse för pool borttagning.

```
{
    "id": "myPool1"
}
```

|Element|type|Anteckningar|
|-------------|----------|-----------|
|`id`|Sträng|Poolens ID.|
