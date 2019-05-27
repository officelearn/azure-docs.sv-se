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
ms.openlocfilehash: aa6f18d4f667862687083c5db3679ce9d8e188cd
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66137466"
---
I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt _&lt;deploymentLocalGitUrl-from-create-step>_ med webbadressen för den fjärranslutna Git som du sparade från [Skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger de autentiseringsuppgifter som du skapade i Konfigurera en distributionsanvändare när Git Credential Manager efterfrågar autentiseringsuppgifter och att du inte använder de autentiseringsuppgifter som du använde när du loggade in på Azure-portalen.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
