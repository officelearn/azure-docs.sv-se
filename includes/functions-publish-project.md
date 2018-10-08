---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/27/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 1b553cbd720fcb76899844712ce5053af46f7ccb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452963"
---
## <a name="deploy-the-function-app-project-to-azure"></a>Distribuera funktionsapprojektet till Azure

När funktionsappen har skapats i Azure kan du använda kommandot [`func azure functionapp publish`](../articles/azure-functions/functions-run-local.md#project-file-deployment) för att distribuera projektkoden till Azure.

```bash
func azure functionapp publish <FunctionAppName>
```

Du ser något som liknar följande utdata, som har trunkerats för läsbarhetens skull.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

Nu kan du testa dina funktioner i Azure.