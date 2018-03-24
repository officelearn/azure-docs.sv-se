---
title: Kontinuerlig distribution från en Docker-behållare registret med webbprogrammet för behållare - Azure | Microsoft Docs
description: Hur du ställer in kontinuerlig distribution från en Docker-behållare registret i webbprogram för behållare.
keywords: Azure apptjänst, linux, docker, acr, oss
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;msangapu
ms.openlocfilehash: ac35dbd041de50ab8aae1a0fb4c00fe3917a7297
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med webbprogrammet för behållare

I kursen får du konfigurera kontinuerlig distribution för en avbildning för anpassade container från hanterade [Azure Container registret](https://azure.microsoft.com/services/container-registry/) databaser eller [Docker-hubb](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Aktivera funktionen kontinuerlig distribution

Aktivera funktionen kontinuerlig distribution med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och köra följande kommando:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

I den [Azure-portalen](https://portal.azure.com/), Välj den **Apptjänst** alternativet till vänster på sidan.

Välj namnet på appen som du vill konfigurera Docker-hubb kontinuerlig distribution.

På den **Dockerbehållare** väljer **på**, och välj sedan **spara** att aktivera kontinuerlig distribution.

![Skärmbild av appinställningen](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Förbereda Webhooksadressen

Hämta Webhooksadressen med [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och köra följande kommando:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

För Webhooksadressen, behöver du följande slutpunkt: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Du kan hämta din `publishingusername` och `publishingpwd` genom att hämta webbprogrammet publiceringsprofil med Azure-portalen.

![Skärmbild för att lägga till webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Lägg till en webhook

### <a name="azure-container-registry"></a>Azure Container Registry

1. Välj på portalsidan registret **Webhooks**.
2. Om du vill skapa en ny webhook **Lägg till**. 
3. I den **skapa webhook** rutan namnge din webhooken. Ange en URL som hämtades i föregående avsnitt för webhook URI.

Kontrollera att du definierar omfattningen som lagringsplatsen som innehåller bilden behållare.

![Skärmbild av webhooken](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

När du uppdaterar avbildningen uppdateras webbprogrammet automatiskt med den nya avbildningen.

### <a name="docker-hub"></a>Docker Hub

Välj på sidan Docker-hubb **Webhooks**, och sedan **skapa en WEBHOOK**.

![Skärmbild för att lägga till webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

Ange en URL som du fick tidigare för webhook-URL.

![Skärmbild för att lägga till webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

När du uppdaterar avbildningen uppdateras webbprogrammet automatiskt med den nya avbildningen.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service på Linux](./app-service-linux-intro.md)
* [Azure-behållaren registret](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en Ruby webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker/gå webbapp i webbprogram för behållare](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera webbprogram för behållare med Azure CLI](./app-service-linux-cli.md)
