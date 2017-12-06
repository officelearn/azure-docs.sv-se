---
title: "Övervaka Azure DC/OS-kluster - Datadog"
description: "Övervaka ett Azure Container Service-kluster med Datadog. Du kan använda webbgränssnittet för DC/OS för att distribuera Datadog agenter i klustret."
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: b895ef906a8c8f3f8cc21267d80f8b59b64837f4
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>Övervaka ett Azure Container Service DC/OS-kluster med Datadog

I den här artikeln ska vi distribuera Datadog agenter för alla agent-noder i Azure Container Service-kluster. Du behöver ett konto med Datadog för den här konfigurationen. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [anslut](../container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [http://datadoghq.com](http://datadoghq.com) att ställa in ett Datadog-konto. 

## <a name="datadog"></a>Datadog
Datadog är en övervakningstjänsten som samlar in övervakningsdata från din behållare i Azure Container Service-kluster. Datadog har en Docker-integrering instrumentpanel där du kan se specifika mått i en behållare. Mätvärden som samlats in från behållarna ordnas efter CPU, minne, nätverk och i/o. Datadog delar mått i behållare och bilder. Det är ett exempel på hur Gränssnittet ser ut CPU-användning under.

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurera Datadog distribution med Marathon
Dessa steg visar hur du konfigurerar och distribuerar program för Datadog till ditt kluster med Marathon. 

Åtkomst till ditt DC/OS-Gränssnittet via [http://localhost:80 /](http://localhost:80/). En gång i DC/OS-Gränssnittet går du till ”universum” som finns på ned till vänster och sedan söka efter ”Datadog” och klicka på ”installera”.

![Datadog paketet i DC/OS-Universe](./media/container-service-monitoring/datadog1.png)

Nu för att slutföra konfigurationen behöver du ett Datadog konto eller ett kostnadsfritt utvärderingskonto. När du är inloggad utseendet på webbplatsen Datadog till vänster och gå till integreringar -> sedan [API: er](https://app.datadoghq.com/account/settings#api). 

![Datadog API-nyckel](./media/container-service-monitoring/datadog2.png)

Bredvid ange API-nyckel i Datadog konfigurationen i DC/OS-Universe. 

![Datadog konfigurationen i DC/OS-Universe](./media/container-service-monitoring/datadog3.png) 

I konfigurationen ovan instanser är inställda på att 10000000 så när en ny nod läggs till i klustret Datadog automatiskt distribuerar en agent till noden. Det här är en tillfällig lösning. När du har installerat paketet ska du gå tillbaka till webbplatsen Datadog och hitta ”[instrumentpaneler](https://app.datadoghq.com/dash/list)”. Därifrån ser anpassad och integrering instrumentpaneler. Den [Docker instrumentpanelen](https://app.datadoghq.com/screen/integration/docker) har alla behållare mått som du behöver för att övervaka ditt kluster. 

