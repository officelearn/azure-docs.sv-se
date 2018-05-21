---
title: Belastningsutjämningsbehållare i Azure DC/OS-klustret
description: Belastningsutjämna mellan flera behållare i ett Azure Container Service DC/OS-kluster.
services: container-service
author: rgardler
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 62967636a4d80f72f731a666947d5d4d5e47f7e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Belastningsutjämna behållare i ett Azure Container Service DC/OS-kluster

I den här artikeln visar vi hur du skapar en intern belastningsutjämnare i en DC/OS-hanterad Azure Container Service med Marathon-LB. Med den här konfigurationen kan du skala program vågrätt. Du kan också använda offentliga och privata agentkluster genom att placera en belastningsutjämnare på det offentliga klustret och dina programbehållare på det privata klustret. I den här kursen ska du:

> [!div class="checklist"]
> * Konfigurera en Marathon-belastningsutjämnare
> * Distribuera ett program med belastningsutjämnaren
> * Konfigurera en Azure belastningsutjämnare

Du behöver ett ACS DC/OS-klustret när du ska utföra stegen i den här självstudien. Om det behövs kan du skapa ett sådant med [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Översikt över belastningsutjämning

Det finns två lager av belastningsutjämning i ett Azure Container Service DC/OS-kluster: 

**Azure Load Balancer** tillhandahåller offentliga startpunkter (de som slutanvändarna får åtkomst till). En Azure-belastningsutjämnare tillhandahålls automatiskt av Azure Container Service och konfigureras som standard att exponera port 80, 443 och 8080.

**Marathon-belastningsutjämnaren (marathon-lb)** dirigerar inkommande begäranden till behållarinstanser som hanterar dessa begäranden. När vi skalar behållarna som tillhandahåller vår webbtjänst anpassas marathon-lb dynamiskt. Den här belastningsutjämnaren tillhandahålls inte som standard i Container Service, men är enkel att installera.

## <a name="configure-marathon-load-balancer"></a>Konfigurera Marathon-belastningsutjämnare

Marathon Load Balancer konfigurerar om sig själv dynamiskt baserat på de behållare som du har distribuerat. Den kan även återhämta sig automatiskt vid en eventuell förlust av en behållare eller agent. Om detta inträffar startar Apache Mesos om behållaren någon annanstans och marathon-lb anpassas automatiskt.

Kör följande kommando för att installera Marathon-belastningsutjämnaren på den offentliga agentens kluster.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Distribuera belastningsutjämnat program

Nu när vi har marathon-lb-paketet kan vi distribuera en programbehållare som vi vill belastningsutjämna. 

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

Använd DC/OS CLI för att köra programmet. Som standard distribuerar Marathon programmet till det privata klustret. Det innebär att ovanstående distribution bara är tillgänglig via belastningsutjämnaren, som normalt är det önskade beteendet.

```azurecli-interactive
dcos marathon app add hello-web.json
```

När programmet har distribuerats bläddrar du till FQDN för agent klustret för att visa det belastningsutjämnade programmet.

![Bild av belastningsutjämnat program](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Konfigurera Azure Load Balancer

Som standard exponerar Azure Load Balancer portarna 80, 8080 och 443. Om du använder en av dessa tre portar (som vi gör i ovanstående exempel) behöver du inte göra något. Du bör kunna nå belastningsutjämnarens FQDN för din agent, och varje gång du uppdaterar når du en av de tre webbservrarna baserat på en resursallokeringsmodell (round-robin). 

Om du använder en annan port måste du lägga till en resursallokeringsregel och en avsökning i belastningsutjämnaren för den port som du använde. Du kan göra detta från [Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), med kommandona `azure network lb rule create` och `azure network lb probe create`.

## <a name="next-steps"></a>Nästa steg

I den här kursen har du fått lära dig om belastningsutjämning i ACS med både Marathon- och Azure-belastningsutjämnare som bland annat följande:

> [!div class="checklist"]
> * Konfigurera en Marathon-belastningsutjämnare
> * Distribuera ett program med belastningsutjämnaren
> * Konfigurera en Azure belastningsutjämnare

Gå vidare till nästa kurs för att lära dig om hur du integrerar Azure Storage med DC/OS i Azure.

> [!div class="nextstepaction"]
> [Montera Azure-filresurs i DC/OS-kluster](container-service-dcos-fileshare.md)