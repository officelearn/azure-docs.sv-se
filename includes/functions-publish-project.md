---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 48bb91b3b2e9a31de63e515edb857bc2a170ea79
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132258"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När funktionsappen har skapats i Azure, kan du använda den [ `func azure functionapp publish` ](../articles/azure-functions/functions-run-local.md#project-file-deployment) Core Tools-kommando för att distribuera projektkoden till Azure. I följande kommando, ersätter `<APP_NAME>` med namnet på din app från föregående steg.

```bash
func azure functionapp publish <APP_NAME>
```

Du kan se utdata som liknar följande, vilket har trunkerats för läsbarhet.

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

Kopiera anropa URL-värdet för din HttpTrigger som du kan nu använda för att testa din funktion i Azure. URL: en innehåller en `code` frågan strängvärdet som är din funktionsnyckel. Den här nyckeln blir det svårare för andra att anropa din slutpunkt för HTTP-utlösare i Azure.