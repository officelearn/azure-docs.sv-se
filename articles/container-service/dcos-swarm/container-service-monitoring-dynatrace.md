---
title: FÖRÅLDRAD Övervaka Azure DC/OS-kluster – dynaTrace
description: Övervaka ett Azure Container Service DC/OS-kluster med dynaTrace. Distribuera dynaTrace-OneAgent med hjälp av DC/OS-instrumentpanelen.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277750"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>FÖRÅLDRAD Övervaka ett Azure Container Service DC/OS-kluster med dynaTrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi hur du distribuerar [dynaTrace](https://www.dynatrace.com/) -OneAgent för att övervaka alla agent-noder i Azure Container Service klustret. Du behöver ett konto med dynaTrace SaaS/Managed för den här konfigurationen. 

## <a name="dynatrace-saasmanaged"></a>DynaTrace SaaS/Managed
DynaTrace är en molnbaserad övervaknings lösning för hög dynamisk behållare och kluster miljöer. Det gör att du bättre kan optimera dina behållar distributioner och minnes tilldelningar genom att använda real tids användnings data. Den kan automatiskt lokalisera program-och infrastruktur problem genom att tillhandahålla automatiserade bas linje, problem korrelation och rotor Saks identifiering.

Följande bild visar dynaTrace-gränssnittet:

![DynaTrace-gränssnitt](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Krav 
[Distribuera](container-service-deployment.md) och [Anslut](./../container-service-connect.md) till ett kluster som kon figurer ATS av Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) om du vill konfigurera ett dynaTrace SaaS-konto.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurera en dynaTrace-distribution med Marathon
De här stegen visar hur du konfigurerar och distribuerar dynaTrace-program till ditt kluster med Marathon.

1. Få åtkomst till ditt DC/OS-gränssnitt via [http://localhost:80/](http://localhost:80/). I användar gränssnittet för DC/OS går du till fliken **universum** och söker efter **dynaTrace**.

    ![DynaTrace i DC/OS-universum](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. För att slutföra konfigurationen behöver du ett dynaTrace SaaS-konto eller ett kostnads fritt utvärderings konto. När du har loggat in på dynaTrace-instrumentpanelen väljer du **distribuera dynaTrace**.

    ![DynaTrace konfigurera PaaS-integrering](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. På sidan väljer du **Konfigurera PaaS-integrering**. 

    ![DynaTrace-API-token](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Ange din API-token i dynaTrace OneAgent-konfigurationen i DC/OS-universum. 

    ![DynaTrace OneAgent-konfiguration i DC/OS-universum](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ställ in instanserna på antalet noder som du vill köra. Att ställa in en högre siffra fungerar också, men DC/OS fortsätter att försöka hitta nya instanser tills numret faktiskt har nåtts. Om du vill kan du också ställa in det på ett värde som 1000000. I det här fallet, när en ny nod läggs till i klustret, distribuerar dynaTrace automatiskt en agent till den nya noden, till priset för DC/OS försöker hela tiden att distribuera ytterligare instanser.

    ![DynaTrace konfiguration i universum för DC/OS-instanser](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Nästa steg

När du har installerat paketet går du tillbaka till dynaTrace-instrumentpanelen. Du kan utforska de olika användnings måtten för behållarna i klustret. 
