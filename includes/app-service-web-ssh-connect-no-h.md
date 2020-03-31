---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187822"
---
Om du vill öppna en direkt SSH-session med behållaren bör appen köras.

Klistra in följande webbadress i \<webbläsaren och ersätt appnamn> med appens namn:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Om du ännu inte är autentiserat måste du autentisera med din Azure-prenumeration för att ansluta. När du har autentiserats visas ett skal i webbläsaren där du kan köra kommandon i behållaren.

![SSH-anslutning](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
