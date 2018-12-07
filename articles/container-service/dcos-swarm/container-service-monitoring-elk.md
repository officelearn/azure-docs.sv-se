---
title: (INAKTUELL) Övervaka ett Azure DC/OS-kluster - ELK-stacken
description: Övervaka ett DC/OS-kluster i Azure Container Service-kluster med ELK (Elasticsearch, Logstash och Kibana).
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998145"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(INAKTUELL) Övervaka ett Azure Container Service-kluster med ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi hur du distribuerar ELK (Elasticsearch, Logstash, Kibana)-stacken på ett DC/OS-kluster i Azure Container Service. 

## <a name="prerequisites"></a>Förutsättningar
[Distribuera](container-service-deployment.md) och [ansluta](../container-service-connect.md) ett DC/OS-kluster som har konfigurerats med Azure Container Service. Utforska instrumentpanelen för DC/OS och Marathon tjänster [här](container-service-mesos-marathon-ui.md). Även installera den [Marathon Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK-stacken är en kombination av Elasticsearch, Logstash och Kibana som tillhandahåller en stack för slutpunkt till slutpunkt som kan användas för att övervaka och analysera loggar i klustret.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurera ELK-stacken på en DC/OS-klustret
Komma åt DC/OS-Gränssnittet via [ http://localhost:80/ ](http://localhost:80/) en gång i DC/OS-Användargränssnittet navigerar du till **Universe**. Sök efter och installera Elasticsearch, Logstash och Kibana från DC/OS Universe och i den specifika ordningen. Du kan lära dig mer om konfiguration om du går till den **avancerade installationen** länk.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

När ELK-behållare och är igång, måste du aktivera Kibana är tillgängliga via Marathon-LB. Gå till **Services** > **kibana**, och klicka på **redigera** enligt nedan.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Växla till **JSON-läget** och rulla ned till avsnittet etiketter.
Du måste lägga till en `"HAPROXY_GROUP": "external"` post här som visas nedan.
När du klickar på **distribuera ändringar**, din behållare startas om.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Om du vill kontrollera att Kibana är registrerad som en tjänst i HAPROXY instrumentpanelen som du behöver öppna port 9090 på agenten klustret som HAPROXY körs på port 9090.
Som standard det öppna portarna 80, 8080 och 443 i klustret för DC/OS-agenten.
Anvisningar för att öppna en port och tillhandahålla offentliga utvärdera tillhandahålls [här](container-service-enable-public-access.md).

Öppna admin-gränssnittet för Marathon-LB på för att komma åt instrumentpanelen HAPROXY: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
När du navigerar till URL: en bör du se HAPROXY instrumentpanelen som visas nedan och du bör se en för Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


För att komma åt Kibana-instrumentpanelen, som har distribuerats på port 5601, måste du öppna port 5601. Följ instruktionerna [här](container-service-enable-public-access.md). Öppna Kibana-instrumentpanelen på: `http://localhost:5601`.

## <a name="next-steps"></a>Nästa steg

* För systemets och programmets logg vidarebefordran och installationen finns i [Logghantering i DC/OS med ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Om du vill filtrera loggar finns i [filtrering loggar med ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

