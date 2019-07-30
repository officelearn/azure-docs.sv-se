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
ms.openlocfilehash: fbb537c9584c948af37694b3bfc77a7c345e084d
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639101"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När du har skapat Function-appen i Azure kan du använda [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) kommandot Core Tools för att distribuera projekt koden till Azure. I följande kommando ersätter `<APP_NAME>` du med namnet på din app från föregående steg.

```bash
func azure functionapp publish <APP_NAME>
```

Du ser utdata som liknar följande, som har trunkerats för läsbarhet:

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

Kopiera värdet för din `HttpTrigger`, som du nu kan använda för att testa din funktion i Azure. `Invoke url` URL: en innehåller `code` ett sträng värde för frågan som är din funktions nyckel. Den här nyckeln gör det svårt för andra att anropa en slut punkt för HTTP-utlösare i Azure.