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
ms.openlocfilehash: 4f061dc66829c49fba6722215dc36ed2ce6464ee
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919376"
---
I Azure Cloud Shell, konfigurerar du autentiseringsuppgifter för distribution med den [ `az webapp deployment user set` ](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) kommando. Den här distributionsanvändare krävs för en FTP-distribution och en lokal git-distribution till en webbapp. Det användarnamn och lösenord är på kontonivå. _De är skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration._

I följande exempel ersätter  *\<användarnamn >* och  *\<lösenord >*, inklusive parenteser, med ett nytt användarnamn och lösenord. Användarnamnet måste vara unikt i Azure. Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

Du får JSON-utdata med lösenordet ska visas som `null`. Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet ` 'Bad Request'. Details: 400` ska du använda ett starkare lösenord. Distributionsanvändarnamnet får inte innehålla ' @' symbolen för lokal Git push-meddelanden.

Du konfigurerar den här distributionsanvändare bara en gång. Du kan använda den för alla Azure-distributioner.

> [!NOTE]
> Anteckna användarnamnet och lösenordet. Du kommer att behöva dem för att distribuera webbappen.
>
>
