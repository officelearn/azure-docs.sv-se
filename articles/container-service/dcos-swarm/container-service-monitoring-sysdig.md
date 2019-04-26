---
title: (INAKTUELL) Övervaka ett Azure Container Service-kluster med Sysdig
description: Övervaka ett Azure Container Service-kluster med Sysdig.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 39cd5a3fe7db0c442608e7943e7cbd6e3198edfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60509571"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(INAKTUELL) Övervaka ett Azure Container Service-kluster med Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln ska vi distribuera Sysdig-agenter till alla agentnoder i ditt Azure Container Service-kluster. Du behöver ett konto hos Sysdig för den här konfigurationen. 

## <a name="prerequisites"></a>Nödvändiga komponenter
[Distribuera](container-service-deployment.md) och [anslut](../container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-gränssnittet](container-service-mesos-marathon-ui.md). Gå till [ https://app.sysdigcloud.com ](https://app.sysdigcloud.com) att ställa in ett Sysdig-molnkonto. 

## <a name="sysdig"></a>Sysdig
Sysdig är en övervakningstjänst som gör att du kan övervaka dina containrar i klustret. Sysdig är kända för att hjälpa till med felsökning men innehåller även grundläggande övervakningsmätvärden för processor, nätverk, minne och I/O. Sysdig gör det enkelt att se vilka containrar som arbetar hårdast eller använder mest minne och CPU. Den här vyn finns i avsnittet ”Översikt”, som för närvarande är en betaversion. 

![Sysdig-gränssnitt](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Konfigurera en Sysdig-distribution med Marathon
Här visas hur du konfigurerar och distribuerar Sysdig-program till klustret med Marathon. 

Komma åt DC/OS-Gränssnittet via [ http://localhost:80/ ](http://localhost:80/) en gång i DC/OS-Användargränssnittet navigerar du till ”Universe”, som är längst ned till vänster och söker sedan efter ”Sysdig”.

![Sysdig i DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

Nu för att slutföra konfigurationen behöver du ett Sysdig-molnkonto eller ett kostnadsfritt utvärderingskonto. När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Ange härnäst din snabbtangent i Sysdig-konfigurationen i DC/OS Universe. 

![Sysdig-konfigurationen i DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Nu anger du instanserna till 10000000 så när du har lagt till en ny nod i klustret, kommer Sysdig automatiskt att distribuera en agent till den nya noden. Det här är en tillfällig lösning för att kontrollera att Sysdig distribueras till alla nya agenter i klustret. 

![Sysdig-konfigurationen i DC/OS Universe-instanser](./media/container-service-monitoring-sysdig/sysdig4.png)

När du har installerat paketet navigerar du tillbaka till Sysdig-gränssnittet. Nu kan du utforska de olika användningsmätvärdena för containrarna i klustret. 

Du kan även installera Mesos- och Marathon-specifika instrumentpaneler via [den nya instrumentpanelsguiden](https://app.sysdigcloud.com/#/dashboards/new).
