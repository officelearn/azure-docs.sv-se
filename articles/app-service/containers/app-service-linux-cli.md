---
title: Hantera webbprogram för behållare som använder Azure CLI 2.0 | Microsoft Docs
description: Hantera webbprogram för behållare med Azure CLI.
keywords: Azure apptjänst, webbprogram, cli, linux, oss
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
ms.openlocfilehash: 54c979313a6ffa43008aa9870332b92d2b2f182a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
ms.locfileid: "24105397"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Hantera webbprogram för behållare med Azure CLI

Kommandona i den här artikeln ska du kunna skapa och hantera en Webbapp för behållare som använder Azure CLI 2.0.
Du kan börja använda den nya versionen av CLI på två sätt:

* [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) på din dator.
* Med hjälp av [Azure-molnet Shell (förhandsgranskning)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Skapa en Linux-Programtjänstplan

Om du vill skapa en Linux App Service-Plan kan du använda följande kommando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Skapa en anpassad dockerbehållare Web App

För att skapa en webbapp och konfigurerar den för att köra en anpassad dockerbehållare, kan du använda följande kommando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivera loggning för Docker-behållare

Du kan använda följande kommando för att aktivera loggning för Docker-behållare:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Ändra anpassade dockerbehållare för en befintlig Webbapp för behållare App

Om du vill ändra en tidigare skapad app från den aktuella Docker-avbildningen till en ny avbildning kan du använda följande kommando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Använda Docker-avbildningar från ett privat register

Du kan konfigurera din app att använda bilder från ett privat register. Du måste ange en url för ditt registret, användarnamn och lösenord. Göra du kan detta med följande kommando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Aktivera kontinuerlig distribution för anpassade Docker-avbildningar

Med det här kommandot kan du aktivera funktionen för CD och få webhooksadressen. URL: en kan användas för att konfigurera DockerHub eller Azure-behållare registret repor.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Skapa ett webbprogram för behållare App med en av våra inbyggda runtime-ramverk

Du kan använda följande kommando för att skapa ett webbprogram för PHP 5.6 för behållare App som.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Ändra framework-version för en befintlig Webbapp för App-behållare

Om du vill ändra en tidigare skapad app från den aktuella framework-versionen till Node.js 6.11 kan du använda följande kommando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Konfigurera Git-distributioner för Webbappen

Om du vill konfigurera Git-distributioner för din app kan du använda följande kommando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure App Service på Linux?](app-service-linux-intro.md)
* [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure-molnet Shell (förhandsgranskning)](../../cloud-shell/overview.md)
* [Skapa mellanlagringsmiljöer i Azure App Service](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution med webbprogrammet för behållare](app-service-linux-ci-cd.md)
