---
title: Kontinuerlig distribution med Web App for Containers-Azure App Service | Microsoft Docs
description: Så här konfigurerar du kontinuerlig distribution i Web App for Containers.
keywords: Azure App Service, Linux, Docker, ACR, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1dc776f0a61ac1a29ab3fe3ebdd542469863cd50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071364"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med Web App for Containers

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållar avbildning från hanterade [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -databaser eller Docker- [hubb](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Aktivera kontinuerlig distribution med ACR

![Skärm bild av ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj alternativet **App Service** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. På sidan **behållar inställningar** väljer du **en behållare**
5. Välj **Azure Container Registry**
6. Välj **kontinuerlig distribution > på**
7. Välj **Spara** för att aktivera kontinuerlig distribution.

## <a name="use-the-acr-webhook"></a>Använd ACR-webhooken

När kontinuerlig distribution har Aktiver ATS kan du Visa den nyskapade webhooken på sidan Azure Container Registry Webhooks.

![Skärm bild av ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Klicka på Webhooks i Container Registry för att visa de aktuella webhookarna.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Aktivera kontinuerlig distribution med Docker Hub (valfritt)

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj alternativet **App Service** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. På sidan **behållar inställningar** väljer du **en behållare**
5. Välj **Docker-hubb**
6. Välj **kontinuerlig distribution > på**
7. Välj **Spara** för att aktivera kontinuerlig distribution.

![Skärm bild av appens inställning](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopiera webhook-URL: en. Om du vill lägga till en webhook för Docker Hub, följer du Webhooks <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">för Docker Hub</a>.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service på Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en ruby-webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker-/go-webbapp i Web App for Containers](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera webbapp för containrar med Azure CLI](./app-service-linux-cli.md)
