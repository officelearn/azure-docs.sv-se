---
title: "Läsa in saldo behållare i Azure DC/OS-klustret"
description: "Belastningen över flera behållare i ett Azure Container Service DC/OS-kluster."
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6f5467d0fbcc577a548f1100ed6e4d380fe38759
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Läsa in saldo behållare i ett Azure Container Service DC/OS-kluster

I den här artikeln förklarar vi hur du skapar en intern belastningsutjämnare i en DC/OS-hanterad Azure Container Service med Marathon-LB. Den här konfigurationen kan du skala ditt program vågrätt. Du kan också dra nytta av offentliga och privata agenten kluster genom att placera din belastningsutjämnare på det offentliga och behållarna programmet på det privata klustret. I den här kursen har du:

> [!div class="checklist"]
> * Konfigurera en belastningsutjämnare för Marathon
> * Distribuera ett program med belastningsutjämnaren
> * Konfigurera och Azure belastningsutjämnare

Du behöver en ACS DC/OS-klustret för att slutföra stegen i den här självstudiekursen. Om det behövs, [detta skriptexempel](./../kubernetes/scripts/container-service-cli-deploy-dcos.md) kan skapa en åt dig.

För den här självstudien krävs Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>Översikt för belastningsutjämning

Det finns två lager för belastningsutjämning i ett Azure Container Service DC/OS-kluster: 

**Azure belastningsutjämnare** ger offentliga startpunkterna (de som slutanvändarna kan komma åt). Ett Azure LB tillhandahålls automatiskt med Azure Container Service och är som standard konfigurerad för att exponera port 80 och 443 8080.

**Marathon-belastningsutjämnare (marathon-lb)** vägar inkommande förfrågningar till behållarinstanser dessa tjänstbegäranden. Som vi skalanpassar behållarna som tillhandahåller våra webbtjänsten marathon-lb anpassas efter dynamiskt. Denna belastningsutjämning har inte angetts som standard i Container Service, men det är lätt att installera.

## <a name="configure-marathon-load-balancer"></a>Konfigurera Marathon belastningsutjämnare

Marathon Load Balancer konfigurerar om sig själv dynamiskt baserat på de behållare som du har distribuerat. Det är också motståndskraftiga mot förlust av en behållare eller agent - om detta inträffar, Apache Mesos startar om behållaren någon annanstans och marathon-lb anpassas efter dina behov.

Kör följande kommando för att installera marathon belastningsutjämnaren på offentlig agent kluster.

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>Distribuera program för Utjämning av nätverksbelastning

Nu när vi har marathon-lb-paketet kan vi distribuera en programbehållare som vi vill belastningsutjämna. 

Först hämta det fullständiga Domännamnet för offentligt exponerade agenterna.

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

Skapa sedan en fil med namnet *hello web.json* och kopiera följande innehåll. Den `HAPROXY_0_VHOST` etikett behöver uppdateras med FQDN för DC/OS-agenterna. 

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

Använd DC/OS CLI för att köra programmet. Som standard Marathon distribuerar den programobjekt till privata klustret. Det innebär att ovanstående distribution är endast tillgänglig via din belastningsutjämnare, som vanligtvis är beteenden som önskas.

```azurecli-interactive
dcos marathon app add hello-web.json
```

När programmet har distribuerats, bläddra till FQDN för agenten klustret att visa belastningsutjämnade program.

![Bild av belastningsutjämnade program](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>Konfigurera Azure belastningsutjämnare

Som standard exponerar Azure Load Balancer portarna 80, 8080 och 443. Om du använder en av dessa tre portar (som vi gör i ovanstående exempel) behöver du inte göra något. Du bör kunna träffa din agentens belastningsutjämnare FQDN och varje gång du uppdaterar du tryck något av de tre webbservrarna resursallokering överskrids. 

Om du använder en annan port måste du lägga till en resursallokeringsregel och en avsökning på belastningsutjämnaren för den port som du använde. Du kan göra detta från [Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md), med kommandona `azure network lb rule create` och `azure network lb probe create`.

## <a name="next-steps"></a>Nästa steg

I kursen får du lärt dig om belastningsutjämning i ACS med både Marathon och Azure belastningsutjämnare som bland annat följande:

> [!div class="checklist"]
> * Konfigurera en belastningsutjämnare för Marathon
> * Distribuera ett program med belastningsutjämnaren
> * Konfigurera och Azure belastningsutjämnare

Gå vidare till nästa kurs att lära dig om att integrera Azure storage med DC/OS i Azure.

> [!div class="nextstepaction"]
> [Montera Azure-filresursen i DC/OS-klustret](container-service-dcos-fileshare.md)