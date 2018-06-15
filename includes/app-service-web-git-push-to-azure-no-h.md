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
ms.openlocfilehash: 125561d61afe0fb7f704144efa1c8c20ecf03db1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
ms.locfileid: "29821053"
---
I det _lokala terminalfönstret_ kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt _&lt;deploymentLocalGitUrl-from-create-step>_ med webbadressen för den fjärranslutna Git som du sparade från [Skapa en webbapp](#create).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. Se till att du anger autentiseringsuppgifterna som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) när du blir ombedd av Git Credential Manager att ange autentiseringsuppgifter, och inte autentiseringsuppgifterna som du använde när du loggade in på Azure Portal.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
