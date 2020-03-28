---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187819"
---
Du kan komma åt konsolloggarna som genereras inifrån behållaren. Aktivera först behållarloggning genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

När containerloggning har aktiverats kör du följande kommando för att visa loggströmmen:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan också granska loggfilerna `https://<app-name>.scm.azurewebsites.net/api/logs/docker`från webbläsaren på .

Om du vill stoppa loggströmning när som helst skriver du `Ctrl` + `C`.
