---
title: "Kontinuerlig distribution från en Docker-behållare registret med webbprogrammet för behållare - Azure | Microsoft Docs"
description: "Hur du ställer in kontinuerlig distribution från en Docker-behållare registret i webbprogram för behållare."
keywords: "Azure apptjänst, linux, docker, acr, oss"
services: app-service
documentationcenter: 
author: ahmedelnably
manager: cfowler
editor: 
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: aelnably;wesmc
ms.openlocfilehash: 9759eac01e2db62154cd7f6bf9fcccf13282b4b4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med webbprogrammet för behållare

I kursen får du konfigurera kontinuerlig distribution för en avbildning för anpassade container från hanterade [Azure Container registret](https://azure.microsoft.com/services/container-registry/) databaser eller [Docker-hubb](https://hub.docker.com).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på den [Azure-portalen](https://portal.azure.com)

## <a name="enable-container-continuous-deployment-feature"></a>Aktivera funktionen för behållaren kontinuerlig distribution

Du kan aktivera funktionen kontinuerlig distribution med [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och köra följande kommando

```azurecli-interactive
az webapp deployment container config -n sname -g rgname -e true
```

I den  **[Azure-portalen](https://portal.azure.com/)**, klicka på den **Apptjänst** alternativet till vänster på sidan.

Klicka på namnet på din app som du vill konfigurera Docker-hubb kontinuerlig distribution för.

I **Dockerbehållare**, Välj 'På' och tryck sedan på Spara för att aktivera kontinuerlig distribution.

![infoga bilden app-inställning](./media/app-service-webapp-service-linux-ci-cd/step2.png)

## <a name="prepare-webhook-url"></a>Förbereda Webhooksadressen

Du kan hämta Webhooksadressen med [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) och köra följande kommando

```azurecli-interactive
az webapp deployment container show-cd-url -n sname1 -g rgname
```

För Webhooksadressen, måste du ha följande slutpunkt: `https://<publishingusername>:<publishingpwd>@<sitename>.scm.azurewebsites.net/docker/hook`.

Du kan hämta din `publishingusername` och `publishingpwd` genom att hämta webbprogrammet publiceringsprofil med Azure-portalen.

![infoga bilden för att lägga till webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-3.png)

## <a name="add-a-web-hook"></a>Lägg till en webbtjänst hook

### <a name="azure-container-registry"></a>Azure Container Registry

I din registret portalbladet klickar du på **Webhooks**, klicka på Skapa en ny webhook **Lägg till**. I den **skapa webhook** bladet namnge din webhooken. För Webhook-URI måste du ange URL: en från **steg3**

Kontrollera att du definierar omfattningen som lagringsplatsen som innehåller bilden behållare.

![Infoga bild av webhooken](./media/app-service-webapp-service-linux-ci-cd/step3ACRWebhook-1.png)

När avbildningen uppdateras hämta webbprogrammet uppdateras automatiskt med den nya avbildningen.

### <a name="docker-hub"></a>Docker Hub

På din hubb Docker klickar du på **Webhooks**, sedan **skapa en WEBHOOK**.

![infoga bilden för att lägga till webhook 1](./media/app-service-webapp-service-linux-ci-cd/step3-1.png)

För Webhooksadressen, måste du ange en URL som hämtats från **steg3**

![infoga bilden för att lägga till webhook 2](./media/app-service-webapp-service-linux-ci-cd/step3-2.png)

När avbildningen uppdateras hämta webbprogrammet uppdateras automatiskt med den nya avbildningen.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure App Service på Linux?](./app-service-linux-intro.md)
* [Azure-behållaren registret](https://azure.microsoft.com/services/container-registry/)
* [Använda .NET Core i Azure App Service i Linux](quickstart-dotnetcore.md)
* [Använda Ruby i Azure App Service i Linux](quickstart-ruby.md)
* [Så här använder du en anpassad Docker-avbildning för Web App for Containers](quickstart-docker-go.md)
* [Azure App Service – Vanliga frågor om Web App for Containers](./app-service-linux-faq.md)
* [Hantera webbprogram för behållare som använder Azure CLI 2.0](./app-service-linux-cli.md)
