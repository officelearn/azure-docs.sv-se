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
ms.openlocfilehash: e2d63ab38bad341400538c5079fee22737cf0b8e
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562938"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När du har skapat Function-appen i Azure kan du använda [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) kommandot Core Tools för att distribuera projekt koden till Azure. I de här exemplen `<APP_NAME>` ersätter du med namnet på din app från föregående steg.

### <a name="c--javascript"></a>C\# /Java Script

```command
func azure functionapp publish <APP_NAME>
```

### <a name="python"></a>Python

```command
func azure functionapp publish <APP_NAME> --build remote
```

### <a name="typescript"></a>TypeScript

```command
npm run build:production 
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