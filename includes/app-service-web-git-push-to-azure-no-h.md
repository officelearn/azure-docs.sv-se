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
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681075"
---
I det lokala terminalfönstret lägger du till en Azure-fjärrlagringsplats till din lokala git-lagringsplats. Ersätt *\<deploymentLocalGitUrl-from-create-step>* med webbadressen för den fjärranslutna Git som du sparade från [Skapa en webbapp](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen med följande kommando. När git Credential Manager uppmanas att ange autentiseringsuppgifter, se till att du anger de autentiseringsuppgifter som du skapade i [Konfigurera en distributions användare](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), inte de autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```bash
git push azure master
```

Det kan ett par minuter att köra kommandot. Medan det körs visas information liknande den i följande exempel:
