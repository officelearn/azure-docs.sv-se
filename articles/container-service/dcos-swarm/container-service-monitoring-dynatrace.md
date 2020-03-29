---
title: (FÖRÅLDRAD) Övervaka Azure DC/OS-kluster - Dynatrace
description: Övervaka ett DC/OS-kluster för Azure Container Service med Dynatrace. Distribuera Dynatrace OneAgent med hjälp av DC/OS-instrumentpanelen.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: a82481c5cb3d12b11179b41999f73e67583ec43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277750"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(FÖRÅLDRAD) Övervaka ett DC/OS-kluster för Azure-behållartjänsten med Dynatrace SaaS/Managed

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi hur du distribuerar [Dynatrace](https://www.dynatrace.com/) OneAgent för att övervaka alla agentnoder i azure container service-klustret. Du behöver ett konto hos Dynatrace SaaS/Managed för den här konfigurationen. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace är en molnbaserad övervakningslösning för mycket dynamiska behållare- och klustermiljöer. Det gör att du bättre kan optimera dina behållardistributioner och minnesallokeringar med hjälp av användningsdata i realtid. Den kan automatiskt identifiera program- och infrastrukturproblem genom att tillhandahålla automatiserad baselining, problemkorrelation och identifiering av grundorsak.

Följande bild visar Dynatrace UI:

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Krav 
[Distribuera](container-service-deployment.md) och [ansluta](./../container-service-connect.md) till ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon UI](container-service-mesos-marathon-ui.md). Gå [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) till för att skapa ett Dynatrace SaaS-konto.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Konfigurera en Dynatrace-distribution med Marathon
De här stegen visar hur du konfigurerar och distribuerar Dynatrace-program till ditt kluster med Marathon.

1. Få tillgång till dc/os-användargränssnittet via [http://localhost:80/](http://localhost:80/). En gång i DC / OS UI, navigera till **fliken Universum** och sedan söka efter **Dynatrace**.

    ![Dynatrace i DC/OS Universe](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. För att slutföra konfigurationen behöver du ett Dynatrace SaaS-konto eller ett kostnadsfritt utvärderingskonto. När du har loggat in på Dynatrace-instrumentpanelen väljer du **Distribuera Dynatrace**.

    ![Dynatrace Konfigurera PaaS-integrering](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Välj **Konfigurera PaaS-integrering**på sidan . 

    ![Api-token för Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Ange din API-token i Dynatrace OneAgent-konfigurationen i DC/OS-universumet. 

    ![Dynatrace OneAgent-konfiguration i DC/OS-universumet](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Ange instanserna till antalet noder som du tänker köra. Ange ett högre nummer fungerar också, men DC / OS kommer att fortsätta att försöka hitta nya instanser tills det numret faktiskt nås. Om du vill kan du också ställa in detta till ett värde som 1000000. I det här fallet, när en ny nod läggs till i klustret, distribuerar Dynatrace automatiskt en agent till den nya noden, till priset av DC/OS som ständigt försöker distribuera ytterligare instanser.

    ![Dynatrace-konfigurationen i DC/OS Universe-instanserna](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Nästa steg

När du har installerat paketet navigerar du tillbaka till Dynatrace-instrumentpanelen. Du kan utforska de olika användningsmåtten för behållarna i klustret. 
