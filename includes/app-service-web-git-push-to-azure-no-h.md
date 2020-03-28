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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75681075"
---
I det lokala terminalfönstret kan du lägga till en Azure-fjärrdatabas till din lokala Git-databas. Ersätt * \<distributionLokalGitUrl-from-create-step>* med URL:en för Git-fjärrkontrollen som du sparade från [Skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När Git-autentiseringshanteraren frågar dig efter autentiseringsuppgifter kontrollerar du att du anger de autentiseringsuppgifter som du skapade i [Konfigurera en distributionsanvändare](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), inte de autentiseringsuppgifter du använder för att logga in på Azure-portalen.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
