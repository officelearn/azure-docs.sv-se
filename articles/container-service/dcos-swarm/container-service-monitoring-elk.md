---
title: "Övervaka ett Azure DC/OS-kluster - stacken ELK | Microsoft Docs"
description: "Övervaka ett DC/OS-kluster i Azure Container Service-kluster med ELK (Elasticsearch Logstash och Kibana)."
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Behållare med DC/OS, Azure, övervakning, elk"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcfa277cdd0f3cebc0fbbb23e771fb23ffbe2ca6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>Övervaka ett Azure Container Service-kluster med ELK
I den här artikeln visar vi hur du distribuerar ELK (Elasticsearch, Logstash, Kibana)-stacken på ett DC/OS-kluster i Azure Container Service. 

## <a name="prerequisites"></a>Krav
[Distribuera](container-service-deployment.md) och [ansluta](../container-service-connect.md) ett DC/OS-kluster som är konfigurerat med Azure Container Service. Utforska instrumentpanelen för DC/OS och Marathon services [här](container-service-mesos-marathon-ui.md). Installera den [Marathon belastningsutjämnaren](container-service-load-balancing.md).


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK (Elasticsearch Logstash, Kibana)
ELK stacken är en kombination av Elasticsearch och Logstash Kibana som tillhandahåller en heltäckande stapel som kan användas för att övervaka och analysera loggar i klustret.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>Konfigurera ELK stacken på ett DC/OS-kluster
Åtkomst till ditt DC/OS-Gränssnittet via [http://localhost:80 /](http://localhost:80/) en gång i DC/OS-Gränssnittet går du till **Universe**. Söka efter och installera Elasticsearch och Logstash Kibana från DC/OS-Universe och i den specifika ordningen. Du kan lära dig mer om konfiguration om du går till den **Installation i Avancerat** länk.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

När ELK behållare och är igång, måste du aktivera Kibana är tillgängliga via Marathon-LB. Gå till **Services** > **kibana**, och klicka på **redigera** enligt nedan.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


Växla till **JSON-läget** och rulla ned till avsnittet etiketter.
Du måste lägga till en `"HAPROXY_GROUP": "external"` post här som visas nedan.
När du klickar på **distribuera ändringar**, omstarter av behållare.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


Om du vill kontrollera att Kibana registreras som en tjänst i HAPROXY instrumentpanelen måste du öppna port 9090 på agenten klustret som HAPROXY körs på port 9090.
Som standard det öppna portarna 80, 8080 och 443 i klustret för DC/OS-agenten.
Instruktioner för att öppna en port och ge offentlig utvärdera [här](container-service-enable-public-access.md).

Öppna Marathon-LB admin-gränssnittet på för att komma åt instrumentpanelen HAPROXY: `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`.
När du navigerar till URL: en bör du se HAPROXY instrumentpanelen som visas nedan och du bör se en för Kibana.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


Om du vill komma åt instrumentpanelen Kibana som har distribuerats på port 5601, måste du öppna port 5601. Följ anvisningarna [här](container-service-enable-public-access.md). Öppna instrumentpanelen på Kibana: `http://localhost:5601`.

## <a name="next-steps"></a>Nästa steg

* För system- och logg vidarebefordran och inställningar, se [hantering i DC/OS med ELK](https://docs.mesosphere.com/1.8/administration/logging/elk/).

* Om du vill filtrera loggarna finns [filtrering loggar med ELK](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/). 

 

