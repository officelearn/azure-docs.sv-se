<properties
   pageTitle="Belastningsutjämna ett Azure Container Service-kluster | Microsoft Azure"
   description="Belastningsutjämna ett Azure Container Service-kluster."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Belastningsutjämna ett Azure Container Service-kluster

I den här artikeln konfigurerar vi en frontwebb som kan skalas upp för att leverera tjänster bakom Azure LB.


## Krav

[Distribuera en Azure Container Service-instans](container-service-deployment.md) med orchestrator-typen DC/OS, [kontrollera att klienten kan ansluta till klustret](container-service-connect.md) och [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].


## Belastningsutjämning

Det finns två belastningsutjämningslager i ett Container Service-kluster: Azure LB för de offentliga startpunkterna (som slutanvändarna kommer till) och den underliggande marathon-lb som dirigerar inkommande förfrågningar till behållarinstanser som servar förfrågningar. Marathon-lb anpassas dynamiskt efter hand som vi skalanpassar behållarna som tillhandahåller tjänsten.

## Marathon LB 

Marathon LB-lösningen konfigureras dynamiskt om utifrån de behållare som du har distribuerat. Den har även återhämtningsförmåga när det gäller förlust av en behållare eller agent. Om det skulle inträffa startar Mesos helt enkelt om behållaren någon annanstans och konfigurerar om Marathon LB. 

Installera Marathon LB genom att köra följande kommando från klientdatorn:

```bash
dcos package install marathon-lb 
``` 

När vi nu har marathon-lb-paketet kan vi distribuera en enkel webbserver med följande konfiguration:


```json
{ 
  "id": "web", 
  "container": { 
    "type": "DOCKER", 
    "docker": { 
      "image": "tutum/hello-world", 
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

De viktigaste delarna är: 
  * Ange värdet för HAProxy_0_VHOST till FQDN på belastningsutjämnaren för agenterna. FQDN är i formatet `<acsName>agents.<region>.cloudapp.azure.com`. Om du t.ex. skapar ett Container Service-kluster med namnet `myacs` i regionen `West US`, skulle FQDN vara: `myacsagents.westus.cloudapp.azure.com`. Du kan även hitta den här informationen genom att leta efter belastningsutjämnaren med ”agent” i namnet när du letar igenom resurserna i resursgruppen du skapade för behållartjänsten i [Azure Portal](https://portal.azure.com).
  * Ange servicePort till en port > = 10000. Då identifieras tjänsten som körs i den här behållaren, och marathon-lb använder en här informationen för att identifiera tjänster den ska utjämna mot.
  * Ange ”external” för etiketten HAPROXY_GROUP.
  * Ange 0 för hostPort. När du gör det innebär det att Marathon godtyckligt allokerar en tillgänglig port.

Kopiera den här JSON-koden i en fil som heter `hello-web.json` och använd den för att distribuera en behållare: 

```bash
dcos marathon app add hello-web.json 
``` 

## Azure LB 

Som standard exponerar Azure LB portarna 80, 8080 och 443. Om du använder en av de här tre portarna (som i ovanstående exempel) behöver du inte göra något. Du borde kunna träffa agent-LB:ns FQDN och varje gång du uppdaterar, träffar du en av de tre webbservrarna med resursallokering. Om du däremot använder en annan port måste du lägga till en resursallokeringsregel och en avsökning på Azure LB för den port du använde. Det kan göras från [Azure XPLAT CLI](../xplat-cli-azure-resource-manager.md) med kommandona `azure lb rule create` och `azure lb probe create`.


## Fler scenarier

Du kan ha ett scenario där du använder olika domäner för att exponera olika tjänster. Exempel: 

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321 

Det kan du uppnå genom att använda [virtuella värdar](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/), som är ett sätt att associera domäner till specifika marathon-lb-sökvägar.

Som ett alternativ kan du exponera olika portar och mappa om dem till rätt tjänst bakom Marathon lb. Exempel:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432 
 

## Nästa steg

Mer information om Marathon LB finns i [det här blogginlägget](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/).



<!--HONumber=Jun16_HO2-->


