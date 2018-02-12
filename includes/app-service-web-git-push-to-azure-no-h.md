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
ms.openlocfilehash: de3be6fcd9cd1bee4cfc590a41e69d4ae2a2468b
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt _&lt;paste\_copied\_url\_here>_ med URL:en för fjärransluten Git som du sparade från [Skapa en webbapp](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd att ange lösenord och inte lösenordet du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
