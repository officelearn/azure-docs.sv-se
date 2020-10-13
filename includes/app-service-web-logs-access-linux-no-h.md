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
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91822820"
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
