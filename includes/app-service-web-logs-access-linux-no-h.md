---
title: inkludera fil
description: inkludera fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905637"
---
Du kan komma åt konsol loggarna som genereras inifrån behållaren. Börja med att aktivera behållar loggning genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

När containerloggning har aktiverats kör du följande kommando för att visa loggströmmen:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Om du vill stoppa logg strömningen när som helst skriver du `Ctrl` + `C` .
