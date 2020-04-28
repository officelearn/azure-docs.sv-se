---
title: FÖRÅLDRAD Övervaka Azure DC/OS-kluster – Datadog
description: Övervaka ett Azure Container Service-kluster med Datadog. Använd webb gränssnittet för DC/OS för att distribuera Datadog-agenterna till klustret.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcb005e39f89298b35bf0f3a0ad1e19601ae4d13
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166161"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>FÖRÅLDRAD Övervaka ett Azure Container Service DC/OS-kluster med Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln ska vi distribuera Datadog-agenter till alla-agent-noder i Azure Container Service klustret. Du behöver ett konto med Datadog för den här konfigurationen. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [anslut](../container-service-connect.md) ett kluster som konfigurerats av Azure Container Service. Utforska [Marathon-användargränssnittet](container-service-mesos-marathon-ui.md). Gå till [https://datadoghq.com](https://datadoghq.com) för att konfigurera ett Datadog-konto. 

## <a name="datadog"></a>Datadog
Datadog är en övervaknings tjänst som samlar in övervaknings data från dina behållare i ditt Azure Container Service-kluster. Datadog har en Docker-integrations instrument panel där du kan se vissa mått i dina behållare. Mått som samlas in från dina behållare ordnas efter processor, minne, nätverk och I/O. Datadog delar upp mått i behållare och avbildningar. Ett exempel på hur gränssnittet ser ut för processor användning finns nedan.

![Datadog-gränssnitt](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Konfigurera en Datadog-distribution med Marathon
De här stegen visar hur du konfigurerar och distribuerar Datadog-program till ditt kluster med Marathon. 

Få åtkomst till ditt DC/OS `http://localhost:80/`-gränssnitt via. När du har aktiverat DC/OS-gränssnittet navigerar du till "universum" som finns längst ned till vänster och sedan söker efter "Datadog" och klickar på "installera".

![Datadog-paket inom DC/OS-universum](./media/container-service-monitoring/datadog1.png)

Nu när du har slutfört konfigurationen behöver du ett Datadog-konto eller ett kostnads fritt utvärderings konto. När du har loggat in på Datadog-webbplatsen tittar du till vänster och går till integrationer – > sedan [API: er](https://app.datadoghq.com/account/settings#api). 

![API-nyckel för Datadog](./media/container-service-monitoring/datadog2.png)

Ange sedan din API-nyckel i Datadog-konfigurationen i DC/OS-universum. 

![Datadog konfiguration i universum för DC/OS](./media/container-service-monitoring/datadog3.png) 

I ovanstående konfigurations instanser anges till 10000000 så när en ny nod läggs till i klustret Datadog distribueras automatiskt en agent till den noden. Detta är en tillfällig lösning. När du har installerat paketet bör du gå tillbaka till Datadog-webbplatsen och hitta "[instrument paneler](https://app.datadoghq.com/dash/list)". Härifrån kan du se anpassade instrument paneler och integrations instrument paneler. [Docker-instrumentpanelen](https://app.datadoghq.com/screen/integration/docker) har alla behållar mått som du behöver för att övervaka klustret. 

