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
ms.openlocfilehash: b5512ed5810ebd05596dc3ca7a29957b4c595c59
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50035414"
---
I Cloud Shell skapar du autentiseringsuppgifter för distribution med kommandot [`az webapp deployment user set`](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set). Den här distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp. Användarnamn och lösenord är på kontonivå. _De skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration._

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
