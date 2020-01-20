---
title: FÖRÅLDRAD DC/OS-Gent för Azure Container Service
description: Hur offentliga och privata agenter fungerar med ett Azure Container Service DC/OS-kluster
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278387"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>FÖRÅLDRAD DC/OS-Gent för Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS-kluster i Azure Container Service innehåller agent-noder i två pooler, en offentlig pool och en privat pool. Ett program kan distribueras till en pool, vilket påverkar åtkomsten mellan datorer i behållar tjänsten. Datorerna kan exponeras för Internet (offentligt) eller vara interna (privata). Den här artikeln ger en kort översikt över varför det finns offentliga och privata pooler.


* **Privata agenter**: noder för privata agenter körs via ett icke-dirigerbart nätverk. Det här nätverket är endast tillgängligt från administrations zonen eller via den offentliga zon Edge-routern. Som standard startar DC/OS appar på privata agent-noder. 

* **Offentliga agenter**: offentliga agent-noder kör DC/OS-appar och-tjänster via ett offentligt tillgängligt nätverk. 

Mer information om säkerhet för DC/OS-nätverk finns i [dokumentationen för DC/OS](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Distribuera agent-pooler

DC/OS-agenterna i Azure Container Service skapas på följande sätt:

* Den **privata poolen** innehåller antalet agent-noder som du anger när du [distribuerar DC/OS-klustret](container-service-deployment.md). 

* Den **offentliga poolen** innehåller ursprungligen ett förinställt antal agent-noder. Den här poolen läggs till automatiskt när DC/OS-klustret är etablerad.

Den privata poolen och den offentliga poolen är skalnings uppsättningar för virtuella Azure-datorer. Du kan ändra storlek på dessa pooler efter distribution.

## <a name="use-agent-pools"></a>Använda agent-pooler
Som standard distribuerar **Marathon** alla nya program till de *privata* agent-noderna. Du måste uttryckligen distribuera programmet till de *offentliga* noderna när du skapar programmet. Välj fliken **valfri** och ange **slave_public** för värdet för **godkända resurs roller** . Den här processen beskrivs [här](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) och i [DC/OS-](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) dokumentationen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur [du hanterar dina DC/OS-behållare](container-service-mesos-marathon-ui.md).

* Lär dig hur du [öppnar brand väggen](container-service-enable-public-access.md) som tillhandahålls av Azure för att tillåta offentlig åtkomst till dina DC/OS-behållare.

