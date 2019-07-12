---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4e699707db02de07f3d1ebb7d1fa8d0575a10aa3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836942"
---
FTP och lokal Git kan distribuera till en Azure-webbapp med hjälp av en *distributionsanvändare*. När du har konfigurerat din distributionsanvändaren kan du använda det för alla Azure-distributioner. Ditt distributionen på kontonivån användarnamn och lösenord skiljer sig från autentiseringsuppgifterna för din Azure-prenumeration. 

Om du vill konfigurera distributionsanvändaren, kör den [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) i Azure Cloud Shell. Ersätt \<användarnamn > och \<lösenord > med en distribution användarens användarnamn och lösenord. 

- Användarnamnet måste vara unikt i Azure och lokal Git push-meddelanden, får inte innehålla den ' @' symbolen. 
- Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar lösenordet som `null`. Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användarnamn och lösenord för att distribuera dina webbprogram.
