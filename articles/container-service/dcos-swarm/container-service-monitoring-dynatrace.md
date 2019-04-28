---
title: (INAKTUELL) Övervaka Azure DC/OS-kluster – Dynatrace
description: Övervaka ett Azure Container Service DC/OS-kluster med Dynatrace. Distribuera Dynatrace OneAgent med hjälp av DC/OS-instrumentpanelen.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119868"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(INAKTUELL) Övervaka ett Azure Container Service DC/OS-kluster med Dynatrace SaaS/hanterad

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi dig hur du distribuerar den [Dynatrace](https://www.dynatrace.com/) OneAgent att övervaka alla agentnoder i ditt Azure Container Service-kluster. Du behöver ett konto med Dynatrace SaaS/hanterad för den här konfigurationen. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/hanteras
Dynatrace är en molnbaserade övervakningslösning för klustermiljöer och dynamiska behållare. Det hjälper dig att bättre optimera din distribution i behållare och minnesallokeringar med hjälp av användningsdata i realtid. Det kan automatiskt hitta problem med program och infrastruktur genom att tillhandahålla automatisk baslinje, problemet Korrelations- och Rotorsaksanalys.

Följande bild visar Dynatrace UI:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Nödvändiga komponenter 
[Distribuera](container-service-deployment.md) och [ansluta](./../container-service-connect.md) till ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) du ställer in en Dynatrace SaaS-konto.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurera Dynatrace distribution med Marathon
De här stegen visar hur du konfigurerar och distribuerar Dynatrace program till klustret med Marathon.

1. Komma åt DC/OS-Gränssnittet via [ http://localhost:80/ ](http://localhost:80/). En gång i DC/OS-Användargränssnittet navigerar du till den **Universe** fliken och sök sedan efter **Dynatrace**.

    ![Dynatrace i DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Om du vill slutföra konfigurationen behöver du ett Dynatrace SaaS-konto eller ett kostnadsfritt konto. När du loggar in i instrumentpanelen Dynatrace väljer **distribuera Dynatrace**.

    ![Dynatrace konfigurera PaaS-integration](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. På sidan Välj **PaaS Installationsintegrering**. 

    ![Dynatrace API-token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Ange API-token i Dynatrace OneAgent konfigurationen i DC/OS Universe. 

    ![Dynatrace OneAgent konfigurationen i DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ange antalet noder som du avser att köra instanserna. Anger en hög siffra också fungerar, men DC/OS försöker att hitta nya instanser tills det numret faktiskt har uppnåtts. Om du vill kan ange du också det till ett värde som 1000000. I det här fallet när en ny nod läggs till i klustret, distribuerar Dynatrace automatiskt en agent till den nya noden på priset för DC/OS ständigt försöker distribuera ytterligare instanser.

    ![Dynatrace konfigurationen i DC/OS Universe-instanser](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Nästa steg

När du har installerat paketet kan du gå tillbaka till instrumentpanelen Dynatrace. Du kan utforska de olika användningsmätvärdena för behållarna i klustret. 