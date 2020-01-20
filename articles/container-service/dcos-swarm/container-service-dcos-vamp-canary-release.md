---
title: FÖRÅLDRAD Kanarie release med vamp på Azure DC/OS-kluster
description: Använda vamp till Kanarie release Services och använda Smart trafik filtrering på ett Azure Container Service DC/OS-kluster
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 60ff148e044df81e64b54fc48c1cb6f67aee14df
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275661"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>FÖRÅLDRAD Kanarie release-mikrotjänster med vamp på ett Azure Container Service DC/OS-kluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här genom gången ställer vi in vamp på Azure Container Service med ett DC/OS-kluster. Vi lanserar vamp demo-tjänsten "Sava" och löser sedan en inkompatibilitet för tjänsten med Firefox genom att använda Smart trafik filtrering. 

> [!TIP] 
> I den här genom gången körs vamp i ett DC/OS-kluster, men du kan också använda vamp med Kubernetes som Orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Om Kanarie-versioner och vamp


En miljö [fri släppning](https://martinfowler.com/bliki/CanaryRelease.html) är en strategi för smart distribution som antas av innovativa organisationer som Netflix, Facebook och Spotify. Det är en metod som passar bra eftersom det minskar problem, introducerar säkerhets nät och ökar innovationer. Varför använder de inte alla företag? Att utöka en CI/CD-pipeline för att innefatta Kanarie strategier ökar komplexiteten och kräver omfattande DevOps-kunskaper och erfarenhet. Det räcker med att blockera mindre företag och företag innan de kommer igång. 

[Vamp](https://vamp.io/) är ett system med öppen källkod som har utformats för att under lätta den här över gången och ta fram funktioner för att få kontroll över en vald container Scheduler. Vamps Kanarie-funktioner går utöver procentbaserade distributioner. Trafiken kan filtreras och delas upp i en mängd olika villkor, till exempel för att rikta in specifika användare, IP-intervall eller enheter. Vamp spårar och analyserar prestanda mått, vilket möjliggör automatisering baserat på verkliga data. Du kan ställa in automatisk återställning vid fel eller skala enskilda varianter utifrån belastnings-eller svars tider.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurera Azure Container Service med DC/OS



1. [Distribuera ett DC/OS-kluster](container-service-deployment.md) med en huvud server och två agenter som standard storlek. 

2. [Skapa en SSH-tunnel](../container-service-connect.md) för att ansluta till DC/OS-klustret. Den här artikeln förutsätter att du tunnlar till klustret på den lokala porten 80.


## <a name="set-up-vamp"></a>Konfigurera vamp

Nu när du har ett DC/OS-kluster som kör kan du installera vamp från DC/OS-gränssnittet (http:\//localhost: 80). 

![DC/OS-gränssnitt:](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Installationen görs i två steg:

1. **Distribuera ElasticSearch**.

2. **Distribuera sedan vamp** genom att installera paketet vamp DC/OS universum.

### <a name="deploy-elasticsearch"></a>Distribuera ElasticSearch

Vamp kräver ElasticSearch för insamling och insamling av mått. Du kan använda [Magneticio Docker-avbildningar](https://hub.docker.com/r/magneticio/elastic/) för att distribuera en kompatibel vamp ElasticSearch-stack.

1. I användar gränssnittet för DC/OS går du till **tjänster** och klickar på **distribuera tjänst**.

2. Välj **JSON-läge** från popup-fönstret **distribuera ny tjänst** .

   ![Välj JSON-läge](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Klistra in i följande JSON. Den här konfigurationen kör behållaren med 1 GB RAM-minne och en grundläggande hälso kontroll på ElasticSearch-porten.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Klicka på **Distribuera**.

   DC/OS distribuerar ElasticSearch-behållaren. Du kan följa förloppet på sidan **tjänster** .  

   ![vill du distribuera e? ElasticSearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Distribuera vamp

När ElasticSearch rapporter har **körts**kan du lägga till paketet vamp DC/OS universum. 

1. Gå till **universum** och Sök efter **vamp**. 
   ![vamp på DC/OS-universum](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klicka på **Installera** bredvid vamp-paketet och välj **Avancerad installation**.

3. Rulla ned och ange följande ElasticSearch-URL: `http://elasticsearch.marathon.mesos:9200`. 

   ![Ange ElasticSearch-URL](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klicka på **Granska och installera**och klicka sedan på **Installera** för att starta distributionen.  

   DC/OS distribuerar alla nödvändiga vamp-komponenter. Du kan följa förloppet på sidan **tjänster** .
  
   ![Distribuera vamp som universum-paket](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. När distributionen har slutförts kan du komma åt vamp-gränssnittet:

   ![Vamp-tjänst på DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp-gränssnitt](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Distribuera din första tjänst

Nu när vamp är igång kan du distribuera en tjänst från en skiss. 

I sin enklaste form beskriver en [vamp skiss](https://vamp.io/documentation/using-vamp/blueprints/) vilka slut punkter (gateways), kluster och tjänster som ska distribueras. Vamp använder kluster för att gruppera olika varianter av samma tjänst i logiska grupper för kontroll av Kanarie-eller A-B-test.  

I det här scenariot används ett exempel på ett monolitisk-program som kallas [**Sava**](https://github.com/magneticio/sava), som är i version 1,0. Monolit paketeras i en Docker-behållare, som finns i Docker Hub under magneticio/Sava: 1.0.0. Appen körs normalt på Port 8080, men du vill visa den under port 9050 i det här fallet. Distribuera appen via vamp med hjälp av en enkel skiss.

1. Gå till **distributioner**.

2. Klicka på **Lägg till**.

3. Klistra in i följande skiss YAML. Den här skissen innehåller ett kluster med endast en variant för tjänsten som vi ändrar i ett senare steg:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Klicka på **Spara**. Vamp initierar distributionen.

Distributionen visas på sidan **distributioner** . Klicka på distributionen för att övervaka dess status.

![Vamp UI – distribuera Sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Sava-tjänst i vamp-ANVÄNDARGRÄNSSNITTET](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Två gateways skapas, som visas på sidan **gatewayer** :

* en stabil slut punkt för att komma åt den aktiva tjänsten (port 9050) 
* en vamp-hanterad intern Gateway (mer på denna gateway senare). 

![Vamp UI – Sava-gatewayer](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Sava-tjänsten har nu distribuerats, men du kan inte komma åt den externt eftersom Azure Load Balancer inte vet att vidarebefordra trafik till den ännu. Uppdatera konfigurationen av Azure-nätverk för att få åtkomst till tjänsten.


## <a name="update-the-azure-network-configuration"></a>Uppdatera konfigurationen av Azure-nätverket

Vamp distribuerade Sava-tjänsten på DC/OS-agent-noderna som exponerar en stabil slut punkt på port 9050. Om du vill komma åt tjänsten från en plats utanför DC/OS-klustret gör du följande ändringar i Azure-nätverks konfigurationen i kluster distributionen: 

1. **Konfigurera Azure Load Balancer** för agenterna (resursen med namnet **DCOS-Agent-LB-xxxx**) med en hälso avsökning och en regel för att vidarebefordra trafik på port 9050 till Sava-instanserna. 

2. **Uppdatera nätverks säkerhets gruppen** för offentliga agenter (resursen med namnet **xxxx-agent-Public-NSG-xxxx**) för att tillåta trafik på port 9050.

Detaljerade anvisningar för att utföra dessa uppgifter med hjälp av Azure Portal finns i [Aktivera offentlig åtkomst till ett Azure Container Service program](container-service-enable-public-access.md). Ange port 9050 för alla port inställningar.


När allt har skapats går du till **översikts** bladet för BELASTNINGSUTJÄMNAREN för DC/OS-agenten (resursen med namnet **DCOS-Agent-LB-xxxx**). Hitta den **offentliga IP-adressen**och Använd adressen för att få åtkomst till Sava på port 9050.

![Azure Portal-Hämta offentlig IP-adress](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Köra en Kanarie-version

Anta att du har en ny version av det här programmet som du vill att Kanarie-versionen ska vara i produktion. Du har den behållare som magneticio/Sava: 1.1.0 och är redo att sätta igång. Med vamp kan du enkelt lägga till nya tjänster i distributionen. Dessa "sammanfogade" tjänster distribueras tillsammans med befintliga tjänster i klustret och tilldelas en vikt på 0%. Ingen trafik dirigeras till en nyligen sammanslagen tjänst tills du justerar trafik distributionen. Skjutreglaget viktning i vamp-ANVÄNDARGRÄNSSNITTET ger dig fullständig kontroll över distributionen, vilket möjliggör stegvisa justeringar (Kanarie release) eller en omedelbar återställning.

### <a name="merge-a-new-service-variant"></a>Sammanfoga en ny tjänst variant

Så här sammanfogar du den nya Sava 1,1-tjänsten med den pågående distributionen:

1. Klicka på **ritningar**i vamp-användargränssnittet.

2. Klicka på **Lägg till** och klistra in i följande skiss yaml: den här skissen beskriver en ny variant av tjänsten (Sava: 1.1.0) som ska distribueras inom det befintliga klustret (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Klicka på **Spara**. Skissen lagras och visas på sidan **ritningar** .

4. Öppna åtgärds menyn i skissen Sava: 1.1 och klicka på **sammanfoga till**.

   ![Vamp UI-ritningar](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Välj **Sava** -distributionen och klicka på **sammanfoga**.

   ![Vamp UI-sammanfoga skiss till distribution](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp distribuerar den nya Sava: 1.1.0 som beskrivs i skissen jämte Sava: 1.0.0 i **sava_cluster** av distributionen som körs. 

![Vamp-gränssnitt – uppdaterad Sava-distribution](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**Sava/sava_cluster/webport** -gatewayen (kluster slut punkten) uppdateras också och lägger till en väg till den nyligen distribuerade Sava: 1.1.0. I det här läget dirigeras ingen trafik här ( **vikten** anges till 0%).

![Vamp UI – kluster-Gateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanarie-version

Med båda versionerna av Sava distribuerade i samma kluster justerar du trafiken mellan dem genom att flytta skjutreglaget för **vikt** .

1. Klicka på ![vamp UI – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) bredvid **vikt**.

2. Ange viktnings fördelningen till 50%/50% och klicka på **Spara**.

   ![Vamp UI – skjutreglage för gateway-viktning](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Gå tillbaka till webbläsaren och uppdatera Sava-sidan några gånger. Sava-programmet växlar nu mellan en Sava: 1.0-sida och en Sava: 1.1-sida.

   ![alternerande Sava 1.0-och Sava 1.1-tjänster](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Den här växlingen av sidan fungerar bäst med läget "Incognito" eller "Anonym" i din webbläsare på grund av cachelagringen av statiska till gångar.
  >

### <a name="filter-traffic"></a>Filtrera trafik

Anta efter distributionen att du har identifierat inkompatibilitet i Sava: 1.1.0 som orsakar visnings problem i Firefox-webbläsare. Du kan ange vamp för att filtrera inkommande trafik och dirigera alla Firefox-användare tillbaka till den kända stabila Sava: 1.0.0. Med det här filtret kan du snabbt lösa störningar för Firefox-användare, medan alla andra fortsätter att njuta av fördelarna med den förbättrade Sava: 1.1.0.

Vamp använder **villkor** för att filtrera trafik mellan vägar i en gateway. Trafiken filtreras först och dirigeras enligt de villkor som tillämpas på varje väg. All återstående trafik distribueras enligt inställningen för gateway-viktning.

Du kan skapa ett villkor för att filtrera alla Firefox-användare och dirigera dem till den gamla Sava: 1.0.0:

1. På sidan Sava/sava_cluster/webport- **gatewayer** klickar du på ![vamp UI-redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) för att lägga till ett **villkor** i Route Sava/sava_cluster/Sava: 1.0.0/webport. 

2. Ange villkoret **User-Agent = = Firefox** och klicka på ![vamp UI – Spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   Vamp lägger till villkoret med en standard styrka på 0%. Om du vill börja filtrera trafik måste du justera villkors styrkan.

3. Klicka på ![vamp UI – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) för att ändra **styrkan** som tillämpas på villkoret.
 
4. Ange **styrkan** till 100% och klicka på ![vamp UI – Spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) för att spara.

   Vamp skickar nu all trafik som matchar villkoret (alla Firefox-användare) till Sava: 1.0.0.

   ![Vamp UI – tillämpa villkor för gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Slutligen justerar du Gateway-viktningen för att skicka all återstående trafik (alla användare som inte är Firefox) till den nya Sava: 1.1.0. Klicka på ![vamp UI – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) bredvid **vikt** och Ställ in viktnings fördelningen så att 100% dirigeras till Route sava/sava_cluster/Sava: 1.1.0/webport.

   All trafik som inte filtrerats av villkoret dirigeras nu till den nya Sava: 1.1.0.

6. Öppna två olika webbläsare (en Firefox och en annan webbläsare) och få åtkomst till Sava-tjänsten från båda för att se hur filtreringen fungerar. Alla Firefox-begäranden skickas till Sava: 1.0.0, medan alla andra webbläsare dirigeras till Sava: 1.1.0.

   ![Vamp UI – filter trafik](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Summering

Den här artikeln var en snabb introduktion till vamp på ett DC/OS-kluster. För starter har du vamp igång på ditt Azure Container Service DC/OS-kluster, distribuerat en tjänst med en vamp-skiss och till gång till den med exponerad slut punkt (Gateway).

Vi har också använt några kraftfulla funktioner i vamp: sammanslagning av en ny tjänst-variant till den pågående distributionen och introducerar den stegvis, och filtrerar sedan trafiken för att lösa en känd inkompatibilitet.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att hantera vamp-åtgärder via [vamp-REST API](https://vamp.io/documentation/api/api-reference/).

* Skapa vamp Automation-skript i Node. js och kör dem som [vamp-arbetsflöden](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Se ytterligare [vamp-självstudier](https://vamp.io/documentation/tutorials/).

