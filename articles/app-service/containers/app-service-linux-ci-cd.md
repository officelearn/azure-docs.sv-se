---
title: Kontinuerlig distribution från en Docker-behållarregister med Web App for Containers – Azure | Microsoft Docs
description: Hur du ställer in kontinuerlig distribution från en Docker-behållarregister i Web App for Containers.
keywords: Azure app service, linux, docker, acr, oss
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
ms.openlocfilehash: 20ca63b7126a6800538129115ff339308c11d8c5
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867040"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med Web App for Containers

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållaravbildning från hanterade [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) databaser eller [Docker Hub](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="enable-the-continuous-deployment-feature"></a>Aktivera funktionen kontinuerlig distribution

Aktivera funktionen kontinuerlig distribution med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och köra följande kommando:

```azurecli-interactive
az webapp deployment container config --name name --resource-group myResourceGroup --enable-cd true
```

I den [Azure-portalen](https://portal.azure.com/)väljer den **Apptjänst** alternativ till vänster på sidan.

Välj namnet på appen som du vill konfigurera kontinuerlig distribution av Docker Hub.

På den **Behållarinställningar** väljer **på**, och välj sedan **spara** du aktiverar kontinuerlig distribution.

![Skärmbild av app-inställning](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-the-webhook-url"></a>Förbereda en webhook-URL

Hämta en webhook-URL med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och köra följande kommando:

```azurecli-interactive
az webapp deployment container show-cd-url --name sname1 --resource-group rgname
```

Anteckna webhook-URL. Du behöver det i nästa avsnitt.
`https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Du kan hämta din `publishingusername` och `publishingpwd` genom att ladda ned webbappen publiceringsprofil med Azure portal.

![Skärmbild för att lägga till webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-webhook"></a>Lägg till en webhook

Om du vill lägga till en webhook, följer du stegen i de här guiderna:

- [Azure Container Registry](../../container-registry/container-registry-webhook.md) med webhook-URL
- [Webhooks för Docker Hub](https://docs.docker.com/docker-hub/webhooks/)

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service i Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en Ruby-webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker-/ Go-webbapp i Web App for Containers](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera Web App for Containers med Azure CLI](./app-service-linux-cli.md)
