---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 463e8b0122339831d5d6a65b6e41d4f697a82013
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
I Cloud Shell skapar du autentiseringsuppgifter för distribution med kommandot [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az_webapp_deployment_user_set). Den här distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp. Användarnamn och lösenord är på kontonivå. _De skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration._

I följande exempel ersätter du *\<username>* och *\<password>* (inklusive parenteser) med ett nytt användarnamn och lösenord. Användarnamnet måste vara unikt inom Azure. Lösenordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror, symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Du bör få JSON-utdata, och lösenordet ska visas som `null`. Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet ` 'Bad Request'. Details: 400` ska du använda ett starkare lösenord.

Du behöver bara skapa distributionsanvändaren en gång. Du kan använda den för alla Azure-distributioner.

> [!NOTE]
> Anteckna användarnamn och lösenord. Du kommer att behöva dem för att distribuera webbappen.
>
>
