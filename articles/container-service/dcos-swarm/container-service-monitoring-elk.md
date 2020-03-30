---
title: (FÖRÅLDRAD) Övervaka ett Azure DC/OS-kluster - ELK-stack
description: Övervaka ett DC/OS-kluster i Azure Container Service-klustret med ELK (Elasticsearch, Logstash och Kibana).
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3d34ebe22344be8acc6ec3cc974071639293e2b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277763"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(FÖRÅLDRAD) Övervaka ett Azure Container Service-kluster med ELK

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi hur du distribuerar ELK-stacken (Elasticsearch, Logstash, Kibana) på ett DC/OS-kluster i Azure Container Service. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [ansluta](../container-service-connect.md) ett DC/OS-kluster som konfigurerats av Azure Container Service. Utforska DC/ OS-instrumentpanelen och Marathon-tjänsterna [här](container-service-mesos-marathon-ui.md). Installera även [Marathon Load Balancer](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch, Logstash, Kibana)
ELK stack är en kombination av Elasticsearch, Logstash och Kibana som ger en end to end stack som kan användas för att övervaka och analysera loggar i klustret.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurera ELK-stacken på ett DC/OS-kluster
Få tillgång till dc/os-användargränssnittet via [http://localhost:80/](http://localhost:80/) En gång i DC/OS-användargränssnittet navigerar till **Universum**. Sök och installera Elasticsearch, Logstash och Kibana från DC/OS Universe och i den specifika ordningen. Du kan läsa mer om konfiguration om du går till länken **Avancerad installation.**

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

När ÄLGEN har behållare och är igång måste du göra det möjligt för Kibana att nås via Marathon-LB. Navigera till **Services** > **kibana**och klicka på **Redigera** enligt nedan.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Växla till **JSON-läge** och rulla nedåt till etikettavsnittet.
Du måste lägga `"HAPROXY_GROUP": "external"` till en post här som visas nedan.
När du klickar på **Distribuera ändringar**startar behållaren om.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Om du vill kontrollera att Kibana är registrerad som en tjänst på HAPROXY-instrumentpanelen måste du öppna port 9090 i agentklustret när HAPROXY körs på port 9090.
Som standard öppnar vi portarna 80, 8080 och 443 i DC/OS-agentklustret.
Instruktioner för att öppna en hamn och ge offentlig bedömning finns [här](container-service-enable-public-access.md).

Öppna admingränssnittet för Marathon-LB för att komma `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`åt HAPROXY-instrumentpanelen på: .
När du navigerar till webbadressen bör du se HAPROXY-instrumentpanelen som visas nedan och du bör se en tjänstpost för Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


För att komma åt Kibana-instrumentpanelen, som distribueras på port 5601, måste du öppna port 5601. Följ instruktionerna [här](container-service-enable-public-access.md). Öppna sedan Kibana-instrumentpanelen på: `http://localhost:5601`.

## <a name="next-steps"></a>Nästa steg

* Vidarebefordrande och inställning av system- och programloggen finns [i Logghantering i DC/OS med ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Så här filtrerar du loggar finns i [Filtrera loggar med ÄLG](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

