---
title: Kontinuerlig distribution med Web App for Containers – Azure App Service | Microsoft Docs
description: Hur du ställer in kontinuerlig distribution i Web App for Containers.
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
ms.date: 11/08/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4acadc4c08ef50e7d52303689c38c43f81187669
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315544"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med Web App for Containers

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållaravbildning från hanterade [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) databaser eller [Docker Hub](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Aktivera kontinuerlig distribution med ACR

![Skärmbild av ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj den **Apptjänst** alternativ till vänster på sidan.
3. Välj namnet på appen som du vill konfigurera kontinuerlig distribution.
4. På den **Behållarinställningar** väljer **enskild behållare**
5. Välj **Azure Container Registry**
6. Välj **kontinuerlig distribution > på**
7. Välj **spara** du aktiverar kontinuerlig distribution.

## <a name="use-the-acr-webhook"></a>Använd ACR webhook

När kontinuerlig distribution har aktiverats kan visa du den nyligen skapade webhooken på din sida för Azure Container Registry-webhookar.

![Skärmbild av ACR webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Klicka på Webhooks för att visa aktuella webhooks i ditt Behållarregister.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Aktivera kontinuerlig distribution med Docker Hub (valfritt)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj den **Apptjänst** alternativ till vänster på sidan.
3. Välj namnet på appen som du vill konfigurera kontinuerlig distribution.
4. På den **Behållarinställningar** väljer **enskild behållare**
5. Välj **Docker Hub**
6. Välj **kontinuerlig distribution > på**
7. Välj **spara** du aktiverar kontinuerlig distribution.

![Skärmbild av app-inställning](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopiera en Webhook-URL. Så lägger du till en webhook för Docker Hub <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">webhooks för Docker Hub</a>.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service i Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en Ruby-webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker-/ Go-webbapp i Web App for Containers](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera Web App for Containers med Azure CLI](./app-service-linux-cli.md)
