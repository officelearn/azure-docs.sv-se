---
title: Övervaka Azure DC/OS-kluster - Dynatrace
description: Övervaka ett Azure Container Service DC/OS-kluster med Dynatrace. Distribuera Dynatrace OneAgent med hjälp av DC/OS-instrumentpanelen.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 3d1bfc3bb61781d487c40831edd5da6fcb5a7df9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162049"
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>Övervaka ett Azure Container Service DC/OS-kluster med Dynatrace SaaS/hanteras

I den här artikeln vi hur du distribuerar den [Dynatrace](https://www.dynatrace.com/) OneAgent att övervaka alla agent-noder i Azure Container Service-kluster. Du behöver ett konto med Dynatrace SaaS/hanteras för den här konfigurationen. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/hanteras
Dynatrace är en moln-intern övervakningslösning för klustret miljöer och mycket dynamisk behållare. På så sätt kan du optimera din distribution i behållare och en minnesallokering bättre genom att använda data i realtid. Den är kapabel att automatiskt lokalisera programmet och infrastruktur problem genom att tillhandahålla automatisk baslinjeuppgifter och problemet korrelation grundorsaken identifiering.

Följande bild visar Dynatrace UI:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Förutsättningar 
[Distribuera](container-service-deployment.md) och [ansluta](./../container-service-connect.md) till ett kluster som har konfigurerats med Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [ https://www.dynatrace.com/trial/ ](https://www.dynatrace.com/trial/) att ställa in en Dynatrace SaaS-konto.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurera Dynatrace distribution med Marathon
Dessa steg visar hur du konfigurerar och distribuerar program för Dynatrace till ditt kluster med Marathon.

1. Åtkomst till ditt DC/OS-Gränssnittet via [ http://localhost:80/ ](http://localhost:80/). En gång i DC/OS-Gränssnittet går du till den **Universe** fliken och sök sedan efter **Dynatrace**.

    ![Dynatrace i DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Om du vill slutföra konfigurationen behöver du ett Dynatrace SaaS-konto eller ett kostnadsfritt utvärderingskonto. När du loggar in i instrumentpanelen Dynatrace välja **distribuera Dynatrace**.

    ![Dynatrace konfigurera PaaS-integrering](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. På sidan Välj **ställa in integration PaaS**. 

    ![Dynatrace API-token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Ange din API-token i Dynatrace OneAgent konfigurationen i DC/OS-Universe. 

    ![Dynatrace OneAgent konfigurationen i DC/OS-Universe](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ange antalet noder som du vill köra instanserna. Anger en hög siffra också fungerar, men DC/OS kommer att försöka hitta nya instanser tills numret faktiskt har uppnåtts. Om du vill kan du också ställa in det till ett värde som 1000000. I det här fallet när en ny nod läggs till i klustret, distribuerar Dynatrace automatiskt en agent till den nya noden i priset för DC/OS ständigt försöker distribuera ytterligare instanser.

    ![Dynatrace konfigurationen i DC/OS Universe-instanser](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Nästa steg

När du har installerat paketet kan du gå tillbaka till instrumentpanelen Dynatrace. Du kan utforska olika användningsstatistik för behållarna i klustret. 