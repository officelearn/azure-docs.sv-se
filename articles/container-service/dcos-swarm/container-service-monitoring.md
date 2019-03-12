---
title: (INAKTUELL) Övervaka Azure DC/OS-kluster – Datadog
description: Övervaka ett Azure Container Service-kluster med Datadog. Du kan använda webbgränssnittet för DC/OS för att distribuera Datadog agenterna i klustret.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a094369d467b3b1f3d5fe93f870dccc9eae7519c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57547219"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(INAKTUELL) Övervaka ett Azure Container Service DC/OS-kluster med Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln ska vi distribuera Datadog agenter till alla agentnoder i ditt Azure Container Service-kluster. Du behöver ett konto med Datadog för den här konfigurationen. 

## <a name="prerequisites"></a>Förutsättningar
[Distribuera](container-service-deployment.md) och [anslut](../container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [ https://datadoghq.com ](https://datadoghq.com) du ställer in en Datadog-konto. 

## <a name="datadog"></a>Datadog
Datadog är en övervakningstjänst som samlar in övervakningsdata från dina behållare i Azure Container Service-klustret. Datadog har en instrumentpanel för integrering av Docker där du kan se specifika mått i dina behållare. Mätvärden som samlats in från dina behållare är ordnade efter processor, minne, nätverk och i/o. Datadog delar mått i behållare och bilder. Det är ett exempel på hur Gränssnittet ser ut för CPU-användning under.

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurera Datadog distribution med Marathon
De här stegen visar hur du konfigurerar och distribuerar Datadog program till klustret med Marathon. 

Komma åt DC/OS-Gränssnittet via [ http://localhost:80/ ](http://localhost:80/). En gång i DC/OS-Användargränssnittet navigerar du till ”Universe” som är längst ned till vänster och sök sedan efter ”Datadog” och klicka på ”installera”.

![Datadog paketet i DC/OS Universe](./media/container-service-monitoring/datadog1.png)

Nu för att slutföra konfigurationen behöver ett Datadog-konto eller ett kostnadsfritt konto. När du har loggat in Datadog webbplats titt till vänster och gå till integreringar -> sedan [API: er](https://app.datadoghq.com/account/settings#api). 

![Datadog API-nyckel](./media/container-service-monitoring/datadog2.png)

Ange därefter din API-nyckel i Datadog konfigurationen i DC/OS Universe. 

![Datadog konfigurationen i DC/OS Universe](./media/container-service-monitoring/datadog3.png) 

I konfigurationen ovan ställs instanserna till 10000000 så när en ny nod läggs till i klustret Datadog kommer automatiskt att distribuera en agent till noden. Det här är en tillfällig lösning. När du har installerat paketet ska du gå tillbaka till webbplatsen Datadog och hitta ”[instrumentpaneler](https://app.datadoghq.com/dash/list)”. Därifrån visas anpassade och integrering instrumentpaneler. Den [Docker instrumentpanelen](https://app.datadoghq.com/screen/integration/docker) har alla behållare mått som du behöver för att övervaka ditt kluster. 

