---
title: (INAKTUELL) Hantera Azure DC/OS-kluster med Marathon REST API
description: Distribuera behållare till ett Azure Container Service DC/OS-kluster med Marathon REST API.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ec264eb31bd1243f683ab99bf19b871b08253ef7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532414"
---
# <a name="deprecated-dcos-container-management-through-the-marathon-rest-api"></a>(INAKTUELL) Hantering av DC/OS-behållare via Marathon REST API

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Utöver DC/OS finns det ett ramverk som hanterar schemaläggning och beräkning av arbetsbelastningar. Även om ramverk är tillgängliga för många populära arbetsbelastningar kan hjälper det här dokumentet dig att börja skapa och skala behållardistributioner med Marathon REST API. 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med de här exemplen behöver du ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

* [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
* [Ansluta till ett Azure Container Service-kluster](../container-service-connect.md)

## <a name="access-the-dcos-apis"></a>Komma åt DC/OS-API: er
När du är ansluten till Azure Container Service-kluster, du kan komma åt DC/OS och relaterade REST-API: er via http://localhost:local-port. Exemplen i det här dokumentet förutsätter att du använder tunneltrafik på port 80. Till exempel Marathon-slutpunkter kan nås på URI: er från och med `http://localhost/marathon/v2/`. 

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

## <a name="deploy-a-docker-formatted-container"></a>Distribuera en Docker-formaterad container
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

För att distribuera en Docker-formaterad behållare kan lagra JSON-filen på en tillgänglig plats. Kör följande kommando för att distribuera containern. Ange namnet på JSON-fil (`marathon.json` i det här exemplet).

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

## <a name="reach-the-container"></a>Nå ut till behållaren

Du kan kontrollera att Nginx körs i en behållare på en av de privata agenterna i klustret. För att hitta värden och den port där behållaren körs, fråga Marathon efter aktiviteterna som körs: 

```bash
curl localhost/marathon/v2/tasks
```

Hitta värdet för `host` i utdata (en IP-adress som liknar `10.32.0.x`), och värdet för `ports`.


Nu skapar du en terminal SSH-anslutning (inte en tunnel anslutning) för management FQDN i klustret. När du är ansluten, se följande begäran, och Ersätt värdena för `host` och `ports`:

```bash
curl http://host:ports
```

Nginx-serverutdata ser ut ungefär så här:

![Nginx från behållaren](./media/container-service-mesos-marathon-rest/nginx.png)




## <a name="scale-your-containers"></a>Skala containrarna
Du kan använda Marathon API för att skala ut eller skala in programdistributioner. I föregående exempel distribuerade du en instans av ett program. Nu kan vi skala ut det här till tre instanser av ett program. Det gör du genom att skapa en JSON-fil med nedanstående JSON-text och lagra den på en tillgänglig plats.

```json
{ "instances": 3 }
```

Kör följande kommando för att skala ut programmet från din tunneltrafik anslutning.

> [!NOTE]
> URI: N är http://localhost/marathon/v2/apps/ följt av ID: T för programmet som ska skalas. Om du använder Nginx-exemplet som anges här blir URI: N http://localhost/marathon/v2/apps/nginx.
> 
> 

```bash
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Fråga slutligen Marathon-slutpunkten efter program. Du ser nu att det finns tre Nginx-containrar.

```bash
curl localhost/marathon/v2/apps
```

## <a name="equivalent-powershell-commands"></a>Motsvarande PowerShell-kommandon
Du kan utföra samma åtgärder genom att använda PowerShell-kommandon på en Windows-dator.

För att samla in information om DC/OS-klustret, t.ex agentnamn och agentstatus, kör du följande kommando:

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Du kan distribuera Docker-formaterade containrar via Marathon genom att använda en JSON-fil som beskriver den avsedda distributionen. I följande exempel distribueras Nginx-containern, vilket binder port 80 på DC/OS-agenten till port 80 på containern.

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

För att distribuera en Docker-formaterad behållare kan lagra JSON-filen på en tillgänglig plats. Kör följande kommando för att distribuera containern. Ange sökvägen till JSON-fil (`marathon.json` i det här exemplet).

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Du kan även använda Marathon API för att skala ut eller skala in programdistributioner. I föregående exempel distribuerade du en instans av ett program. Nu kan vi skala ut det här till tre instanser av ett program. Det gör du genom att skapa en JSON-fil med nedanstående JSON-text och lagra den på en tillgänglig plats.

```json
{ "instances": 3 }
```

Kör följande kommando för att skala ut programmet:

> [!NOTE]
> URI: N är http://localhost/marathon/v2/apps/ följt av ID: T för programmet som ska skalas. Om du använder Nginx-exemplet som anges här blir URI: N blir http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Mesos HTTP-slutpunkter](https://mesos.apache.org/documentation/latest/endpoints/)
* [Läs mer om Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html)

