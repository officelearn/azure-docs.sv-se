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
ms.openlocfilehash: 70548db9ef98cc8fa59ebc11c1371325e63e02fc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2018
---
Skapa i Cloud Shell en [webbapp](../articles/app-service/app-service-web-overview.md) i `myAppServicePlan` App Service-planen. Du kan göra det med hjälp av kommandot [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). Ersätt *\<app_name>* med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

När webbappen har skapats visar Azure CLI information liknande den i följande exempel:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Du har skapat en tom webbapp med git-distribution aktiverad.

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>

Bläddra till webbappen som precis skapades.

```
http://<app_name>.azurewebsites.net
```

Så här bör din nya webbapp se ut:
