---
title: DC/OS-agenten pooler för Azure Container Service
description: Så här fungerar offentliga och privata agent-pooler med ett Azure Container Service DC/OS-kluster
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 81059fd75f0e61324221614c4bb8eccd94203478
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>DC/OS-agenten pooler för Azure Container Service
DC/OS-kluster i Azure Container Service innehålla agent noder i två pooler, en pool med offentliga och privata poolen. Ett program kan distribueras till antingen poolen, påverkar tillgängligheten mellan datorer i container service. Datorerna kan exponerad mot internet (offentlig) eller hålls intern (privat). Den här artikeln ger en kort översikt över därför offentliga och privata pooler.


* **Privata agenter**: privat agent noder kör via ett icke-dirigerbara nätverk. Det här nätverket är endast tillgänglig från zonen admin eller via gränsroutern offentliga zon. Som standard startar DC/OS appar på privata agent noder. 

* **Offentliga agenter**: offentlig agent noder kör DC/OS-appar och tjänster via ett allmänt tillgängligt nätverk. 

Mer information om nätverkssäkerhet för DC/OS finns det [DC/OS-dokumentationen](https://dcos.io/docs/1.7/administration/securing-your-cluster/).

## <a name="deploy-agent-pools"></a>Distribuera agenten pooler

DC/OS-agenten pooler i Azure Container Service skapas på följande sätt:

* Den **privata poolen** innehåller antalet agent noder som du anger när du [distribuera DC/OS-klustret](container-service-deployment.md). 

* Den **offentliga poolen** ursprungligen innehåller ett förbestämt antal agent noder. Den här poolen läggs till automatiskt när DC/OS-klustret har tillhandahållits.

Poolen privata och offentliga poolen är skalningsuppsättningar i virtuella Azure-datorn. Du kan ändra dessa pooler efter distributionen.

## <a name="use-agent-pools"></a>Använda agent-pooler
Som standard **Marathon** distribuerar alla nya program till den *privata* agent noder. Du måste uttryckligen distribuera programmet till den *offentliga* noder under genereringen av programmet. Välj den **valfritt** och ange **slave_public** för den **accepterade resursroller** värde. Den här processen dokumenteras [här](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) och i den [DC/OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) dokumentation.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hantera DC/OS-behållare](container-service-mesos-marathon-ui.md).

* Lär dig hur du [öppna brandväggen](container-service-enable-public-access.md) tillhandahålls av Azure för att tillåta offentlig åtkomst till ditt DC/OS-behållare.

