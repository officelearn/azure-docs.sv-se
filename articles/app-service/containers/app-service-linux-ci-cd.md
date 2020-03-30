---
title: CI/CD till anpassade Linux-behållare
description: Lär dig hur du konfigurerar kontinuerlig distribution till en anpassad Linux-behållare i Azure App Service. Kontinuerlig distribution stöds för Docker Hub och ACR.
keywords: azure app tjänst, Linux, docker, acr, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687623"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med Web App for Containers

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållaravbildning från hanterade [Azure Container-registerdatabaser](https://azure.microsoft.com/services/container-registry/) eller [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Aktivera kontinuerlig distribution med ACR

![Skärmbild av ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj alternativet **Apptjänst** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. Välj **En behållare på** sidan **Behållarinställningar**
5. Välj **Azure-behållarregister**
6. Välj **kontinuerlig > på**
7. Välj **Spara** om du vill aktivera kontinuerlig distribution.

## <a name="use-the-acr-webhook"></a>Använd ACR webhook

När kontinuerlig distribution har aktiverats kan du visa den nyskapade webhooken på din azure-behållareregisterwebbhooks-sida.

![Skärmbild av ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

I behållarregistret klickar du på Webhooks för att visa de aktuella webhooks.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Aktivera kontinuerlig distribution med Docker Hub (valfritt)

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj alternativet **Apptjänst** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. Välj **En behållare på** sidan **Behållarinställningar**
5. Välj **Docker-hubb**
6. Välj **kontinuerlig > på**
7. Välj **Spara** om du vill aktivera kontinuerlig distribution.

![Skärmbild av appinställning](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopiera Webhook-URL:en. Om du vill lägga till en webhook för Docker Hub följer du <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks för Docker Hub</a>.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service i Linux](./app-service-linux-intro.md)
* [Azure-behållarregister](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en Ruby-webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker-/Go-webbapp i Web App for Containers](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera webbapp för containrar med Azure CLI](./app-service-linux-cli.md)
