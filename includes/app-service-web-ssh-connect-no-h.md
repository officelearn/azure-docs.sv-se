---
title: inkludera fil
description: inkludera fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187822"
---
Om du vill öppna en direkt SSH-session med din behållare ska du köra appen.

Klistra in följande URL i webbläsaren och Ersätt \<app-name> med namnet på appen:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Om du inte redan har autentiserats måste du autentisera dig med din Azure-prenumeration för att kunna ansluta. När autentiseringen har autentiserats visas ett gränssnitt i webbläsaren där du kan köra kommandon i din behållare.

![SSH-anslutning](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
