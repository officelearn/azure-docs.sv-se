---
title: "Azure Container Service-hantering av behållare via REST API | Microsoft Docs"
description: "Distribuera behållare till ett Azure Container Service Mesos-kluster med Marathon REST API."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, behållare, Micro-tjänster, Mesos, Azure"
ms.assetid: c7175446-4507-4a33-a7a2-63583e5996e3
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: timlt
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e8f1ad596d2b64380876a501ebcf127afdda9ccf


---
# <a name="container-management-through-the-rest-api"></a>Behållarhantering via REST API
DC/OS erbjuder en miljö för att distribuera och skala klustrade arbetsbelastningar samtidigt som den underliggande maskinvaran abstraheras. Utöver DC/OS finns det ett ramverk som hanterar schemaläggning och beräkning av arbetsbelastningar.

Även om ramverk är tillgängliga för många populära arbetsbelastningar beskriver det här dokumentet hur du kan skapa och skala distribution i behållare med Marathon. Innan du börjar med de här exemplen behöver du ett DC/OS-kluster som har konfigurerats i Azure Container Service. Du måste också kunna fjärransluta till det här klustret. Mer information finns i följande artiklar:

* [Distribuera ett Azure Container Service-kluster](container-service-deployment.md)
* [Ansluta till ett Azure Container Service-kluster](container-service-connect.md)

När du har anslutit till Azure Container Service-klustret kan du komma åt DC/OS och relaterade REST API:er via http://localhost:local-port. Exemplen i det här dokumentet förutsätter att du använder tunneltrafik på port 80. Marathon-slutpunkten kan exempelvis nås på `http://localhost/marathon/v2/`. Mer information om de olika API:erna finns i Mesosphere-dokumentationen för [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) och [Chronos API](https://mesos.github.io/chronos/docs/api.html), samt Apache-dokumentationen för [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Samla in information från DC/OS och Marathon
Samla in information om DC/OS-klustret, till exempel namn och aktuell status för DC/OS-agenterna, innan du distribuerar behållare i DC/OS-klustret. Det gör du genom att fråga `master/slaves`-slutpunkten för DC/OS REST API. Om allt går bra visas en lista över DC/OS-agenter och flera egenskaper för varje agent.

```bash
curl http://localhost/mesos/master/slaves
```

Nu kan du använda Marathon-slutpunkten `/apps` för att kontrollera om det finns aktuella programdistributioner i DC/OS-klustret. Om det här är ett nytt kluster visas en tom matris för appar.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Distribuera en Docker-formaterad behållare
Du kan distribuera Docker-formaterade behållare via Marathon genom att använda en JSON-fil som beskriver den avsedda distributionen. I följande exempel distribueras Nginx-behållaren, vilket binder port 80 på DC/OS-agenten till port 80 på behållaren. Observera även att ”slave_public” har angetts för egenskapen ”acceptedResourceRoles”. Det innebär att behållaren distribueras till en agent i den offentliga agentskalningsuppsättningen.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Distribuera en Docker-formaterad behållare genom att skapa en egen JSON-fil, eller använd exemplet som anges i [Azure Container Service-demon](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Lagra den på en tillgänglig plats. Kör följande kommando för att distribuera behållaren. Ange namnet på JSON-filen.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Resultatet blir något som liknar:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Om du nu frågar Marathon efter program visas det här nya programmet i resultaten.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Skala behållarna
Du kan även använda Marathon API för att skala ut eller skala in programdistributioner. I föregående exempel distribuerade du en instans av ett program. Nu kan vi skala ut det här till tre instanser av ett program. Det gör du genom att skapa en JSON-fil med nedanstående JSON-text och lagra den på en tillgänglig plats.

```json
{ "instances": 3 }
```

Kör följande kommando för att skala ut programmet.

> [!NOTE]
> URI:n blir http://localhost/marathon/v2/apps/ och sedan ID:t för programmet som ska skalas. Om du använder Nginx-exemplet som anges här blir URI:n http://localhost/marathon/v2/apps/nginx.
> 
> 

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Fråga slutligen Marathon-slutpunkten efter program. Du ser nu att det finns tre Nginx-behållare.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Använd PowerShell för den här övningen: Marathon REST API-interaktion med PowerShell
Du kan utföra samma åtgärder genom att använda PowerShell-kommandon på en Windows-dator.

Kör följande kommando för att samla in information om DC/OS-klustret, t.ex. agentnamn och agentstatus.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Du kan distribuera Docker-formaterade behållare via Marathon genom att använda en JSON-fil som beskriver den avsedda distributionen. I följande exempel distribueras Nginx-behållaren, vilket binder port 80 på DC/OS-agenten till port 80 på behållaren.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Skapa en egen JSON-fil eller använd exemplet som anges i [Azure Container Service-demon](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Lagra den på en tillgänglig plats. Kör följande kommando för att distribuera behållaren. Ange namnet på JSON-filen.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Du kan även använda Marathon API för att skala ut eller skala in programdistributioner. I föregående exempel distribuerade du en instans av ett program. Nu kan vi skala ut det här till tre instanser av ett program. Det gör du genom att skapa en JSON-fil med nedanstående JSON-text och lagra den på en tillgänglig plats.

```json
{ "instances": 3 }
```

Kör följande kommando för att skala ut programmet.

> [!NOTE]
> URI:n blir http://localhost/marathon/v2/apps/ och sedan ID:t för programmet som ska skalas. Om du använder Nginx-exemplet som anges här blir URI:n http://localhost/marathon/v2/apps/nginx.
> 
> 

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Nästa steg
* [Mer information om Mesos HTTP-slutpunkter](http://mesos.apache.org/documentation/latest/endpoints/).
* [Mer information om Marathon REST API](https://mesosphere.github.io/marathon/docs/rest-api.html).




<!--HONumber=Dec16_HO1-->


