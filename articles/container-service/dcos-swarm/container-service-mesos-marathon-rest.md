---
title: Hantera Azure DC/OS-kluster med Marathon REST API | Microsoft Docs
description: "Distribuera behållare till ett Azure Container Service DC/OS-kluster med Marathon REST API."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, behållare, Micro-tjänster, Mesos, Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 65f8e0170fa7b89162e811a1d5dd58775fd20d7b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="dcos-container-management-through-the-marathon-rest-api"></a>DC/OS-hantering av behållare via Marathon REST API
DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Utöver DC/OS finns det ett ramverk som hanterar schemaläggning och beräkning av arbetsbelastningar. Även om ramverk är tillgängliga för många populära arbetsbelastningar, hjälper det här dokumentet dig att börja skapa och skala distribution i behållare med Marathon REST API. 

## <a name="prerequisites"></a>Krav

Innan du börjar med de här exemplen behöver du ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

* [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
* [Ansluta till ett Azure Container Service-kluster](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>DC/OS-API: er
När du har anslutit till Azure Container Service-klustret kan du komma åt DC/OS och relaterade REST API:er via http://localhost:local-port. Exemplen i det här dokumentet förutsätter att du använder tunneltrafik på port 80. Till exempel Marathon-slutpunkter kan nås på URI: er från och med `http://localhost/marathon/v2/`. 

Mer information om de olika API:erna finns i Mesosphere-dokumentationen för [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) och [Chronos API](https://mesos.github.io/chronos/docs/api.html), samt Apache-dokumentationen för [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Samla in information från DC/OS och Marathon
Samla in information om DC/OS-klustret, till exempel namn och status för DC/OS-agenter innan du distribuerar behållare i DC/OS-klustret. Det gör du genom att fråga `master/slaves`-slutpunkten för DC/OS REST API. Om allt går bra returnerar frågan en lista över DC/OS-agenter och flera egenskaper för varje agent.

```bash
curl http://localhost/mesos/master/slaves
```

Nu kan du använda Marathon-slutpunkten `/apps` för att kontrollera om det finns aktuella programdistributioner i DC/OS-klustret. Om det här är ett nytt kluster visas en tom matris för appar.

```bash
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Distribuera en Docker-formaterad behållare
Du kan distribuera Docker-formaterade behållare via Marathon REST API med hjälp av en JSON-fil som beskriver den avsedda distributionen. I följande exempel distribueras Nginx-behållaren till en privat agent i klustret. 

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Distribuera en Docker-formaterad behållare genom att lagra JSON-filen på en tillgänglig plats. Kör följande kommando för att distribuera behållaren. Ange namnet på JSON-fil (`marathon.json` i det här exemplet).

```bash
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

De utdata som genereras liknar följande:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Om du nu frågar Marathon efter program visas det här nya programmet i resultaten.

```bash
curl localhost/marathon/v2/apps
```

## <a name="reach-the-container"></a>Nå behållaren

Du kan kontrollera att Nginx körs i en behållare på en av de privata agenterna i klustret. Fråga Marathon efter aktiviteter som körs för att hitta värden och den port där behållaren körs: 

```bash
curl localhost/marathon/v2/tasks
```

Hitta värdet för `host` i utdata (en IP-adress som liknar `10.32.0.x`), och värdet för `ports`.


Nu göra en terminal SSH-anslutning (inte en tunnel anslutning) för management FQDN i klustret. När du är ansluten, se följande begäran, ersätter de korrekta värdena för `host` och `ports`:

```bash
curl http://host:ports
```

Nginx server-utdata liknar följande:

![Nginx från behållaren](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Skala behållarna
Du kan använda Marathon API för att skala ut eller skala in programdistributioner. I föregående exempel distribuerade du en instans av ett program. Nu kan vi skala ut det här till tre instanser av ett program. Det gör du genom att skapa en JSON-fil med nedanstående JSON-text och lagra den på en tillgänglig plats.

```json
{ "instances": 3 }
```

Kör följande kommando för att skala ut programmet från anslutningens tunnel.

> [!NOTE]
> URI:n är http://localhost/marathon/v2/apps/ och följs av ID:t för programmet som ska skalas. Om du använder Nginx-exemplet som anges här blir URI:n http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Fråga slutligen Marathon-slutpunkten efter program. Du ser nu att det finns tre Nginx-behållare.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Motsvarande PowerShell-kommandon
Du kan utföra samma åtgärder genom att använda PowerShell-kommandon på en Windows-dator.

Kör följande kommando för att samla in information om DC/OS-klustret, t.ex agentnamn och agentstatus:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Du kan distribuera Docker-formaterade behållare via Marathon genom att använda en JSON-fil som beskriver den avsedda distributionen. I följande exempel distribueras Nginx-behållaren, vilket binder port 80 på DC/OS-agenten till port 80 på behållaren.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 32.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Distribuera en Docker-formaterad behållare genom att lagra JSON-filen på en tillgänglig plats. Kör följande kommando för att distribuera behållaren. Ange sökvägen till JSON-fil (`marathon.json` i det här exemplet).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Du kan även använda Marathon API för att skala ut eller skala in programdistributioner. I föregående exempel distribuerade du en instans av ett program. Nu kan vi skala ut det här till tre instanser av ett program. Det gör du genom att skapa en JSON-fil med nedanstående JSON-text och lagra den på en tillgänglig plats.

```json
{ "instances": 3 }
```

Kör följande kommando för att skala ut programmet:

> [!NOTE]
> URI:n är http://localhost/marathon/v2/apps/ och följs av ID:t för programmet som ska skalas. Om du använder Nginx-exemplet som anges här blir URI:n http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Mesos HTTP-slutpunkter](http://mesos.apache.org/documentation/latest/endpoints/)
* [Läs mer om Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

