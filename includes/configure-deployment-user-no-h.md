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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836942"
---
FTP och lokala Git kan distribuera till en Azure-webbapp med hjälp av en *distributionsanvändare*. När du har konfigurerat distributionsanvändaren kan du använda den för alla dina Azure-distributioner. Användarnamn och lösenord för distribution på kontonivå skiljer sig från dina Azure-prenumerationsuppgifter. 

Om du vill konfigurera distributionsanvändaren kör du kommandot [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) i Azure Cloud Shell. Ersätt \<användarnamn> och \<lösenord> med användarnamn och lösenord för distributionsanvändare. 

- Användarnamnet måste vara unikt i Azure och för lokala Git-pushes får inte innehålla @-symbolen. 
- Lösenordet måste vara minst åtta tecken långt, med två av följande tre element: bokstäver, siffror och symboler. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON-utdata visar `null`lösenordet som . Om du ser felet `'Conflict'. Details: 409` ska du byta användarnamn. Om du ser felet `'Bad Request'. Details: 400` ska du använda ett starkare lösenord. 

Registrera ditt användarnamn och lösenord som ska användas för att distribuera dina webbappar.
