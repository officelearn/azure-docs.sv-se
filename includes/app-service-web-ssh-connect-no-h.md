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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850248"
---
Om du vill att öppna en direkt SSH-session med din behållare, bör du köra din app.

Klistra in följande URL i webbläsaren och Ersätt \<appens namn-> med appnamnet på din:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Om du ännu inte är autentiserad måste du autentisera med Azure-prenumerationen för att ansluta. När autentiseringen är klar visas ett gränssnitt i webbläsaren, där du kan köra kommandon i di behållare.

![SSH-anslutning](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
