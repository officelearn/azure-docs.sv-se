---
title: (INAKTUELL) Belastningsutjämningscontainrar i Azure DC/OS-kluster
description: Belastningsutjämna mellan flera containrar i ett Azure Container Service DC/OS-kluster.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 1e4c978a8767154fb6a1f9a822cb0dd8d1b8796e
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331035"
---
# <a name="deprecated-load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>(INAKTUELL) Belastningsutjämna containrar i ett Azure Container Service DC/OS-kluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här artikeln visar vi hur du skapar en intern lastbalanserare i en DC/OS-hanterad Azure Container Service med Marathon-LB. Med den här konfigurationen kan du skala program vågrätt. Du kan också använda offentliga och privata agentkluster genom att placera en lastbalanserare på det offentliga klustret och dina programcontainrar på det privata klustret. I den här kursen för du göra följande:

> [!div class="checklist"]
> * Konfigurera en Marathon-lastbalanserare
> * Distribuera ett program med lastbalanseraren
> * Konfigurera Azure Load Balancer

Du behöver ett ACS DC/OS-klustret när du ska utföra stegen i den här självstudien. Om det behövs kan du skapa ett sådant med [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Översikt över belastningsutjämning

Det finns två lager av belastningsutjämning i ett Azure Container Service DC/OS-kluster: 

**Azure Load Balancer** tillhandahåller offentliga startpunkter (de som slutanvändarna får åtkomst till). En Azure-lastbalanserare tillhandahålls automatiskt av Azure Container Service och konfigureras som standard att exponera port 80, 443 och 8080.

**Marathon-lastbalanseraren (marathon-lb)** dirigerar inkommande begäranden till containerinstanser som hanterar dessa begäranden. När vi skalar containrarna som tillhandahåller vår webbtjänst anpassas marathon-lb dynamiskt. Den här lastbalanseraren tillhandahålls inte som standard i Container Service, men är enkel att installera.

## <a name="configure-marathon-load-balancer"></a>Konfigurera Marathon-lastbalanserare

Marathon Load Balancer konfigurerar om sig själv dynamiskt baserat på de containrar som du har distribuerat. Den kan även återhämta sig automatiskt vid en eventuell förlust av en container eller agent. Om detta inträffar startar Apache Mesos om containern någon annanstans och marathon-lb anpassas automatiskt.

Kör följande kommando för att installera Marathon-lastbalanseraren på den offentliga agentens kluster.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Distribuera belastningsutjämnat program

Nu när vi har marathon-lb-paketet kan vi distribuera en programcontainer som vi vill belastningsutjämna. 

Hämta först FQDN för de offentligt exponerade agenterna.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Skapa sedan en fil med namnet *hello-web.json* and kopiera in följande innehåll. Etiketten `HAPROXY_0_VHOST` måste uppdateras med FQDN för DC/OS-agenterna. 

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}
```

Använd DC/OS CLI för att köra programmet. Som standard distribuerar Marathon programmet till det privata klustret. Det innebär att ovanstående distribution bara är tillgänglig via lastbalanseraren, som normalt är det önskade beteendet.

```azurecli-interactive
dcos marathon app add hello-web.json
```

När programmet har distribuerats bläddrar du till FQDN för agent klustret för att visa det belastningsutjämnade programmet.

![Bild av belastningsutjämnat program](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Konfigurera Azure Load Balancer

Som standard exponerar Azure Load Balancer portarna 80, 8080 och 443. Om du använder en av dessa tre portar (som vi gör i ovanstående exempel) behöver du inte göra något. Du bör kunna nå lastbalanserarens FQDN för din agent, och varje gång du uppdaterar når du en av de tre webbservrarna baserat på en resursallokeringsmodell (round-robin). 

Om du använder en annan port måste du lägga till en resursallokeringsregel och en avsökning i lastbalanseraren för den port som du använde. Du kan göra detta från [Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), med kommandona `azure network lb rule create` och `azure network lb probe create`.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du fått lära dig om lastbalansering i ACS med både Marathon- och Azure-lastbalanserare som bland annat följande:

> [!div class="checklist"]
> * Konfigurera en Marathon-lastbalanserare
> * Distribuera ett program med lastbalanseraren
> * Konfigurera Azure Load Balancer

Gå vidare till nästa kurs för att lära dig om hur du integrerar Azure Storage med DC/OS i Azure.

> [!div class="nextstepaction"]
> [Montera Azure-filresurs i DC/OS-kluster](container-service-dcos-fileshare.md)