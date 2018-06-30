---
title: Kontinuerlig distribution från en Docker-behållare registret med webbprogrammet för behållare - Azure | Microsoft Docs
description: Hur du ställer in kontinuerlig distribution från en Docker-behållare registret i webbprogram för behållare.
keywords: Azure apptjänst, linux, docker, acr, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2018
ms.author: msangapu
ms.openlocfilehash: 0f2d4626308eed376b71f1b3df2f9e43f1b2a4f7
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130981"
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

Anteckna Webhooksadressen. Du behöver det i nästa avsnitt.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Du kan hämta din `publishingusername` och `publishingpwd` genom att hämta webbprogrammet publiceringsprofil med Azure-portalen.

![Skärmbild för att lägga till webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Lägg till en webhook

Följ stegen i de här guiderna för att lägga till en webhook:

- [Azure Container registret](../../container-registry/container-registry-webhook.md) med Webhooksadressen
- [Webhooks för Docker-hubb](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service på Linux](./app-service-linux-intro.md)
* [Azure-behållaren registret](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en Ruby webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker/gå webbapp i webbprogram för behållare](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera webbprogram för behållare med Azure CLI](./app-service-linux-cli.md)
