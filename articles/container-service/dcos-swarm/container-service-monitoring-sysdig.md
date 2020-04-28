---
title: FÖRÅLDRAD Övervaka ett Azure Container Service kluster med Sysdig
description: Övervaka ett Azure Container Service-kluster med Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 611d3e6d5ecaf986cc09c550c4aee760ed0a0d98
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166163"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>FÖRÅLDRAD Övervaka ett Azure Container Service kluster med Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln ska vi distribuera Sysdig-agenter till alla agentnoder i ditt Azure Container Service-kluster. Du behöver ett konto hos Sysdig för den här konfigurationen. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [anslut](../container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-användargränssnittet](container-service-mesos-marathon-ui.md). Gå till [https://app.sysdigcloud.com](https://app.sysdigcloud.com) för att konfigurera ett Sysdig Cloud-konto. 

## <a name="sysdig"></a>Sysdig
Sysdig är en övervakningstjänst som gör att du kan övervaka dina containrar i klustret. Sysdig är kända för att hjälpa till med felsökning men innehåller även grundläggande övervakningsmätvärden för processor, nätverk, minne och I/O. Sysdig gör det enkelt att se vilka containrar som arbetar hårdast eller använder mest minne och CPU. Den här vyn finns i avsnittet ”Översikt”, som för närvarande är en betaversion. 

![Sysdig-gränssnitt](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Konfigurera en Sysdig-distribution med Marathon
Här visas hur du konfigurerar och distribuerar Sysdig-program till klustret med Marathon. 

Kom åt ditt DC/OS- `http://localhost:80/` gränssnitt via en gång i DC/OS-gränssnittet navigera till "universum", som finns längst ned till vänster och Sök sedan efter "Sysdig".

![Sysdig i DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

Nu för att slutföra konfigurationen behöver du ett Sysdig-molnkonto eller ett kostnadsfritt utvärderingskonto. När du har loggat in på Sysdig-molnwebbplatsen klickar du på ditt användarnamn. Din ”snabbtangent” bör nu visas på sidan. 

![API-nyckel för Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Ange härnäst din snabbtangent i Sysdig-konfigurationen i DC/OS Universe. 

![Sysdig-konfigurationen i DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

Nu anger du instanserna till 10000000 så när du har lagt till en ny nod i klustret, kommer Sysdig automatiskt att distribuera en agent till den nya noden. Det här är en tillfällig lösning för att kontrollera att Sysdig distribueras till alla nya agenter i klustret. 

![Sysdig-konfigurationen i DC/OS Universe-instanser](./media/container-service-monitoring-sysdig/sysdig4.png)

När du har installerat paketet navigerar du tillbaka till Sysdig-gränssnittet. Nu kan du utforska de olika användningsmätvärdena för containrarna i klustret. 

Du kan även installera Mesos- och Marathon-specifika instrumentpaneler via [den nya instrumentpanelsguiden](https://app.sysdigcloud.com/#/dashboards/new).
