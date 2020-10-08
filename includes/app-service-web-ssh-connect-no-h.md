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
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822819"
---
Om du ska öppna en SSH-direktsession med din container måste appen vara igång.

Klistra in följande URL i webbläsaren och ersätt `<app-name>` med namnet på appen:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Om du inte redan har autentiserats måste du autentisera dig med din Azure-prenumeration för att kunna ansluta. När autentiseringen är klar visas ett gränssnitt i webbläsaren där du kan köra kommandon i containern.

![SSH-anslutning](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
