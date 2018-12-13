---
title: Hantera Web App for Containers med Azure CLI - Azure App Service | Microsoft Docs
description: Hantera Web App for Containers med Azure CLI.
keywords: Azure apptjänst, webbapp, cli, linux, oss
services: app-service
documentationCenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 0b125903493712dca3b8c8c3a9ce663f9e1b408c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53257649"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Hantera Web App for Containers med Azure CLI

Med hjälp av kommandona i den här artikeln kan du skapa och hantera en Webbapp för behållare med hjälp av Azure CLI.
Du kan börja använda den nya versionen av CLI på två sätt:

* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på din dator.
* Med hjälp av [Azure Cloudshell (förhandsversion)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux App Service-Plan

För att skapa en Linux App Service-Plan, kan du använda följande kommando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Skapa en anpassad Docker-behållare Web App

Om du vill skapa en webbapp och konfigurera den för att köra en anpassad Docker-behållare, kan du använda följande kommando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivera Docker-behållarloggning

Du kan använda följande kommando för att aktivera loggning för Docker-behållare:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Ändra anpassad Docker-behållare för en befintlig Webbapp för Behållarapp

Om du vill ändra en tidigare skapad app från den aktuella Docker-avbildningen till en ny avbildning kan du använda följande kommando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Med hjälp av Docker-avbildningar från ett privat register

Du kan konfigurera din app för att använda avbildningar från ett privat register. Du måste ange en url för ditt register, användarnamn och lösenord. Detta kan uppnås med hjälp av följande kommando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Aktivera kontinuerlig distribution för anpassad Docker-avbildningar

Med följande kommando kan du aktivera CD-funktioner och få en webhook-url. Den här URL: en kan användas för att konfigurera du DockerHub eller Azure Container Registry-lagringsplatser.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Skapa en Webbapp för Behållarapp med någon av våra inbyggda runtime-ramverk

Du kan använda följande kommando för att skapa en PHP-5.6 Webbapp för Behållarapp som.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Ändra framework-version för en befintlig Webbapp för Behållarapp

Du kan använda följande kommando om du vill ändra en tidigare skapad app från den aktuella framework-versionen till Node.js 6.11:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Ställ in Git-distributioner för din Webbapp

Om du vill konfigurera Git-distributioner för din app kan du använda följande kommando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure App Service i Linux?](app-service-linux-intro.md)
* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloudshell (förhandsversion)](../../cloud-shell/overview.md)
* [Konfigurera mellanlagringsmiljöer i Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution med Webbapp for Containers](app-service-linux-ci-cd.md)
