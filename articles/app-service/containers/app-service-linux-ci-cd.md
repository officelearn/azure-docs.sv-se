---
title: CI/CD till anpassade Linux-behållare
description: Lär dig hur du konfigurerar kontinuerlig distribution till en anpassad Linux-behållare i Azure App Service. Kontinuerlig distribution stöds för Docker Hub och ACR.
keywords: Azure App Service, Linux, Docker, ACR, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d43491de7500204ed470757a1b744017a8180b57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687623"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Kontinuerlig distribution med Web App for Containers

I den här självstudien konfigurerar du kontinuerlig distribution för en anpassad behållar avbildning från hanterade [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) -databaser eller [Docker-hubb](https://hub.docker.com).

## <a name="enable-continuous-deployment-with-acr"></a>Aktivera kontinuerlig distribution med ACR

![Skärm bild av ACR-webhook](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. Logga in på [Azure-portalen](https://portal.azure.com).
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

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj alternativet **App Service** till vänster på sidan.
3. Välj namnet på den app som du vill konfigurera kontinuerlig distribution för.
4. På sidan **behållar inställningar** väljer du **en behållare**
5. Välj **Docker-hubb**
6. Välj **kontinuerlig distribution > på**
7. Välj **Spara** för att aktivera kontinuerlig distribution.

![Skärm bild av appens inställning](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Kopiera webhook-URL: en. Om du vill lägga till en webhook för Docker Hub, följer du <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Webhooks för Docker Hub</a>.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure App Service i Linux](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Skapa en .NET Core-webbapp i App Service på Linux](quickstart-dotnetcore.md)
* [Skapa en ruby-webbapp i App Service på Linux](quickstart-ruby.md)
* [Distribuera en Docker-/Go-webbapp i Web App for Containers](quickstart-docker-go.md)
* [Vanliga frågor och svar om Azure App Service on Linux](./app-service-linux-faq.md)
* [Hantera webbapp för containrar med Azure CLI](./app-service-linux-cli.md)
