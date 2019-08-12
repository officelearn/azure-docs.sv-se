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
ms.openlocfilehash: 9fefba9c79e9c58956243de8db67ca4cf316251c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950011"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När du har skapat Function-appen i Azure kan du använda [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) kommandot Core Tools för att distribuera projekt koden till Azure. I de här exemplen `<APP_NAME>` ersätter du med namnet på din app från föregående steg.

### <a name="c--javascript"></a>C\# /Java Script

```command
func azure functionapp publish <APP_NAME>
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