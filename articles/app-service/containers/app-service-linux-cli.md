---
title: Hantera anpassade Linux-behållare med CLI
description: Lär dig hur du hanterar anpassade Linux-behållare i Azure App Service från kommandoraden. Automatisera appetablering eller underhåll.
keywords: azure app tjänst, webbapp, cli, linux, oss
ms.topic: article
ms.date: 08/22/2017
ms.custom: seodec18
ms.openlocfilehash: 5ca5322467402af710df68c82d747f8f8d65e142
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255926"
---
# <a name="manage-web-app-for-containers-using-azure-cli"></a>Hantera webbapp för containrar med Azure CLI

Med hjälp av kommandona i den här artikeln kan du skapa och hantera en web app för behållare med hjälp av Azure CLI.
Du kan börja använda den nya versionen av CLI på två sätt:

* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) på datorn.
* Använda [Azure Cloud Shell (förhandsversion)](../../cloud-shell/overview.md)

## <a name="create-a-linux-app-service-plan"></a>Skapa en Tjänstplan för Linux-appar

Om du vill skapa en Linux App Service Plan kan du använda följande kommando:

```azurecli-interactive
az appservice plan create -n appname -g rgname --is-linux -l "South Central US" --sku S1 --number-of-workers 1
```

## <a name="create-a-custom-docker-container-web-app"></a>Skapa en anpassad Docker-behållare Web App

Om du vill skapa en webbapp och konfigurera den så att den kör en anpassad Docker-behållare kan du använda följande kommando:

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -i elnably/dockerimagetest
```

## <a name="activate-the-docker-container-logging"></a>Aktivera loggning av Docker-behållare

Om du vill aktivera Docker-behållarloggningen kan du använda följande kommando:

```azurecli-interactive
az webapp log config -n sname -g rgname --web-server-logging filesystem
```

## <a name="change-the-custom-docker-container-for-an-existing-web-app-for-containers-app"></a>Ändra den anpassade Docker-behållaren för en befintlig Web App for Containers-app

Om du vill ändra en tidigare skapad app, från den aktuella Docker-avbildningen till en ny avbildning, kan du använda följande kommando:

```azurecli-interactive
az webapp config container set -n sname -g rgname -c apurvajo/mariohtml5
```

## <a name="using-docker-images-from-a-private-registry"></a>Använda Docker-avbildningar från ett privat register

Du kan konfigurera appen så att den använder avbildningar från ett privat register. Du måste ange url:en för registret, användarnamnet och lösenordet. Detta kan uppnås med hjälp av följande kommando:

```azurecli-interactive
az webapp config container set -n sname1 -g rgname -c <container name> -r <server url> -u <username> -p <password>
```

## <a name="enable-continuous-deployments-for-custom-docker-images"></a>Aktivera kontinuerliga distributioner för anpassade Docker-avbildningar

Med följande kommando kan du aktivera CD-funktionen och hämta webhook url. Den här url:en kan användas för att konfigurera du TaerHub- eller Azure Container Registry-repos.

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

## <a name="create-a-web-app-for-containers-app-using-one-of-our-built-in-runtime-frameworks"></a>Skapa en web app för containers-appen med hjälp av något av våra inbyggda runtime-ramverk

Om du vill skapa en PHP 5.6 Web App for Containers App som du kan använda följande kommando.

```azurecli-interactive
az webapp create -n sname -g rgname -p pname -r "php|5.6"
```

## <a name="change-framework-version-for-an-existing-web-app-for-containers-app"></a>Ändra ramversion för en befintlig web app för containers-appen

Om du vill ändra en tidigare skapad app, från den aktuella ramversionen till Node.js 6.11, kan du använda följande kommando:

```azurecli-interactive
az webapp config set -n sname -g rgname --linux-fx-version "node|6.11"
```

## <a name="set-up-git-deployments-for-your-web-app"></a>Konfigurera Git-distributioner för din webbapp

Om du vill konfigurera Git-distributioner för din app kan du använda följande kommando:

```azurecli-interactive
az webapp deployment source config -n sname -g rgname --repo-url <gitrepo url> --branch <branch>
```

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure App Service på Linux?](app-service-linux-intro.md)
* [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Azure Cloud Shell (förhandsversion)](../../cloud-shell/overview.md)
* [Konfigurera mellanlagringsmiljöer i Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Kontinuerlig distribution med Web App för behållare](app-service-linux-ci-cd.md)
