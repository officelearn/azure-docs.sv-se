---
title: (FÖRÅLDRAD) Övervaka Azure DC/OS-kluster - Datadog
description: Övervaka ett Azure Container Service-kluster med Datadog. Använd dc/os-webbgränssnittet för att distribuera Datadog-agenterna till klustret.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275258"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(FÖRÅLDRAD) Övervaka ett DC/OS-kluster för Azure-behållartjänsten med Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln distribuerar vi Datadog-agenter till alla agentnoder i azure container service-klustret. Du behöver ett konto hos Datadog för den här konfigurationen. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [anslut](../container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon UI](container-service-mesos-marathon-ui.md). Gå [https://datadoghq.com](https://datadoghq.com) till för att skapa ett Datadog-konto. 

## <a name="datadog"></a>Datadog (datahund)
Datadog är en övervakningstjänst som samlar in övervakningsdata från dina behållare i azure container service-klustret. Datadog har en Docker Integration Dashboard där du kan se specifika mått i dina behållare. Mått som samlas in från dina behållare ordnas efter CPU, Minne, Nätverk och I/O. Datadog delar upp mätvärden i behållare och bilder. Ett exempel på hur användargränssnittet ser ut för CPU-användning finns nedan.

![Datadog användargränssnitt](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurera en Datadog-distribution med Marathon
De här stegen visar hur du konfigurerar och distribuerar Datadog-program till ditt kluster med Marathon. 

Få tillgång till dc/os-användargränssnittet via [http://localhost:80/](http://localhost:80/). En gång i DC / OS UI navigera till "Universum" som är längst ner till vänster och sedan söka efter "Datadog" och klicka på "Installera".

![Datadog-paketet i DC/OS Universe](./media/container-service-monitoring/datadog1.png)

Nu för att slutföra konfigurationen behöver du ett Datadog-konto eller ett gratis utvärderingskonto. När du är inloggad på Datadogs webbplats tittar du åt vänster och går till Integrationer -> sedan [API:er](https://app.datadoghq.com/account/settings#api). 

![Api-nyckel för Datadog](./media/container-service-monitoring/datadog2.png)

Ange sedan din API-nyckel i Datadog-konfigurationen i DC/OS Universe. 

![Datadog-konfiguration i DC/OS-universumet](./media/container-service-monitoring/datadog3.png) 

I ovanstående konfigurationsinstanser är inställda på 10000000 så när en ny nod läggs till i klustret kommer Datadog automatiskt att distribuera en agent till den noden. Detta är en tillfällig lösning. När du har installerat paketet bör du navigera tillbaka till Datadog webbplats och hitta["Instrumentpaneler](https://app.datadoghq.com/dash/list)." Därifrån ser du anpassade instrumentpaneler och integrationsinstrumentpaneler. [Docker-instrumentpanelen](https://app.datadoghq.com/screen/integration/docker) har alla behållarmått som du behöver för att övervaka klustret. 

