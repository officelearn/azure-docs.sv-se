---
title: (FÖRÅLDRAD) DC/OS-agentpooler för Azure Container Service
description: Så här fungerar de offentliga och privata agentpoolerna med ett DC/OS-kluster för Azure Container Service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278387"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(FÖRÅLDRAD) DC/OS-agentpooler för Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS-kluster i Azure Container Service innehåller agentnoder i två pooler, en offentlig pool och en privat pool. Ett program kan distribueras till båda poolerna, vilket påverkar tillgängligheten mellan datorer i behållartjänsten. Maskinerna kan utsättas för internet (offentliga) eller hållas interna (privata). Den här artikeln ger en kort översikt över varför det finns offentliga och privata pooler.


* **Privata agenter**: Privata agentnoder körs genom ett icke-dirigerbart nätverk. Det här nätverket är endast tillgängligt från administratörszonen eller via routern för den offentliga zonens kant. Som standard startar DC/OS appar på privata agentnoder. 

* **Offentliga agenter**: Offentliga agentnoder kör DC/OS-appar och -tjänster via ett allmänt tillgängligt nätverk. 

Mer information om dc/os-nätverkssäkerhet finns i [DC/OS-dokumentationen](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Distribuera agentpooler

DC/OS-agentpoolerna i Azure Container Service skapas på följande sätt:

* Den **privata poolen** innehåller antalet agentnoder som du anger när du [distribuerar DC/OS-klustret](container-service-deployment.md). 

* Den **offentliga poolen** innehåller ursprungligen ett förutbestämt antal agentnoder. Den här poolen läggs till automatiskt när DC/OS-klustret etableras.

Den privata poolen och den offentliga poolen är Azure-skalningsuppsättningar för virtuella datorer. Du kan ändra storlek på dessa pooler efter distributionen.

## <a name="use-agent-pools"></a>Använda agentpooler
Som standard distribuerar **Marathon** alla nya program till de *privata* agentnoderna. Du måste uttryckligen distribuera programmet till de *offentliga* noderna när programmet skapas. Välj fliken **Valfritt** och ange **slave_public** för värdet **Godkända resursroller.** Denna process dokumenteras [här](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) och i [DC/OS-dokumentationen.](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/)

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du hanterar dc/os-behållarna](container-service-mesos-marathon-ui.md).

* Lär dig hur du [öppnar brandväggen](container-service-enable-public-access.md) som tillhandahålls av Azure för att ge allmänheten åtkomst till dina DC/OS-behållare.

