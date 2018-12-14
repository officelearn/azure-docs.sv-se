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
ms.openlocfilehash: 95d89da66935ce933fee082a5f53ee2e36ea953f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344694"
---
Konfigurera autentiseringsuppgifter för distribution med i Cloud Shell den [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) kommando. Den här distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp. Användarnamn och lösenord är på kontonivå. _De skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration._

I följande exempel ersätter du *\<username>* och *\<password>* (inklusive parenteser) med ett nytt användarnamn och lösenord. Användarnamnet måste vara unikt inom Azure. Lösenordet måste innehålla minst åtta tecken, med två av följande tre element: bokstäver, siffror, symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Du bör få JSON-utdata, och lösenordet ska visas som `null`. Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet ` 'Bad Request'. Details: 400` ska du använda ett starkare lösenord.

Du måste konfigurera den här distributionsanvändare bara en gång. Du kan använda den för alla Azure-distributioner.

> [!NOTE]
> Anteckna användarnamn och lösenord. Du kommer att behöva dem för att distribuera webbappen.
>
>
