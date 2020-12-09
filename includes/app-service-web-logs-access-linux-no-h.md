---
title: inkludera fil
description: inkludera fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743808"
---
Du kan komma åt konsol loggarna som genereras inifrån behållaren.

Börja med att aktivera behållar loggning genom att köra följande kommando:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Ersätt `<app-name>` och `<resource-group-name>` med de namn som är lämpliga för din webbapp.

När containerloggning har aktiverats kör du följande kommando för att visa loggströmmen:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Om du vill stoppa logg strömningen när som helst, skriver du **CTRL** + **C**.

Du kan också granska loggfilerna i en webbläsare på `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
