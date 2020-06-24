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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905875"
---
Om du vill komma åt konsol loggar som genereras inifrån din program kod i App Service aktiverar du diagnostikloggning genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Möjliga värden för `--level` är: `Error` , `Warning` , `Info` och `Verbose` . På varje efterföljande nivå ingår den föregående nivån. Exempel: `Error` innehåller endast fel meddelanden och `Verbose` innehåller alla meddelanden.

När Diagnostic-loggning har Aktiver ATS kör du följande kommando för att se logg strömmen:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Om du vill stoppa logg strömningen när som helst skriver du `Ctrl` + `C` .
