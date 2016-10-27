<properties
   pageTitle="Belastningsutjämna behållare i ett Azure Container Service-kluster | Microsoft Azure"
   description="Belastningsutjämna mellan flera behållare i ett Azure Container Service-kluster."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Behållare, Micro-tjänster, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="rogardle"/>


# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Belastningsutjämna behållare i ett Azure Container Service-kluster

I den här artikeln visar vi hur du skapar en intern belastningsutjämnare i en DC/OS-hanterad Azure Container Service med Marathon-LB. Detta gör att du kan skala dina program vågrätt. Du kan också använda offentliga och privata agentkluster genom att placera en belastningsutjämnare på det offentliga klustret och dina programbehållare på det privata klustret.

## <a name="prerequisites"></a>Krav

[Distribuera en instans av Azure Container Service](container-service-deployment.md) med Orchestrator-typen DC/OS och [kontrollera att klienten kan ansluta till klustret](container-service-connect.md). 

## <a name="load-balancing"></a>Belastningsutjämning

Det finns två belastningsutjämningslager i Container Service-klustret som vi ska skapa: 

  1. Azure Load Balancer tillhandahåller offentliga startpunkter (de som slutanvändarna kommer till). Detta tillhandahålls automatiskt av Azure Container Service och konfigureras som standard att exponera port 80, 443 och 8080.
  2. Marathon Load Balancer (marathon-lb) dirigerar inkommande begäranden till behållarinstanser som hanterar dessa begäranden. När vi skalar behållarna som tillhandahåller vår webbtjänst anpassas marathon-lb dynamiskt. Den här belastningsutjämnaren tillhandahålls inte som standard i Container Service, men är mycket enkel att installera.

## <a name="marathon-load-balancer"></a>Marathon Load Balancer

Marathon Load Balancer konfigurerar om sig själv dynamiskt baserat på de behållare som du har distribuerat. Den kan även återhämta sig automatiskt vid en eventuell förlust av en behållare eller agent. Om detta inträffar startar Apache Mesos bara om behållaren någon annanstans och marathon-lb anpassas automatiskt.

Du kan installera Marathon Load Balancer antingen via DC/OS-webbgränssnittet eller från kommandoraden.

### <a name="install-marathon-lb-using-dc/os-web-ui"></a>Installera Marathon-LB med hjälp av DC/OS-webbgränssnittet

  1. Klicka på ”Universe”.
  2. Sök efter ”Marathon LB”.
  3. Klicka på ”Installera”.

![Installera marathon-lb via webbgränssnittet för DC/OS](./media/dcos/marathon-lb-install.png)

### <a name="install-marathon-lb-using-the-dc/os-cli"></a>Installera Marathon-LB via kommandoradsgränssnittet för DC/OS

När du har installerat DC/OS CLI och bekräftat att du kan ansluta till klustret kör du följande kommando från klientdatorn:

```bash
dcos package install marathon-lb
```

Det här kommandot installerar automatiskt belastningsutjämnaren på det offentliga agentklustret.

## <a name="deploy-a-load-balanced-web-application"></a>Distribuera en webbapp för belastningsutjämning

Nu när vi har marathon-lb-paketet kan vi distribuera en programbehållare som vi vill belastningsutjämna. I det här exemplet distribuerar vi en enkel webbserver med följande konfiguration:

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

  * Ange värdet för `HAProxy_0_VHOST` till det fullständiga domännamnet för belastningsutjämnaren för dina agenter. Detta har formatet `<acsName>agents.<region>.cloudapp.azure.com`. Om du till exempel skapar ett Container Service-kluster med namnet `myacs` i regionen `West US`, är FQDN: `myacsagents.westus.cloudapp.azure.com`. Du kan även hitta den här informationen genom att leta efter belastningsutjämnaren med ”agent” i namnet när du letar igenom resurserna i resursgruppen som du skapade för Container Service på [Azure-portalen](https://portal.azure.com).
  * Ange servicePort till en port > = 10000. Nu identifieras tjänsten som körs i behållaren, och marathon-lb använder den här informationen för att identifiera tjänster som ska belastningsutjämnas.
  * Ange `HAPROXY_GROUP`-etiketten till ”external”.
  * Ange `hostPort` till 0. Detta gör att Marathon godtyckligt allokerar en tillgänglig port.
  * Ange `instances` till antalet instanser som du vill skapa. Du kan skala upp eller ned antalet senare.

Det är värt att notera att standard-Marathon distribueras till det privata klustret. Det innebär att ovanstående distribution endast nås via en belastningsutjämnare, vilket vanligtvis är det beteende som vi vill ha.

### <a name="deploy-using-the-dc/os-web-ui"></a>Distribuera med hjälp av webbgränssnittet för DC/OS

  1. Gå till Marathon-sidan på http://localhost/marathon (efter att du konfigurerat din [SSH-tunnel](container-service-connect.md) och klicka på `Create Appliction`
  2. Klicka på `JSON Mode` längst upp till höger i dialogrutan `New Application`
  3. Klistra in ovanstående JSON i redigeraren
  4. Klicka på `Create Appliction`

### <a name="deploy-using-the-dc/os-cli"></a>Distribuera via kommandoradsgränssnittet för DC/OS

Om du vill distribuera det här programmet med kommandoradsgränssnittet för DC/OS kopierar du bara ovanstående JSON till en fil med namnet `hello-web.json` och kör:

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure Load Balancer

Som standard exponerar Azure Load Balancer portarna 80, 8080 och 443. Om du använder en av dessa tre portar (som vi gör i ovanstående exempel) behöver du inte göra något. Du bör kunna nå belastningsutjämnarens FQDN för din agent – och varje gång du uppdaterar når du en av de tre webbservrarna baserat på en resursallokeringsmodell (round-robin). Om du använder en annan port måste du dock lägga till en resursallokeringsregel och en avsökning i belastningsutjämnaren för den port som du använde. Du kan göra detta från [Azure CLI](../xplat-cli-azure-resource-manager.md), med kommandona `azure network lb rule create` och `azure network lb probe create`. Du kan också göra detta i Azure Portal.


## <a name="additional-scenarios"></a>Fler scenarier

Du kan ha ett scenario där du använder olika domäner för att exponera olika tjänster. Exempel:

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

Information om hur du associerar domäner med specifika marathon-lb-sökvägar finns i [Virtuella värdar](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/).

Som ett alternativ kan du exponera andra portar och mappa om dem till rätt tjänst bakom marathon-lb. Exempel:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432


## <a name="next-steps"></a>Nästa steg

Mer information om [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/) finns i DC/OS-dokumentationen.



<!--HONumber=Oct16_HO3-->


