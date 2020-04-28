---
title: FÖRÅLDRAD Övervaka en Azure DC/OS-ELK stack
description: Övervaka ett DC/OS-kluster i Azure Container Service kluster med ELK (ElasticSearch, Logstash och Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 586b8d25a9f391487640e9b1f8adb3be0e4be6db
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166180"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>FÖRÅLDRAD Övervaka ett Azure Container Service kluster med ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi hur du distribuerar ELK-stacken (ElasticSearch, Logstash, Kibana) på ett DC/OS-kluster i Azure Container Service. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [Anslut](../container-service-connect.md) ett DC/OS-kluster som kon figurer ATS av Azure Container Service. Utforska DC/OS-instrumentpanelen och Marathon-tjänsterna [här](container-service-mesos-marathon-ui.md). Installera även [Marathon-Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (ElasticSearch, Logstash, Kibana)
ELK-stack är en kombination av ElasticSearch, Logstash och Kibana som tillhandahåller en slut punkt till slut punkt som kan användas för att övervaka och analysera loggar i klustret.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurera ELK-stacken på ett DC/OS-kluster
Kom åt ditt DC/OS- `http://localhost:80/` gränssnitt via en gång i DC/OS-gränssnittet navigera till **universum**. Sök efter och installera ElasticSearch, Logstash och Kibana från DC/OS-universum och i den aktuella ordningen. Du kan läsa mer om konfigurationen om du går till den **avancerade installations** länken.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

När ELK-behållarna och är igång måste du aktivera Kibana för åtkomst via Marathon-LB. Gå till **tjänster** > **Kibana**och klicka på **Redigera** som visas nedan.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Växla till **JSON-läge** och rulla ned till avsnittet etiketter.
Du måste lägga till en `"HAPROXY_GROUP": "external"` post så som visas nedan.
När du klickar på **distribuera ändringar**startar behållaren om.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Om du vill kontrol lera att Kibana har registrerats som en tjänst på HAPROXY-instrumentpanelen måste du öppna port 9090 på agent klustret när HAPROXY körs på port 9090.
Som standard öppnar vi portarna 80, 8080 och 443 i DC/OS agent-klustret.
Instruktioner för att öppna en port och tillhandahålla allmän utvärdering finns [här](container-service-enable-public-access.md).

Öppna HAPROXY-instrumentpanelen genom att öppna Marathon-LB admin-gränssnittet på `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`:.
När du navigerar till webb adressen bör du se HAPROXY-instrumentpanelen som visas nedan och du bör se en tjänst post för Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


För att komma åt Kibana-instrumentpanelen, som distribueras på port 5601, måste du öppna port 5601. Följ instruktionerna [här](container-service-enable-public-access.md). Öppna sedan Kibana-instrumentpanelen på `http://localhost:5601`:.

## <a name="next-steps"></a>Nästa steg

* För vidarebefordring och konfiguration av system-och program loggen, se [logg hantering i DC/OS med Elk](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* För att filtrera loggar, se [filtrera loggar med Elk](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

