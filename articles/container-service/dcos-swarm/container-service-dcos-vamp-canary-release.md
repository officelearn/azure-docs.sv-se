---
title: (INAKTUELL) Canary release med Vamp på Azure DC/OS-kluster
description: Hur du använder Vamp till canary release-tjänster och tillämpa smart trafikfiltrering i ett Azure Container Service DC/OS-kluster
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 7ab63b869d9cd8a5b1f2b60429c5b54d0da5761f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002062"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(INAKTUELL) Mikrotjänster av canary release med Vamp på ett Azure Container Service DC/OS-kluster

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här genomgången ska konfigurera vi Vamp på Azure Container Service med DC/OS-kluster. Vi canary release-Vamp demo tjänsten ”sava” och Lös inkompatibilitet tjänsten med Firefox genom att använda smarta trafikfiltrering. 

> [!TIP] 
> I den här genomgången Vamp körs på ett DC/OS-kluster, men du kan också använda Vamp med Kubernetes som orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Om kanarieversioner och Vamp


[Kontrollvärde lanserar](https://martinfowler.com/bliki/CanaryRelease.html) är en smart distributionsstrategi som antas av innovativa organisationer som Netflix, Facebook och Spotify. Det är en metod som passar, eftersom det minskar problem, introducerar säkerhet-nät och ökar innovation. Så varför inte alla företag som använder den? Att utöka en CI/CD-pipeline för att inkludera kontrollvärde strategier mer komplex och kräver omfattande devops-kunskaper och erfarenhet. Det är tillräckligt för att blockera mindre företag och företag som är både innan de även komma igång. 

[Vamp](http://vamp.io/) ett system för öppen källkod som utformats för att underlätta övergången och ta med Kanarieöarna lanserar funktioner till din önskade behållare scheduler. Vamps kontrollvärde funktioner är mer omfattande än procent-baserade distributioner. Trafiken kan filtreras och delas upp på flera olika villkor, till exempel till specifika målanvändare, IP-adressintervall eller enheter. Vamp spårar och analyserar prestandamått, vilket ger automation baserat på verkliga data. Du kan ställa in automatisk återställning vid fel eller skala en enskild tjänst varianter baserat på belastning eller svarstid.

## <a name="set-up-azure-container-service-with-dcos"></a>Ställ in Azure Container Service med DC/OS



1. [Distribuera ett DC/OS-kluster](container-service-deployment.md) med en huvudnod och två agenter av standardstorleken. 

2. [Skapa en SSH-tunnel](../container-service-connect.md) att ansluta till DC/OS-kluster. Den här artikeln förutsätter att du skapa en tunnel till klustret på lokal port 80.


## <a name="set-up-vamp"></a>Konfigurera Vamp

Nu när du har ett DC/OS-kluster som körs kan du installera Vamp från Gränssnittet för DC/OS (http://localhost:80). 

![DC/OS-gränssnitt:](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Installationen är klar i två steg:

1. **Installera Elasticsearch**.

2. Sedan **distribuera Vamp** genom att installera paketet Vamp DC/OS universe.

### <a name="deploy-elasticsearch"></a>Installera Elasticsearch

Vamp kräver Elasticsearch för insamling av mått och aggregering. Du kan använda den [magneticio Docker-avbildningar](https://hub.docker.com/r/magneticio/elastic/) att distribuera en kompatibel Vamp Elasticsearch-stack.

1. I DC/OS-Gränssnittet går du till **Services** och klicka på **distribuera tjänst**.

2. Välj **JSON-läget** från den **distribuera ny tjänst** popup.

  ![Välj JSON-läge](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Klistra in följande JSON. Den här konfigurationen kör behållaren med 1 GB RAM-minne och en grundläggande kontroll på Elasticsearch-port.
  
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
  

3. Klicka på **distribuera**.

  DC/OS distribuerar Elasticsearch-behållaren. Du kan följa förloppet på den **Services** sidan.  

  ![distribuera e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Distribuera Vamp

När Elasticsearch rapporterar som **kör**, du kan lägga till Vamp DC/OS Universe-paketet. 

1. Gå till **Universe** och Sök efter **vamp**. 
  ![Vamp på DC/OS universe](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klicka på **installera** bredvid vamp paketera och välj **avancerade installationen**.

3. Bläddra nedåt och ange följande elasticsearch-url: `http://elasticsearch.marathon.mesos:9200`. 

  ![Ange URL: en för Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klicka på **granska och installera**, klicka sedan på **installera** att starta distributionen.  

  DC/OS distribuerar alla nödvändiga komponenter för Vamp. Du kan följa förloppet på den **Services** sidan.
  
  ![Distribuera Vamp som universe paket](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. När distributionen är klar kan du komma åt Vamp-Användargränssnittet:

  ![Vamp-tjänsten på DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Distribuera din första tjänst

Nu den Vamp är igång kan du distribuera en tjänst från en skiss. 

I sin enklaste form en [Vamp skissen](http://vamp.io/documentation/using-vamp/blueprints/) beskriver slutpunkter (gateway), kluster och tjänster för att distribuera. Vamp använder kluster för att gruppera olika varianter av samma tjänst i logiska grupper kontrollvärde lanserar eller A / B-testning.  

Det här scenariot använder ett monolitiskt exempelprogram som kallas [ **sava**](https://github.com/magneticio/sava), vilket är version 1.0. Gammal är paketerat i en Docker-behållare som finns i Docker Hub under magneticio/sava:1.0.0. Appen körs vanligtvis på port 8080, men du vill exponera dem under port 9050 i det här fallet. Distribuera appen via Vamp med hjälp av en enkel skiss.

1. Gå till **distributioner**.

2. Klicka på **Lägg till**.

3. Klistra in följande skissen YAML. Den här skissen innehåller ett kluster med endast en tjänst-variant som vi ändra i ett senare steg:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Klicka på **Spara**. Vamp initierar distributionen.

Distributionen visas på den **distributioner** sidan. Klicka på distributionen för att övervaka dess status.

![Vamp gränssnitt – distribuera sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava service i Användargränssnittet för Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Två gateways skapas, som visas på den **gatewayer** sidan:

* en stabil slutpunkt för att få åtkomst till tjänsten som körs (port 9050) 
* en hanterad Vamp intern gateway (mer om den här gatewayen senare). 

![Vamp UI - sava gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Tjänsten sava har nu distribuerats, men du kan inte komma åt den externt eftersom Azure Load Balancer inte vet för att vidarebefordra trafik till den ännu. Uppdatera Azure nätverkskonfigurationen för att komma åt tjänsten.


## <a name="update-the-azure-network-configuration"></a>Uppdatera konfigurationen av Azure-nätverk

Vamp distribuerade tjänsten sava på DC/OS-agentnoder, exponera en stabil slutpunkten på port 9050. Gör följande ändringar i Azure-nätverkskonfigurationen i distributionen av klustret för att komma åt tjänsten utanför DC/OS-klustret: 

1. **Konfigurera Azure Load Balancer** för agenterna (resursen med namnet **dcos-agent-lb-xxxx**) med en hälsoavsökning och en regel för att vidarebefordra trafik på port 9050 till sava-instanser. 

2. **Uppdatera nätverkssäkerhetsgruppen** för offentliga agenter (resursen med namnet **XXXX-agent-public-nsg-XXXX**) som tillåter trafik på port 9050.

Detaljerade steg för att utföra dessa uppgifter med Azure portal, se [aktivera offentlig åtkomst till ett Azure Container Service-program](container-service-enable-public-access.md). Ange port 9050 för alla portinställningar.


När allt har skapats går du till den **översikt** -bladet i DC/OS-agentens belastningsutjämnare (resursen med namnet **dcos-agent-lb-xxxx**). Hitta den **offentliga IP-adressen**, och Använd adress för att komma åt sava på port 9050.

![Azure portal – get offentlig IP-adress](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Kör en canary release

Anta att du har en ny version av det här programmet som du vill att kontrollvärde ut i produktion. Du har den behållare som magneticio/sava:1.1.0 och är redo att användas. Vamp kan du enkelt lägga till nya tjänster i distributionen som körs. Tjänsterna ”kopplade” distribueras tillsammans med befintliga tjänster i klustret, och tilldelas en vikt på 0%. Ingen trafik dirigeras till en nyligen kopplade tjänsten tills du justera fördelning av trafik. Vikt skjutreglaget i Användargränssnittet för Vamp ger dig fullständig kontroll över distributionen så att du inkrementell justeringar (canary release) eller en omedelbar återställning.

### <a name="merge-a-new-service-variant"></a>Koppla en ny variant av tjänsten

Att sammanfoga den nya sava 1.1-tjänsten med distributionen som körs:

1. I Användargränssnittet för Vamp klickar du på **skisser**.

2. Klicka på **Lägg till** och klistra in följande skissen YAML: den här skissen beskriver en ny tjänst-variant (sava: 1.1.0) ska distribueras i det befintliga klustret (sava_cluster).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Klicka på **Spara**. Skissen lagras och visas på den **skisser** sidan.

4. Öppna åtgärdsmenyn på sava: 1.1 skissen och klicka på **koppla till**.

  ![Vamp UI - skisser](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Välj den **sava** distribution och klickar på **sammanfoga**.

  ![Vamp UI - merge skissen till distribution](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp distribuerar den nya sava: 1.1.0 service varianten som beskrivs i skissen tillsammans med sava: 1.0.0 i den **sava_cluster** för distributionen som körs. 

![Vamp UI - uppdaterade sava distribution](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Den **sava/sava_cluster/webport** gateway (klusterslutpunkten) har också uppdaterats, att lägga till en väg till den nyligen distribuerade sava: 1.1.0. Nu kan ingen trafik dirigeras här (den **vikt** är inställd på 0%).

![Vamp UI - kluster-gateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Canary release

Med båda versionerna av sava som distribueras i samma kluster, ändra fördelningen av trafiken mellan dem genom att flytta den **vikt** skjutreglaget.

1. Klicka på ![Vamp gränssnitt – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) bredvid **vikt**.

2. Ange viktfördelningen till 50% / 50% och klickar på **spara**.

  ![Vamp UI - gateway vikt skjutreglaget](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Gå tillbaka till webbläsaren och uppdatera sidan sava några gånger. Sava programmet nu växlar mellan en sava: 1.0-sida och en sava: 1.1-sida.

  ![Alternerande sava1.0 och sava1.1 tjänster](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Den här växlingar sidans fungerar bäst med ”Incognito” eller ”anonym” läget i webbläsaren på grund av cachelagring av statiska tillgångar.
  >

### <a name="filter-traffic"></a>Filtrera trafik

Anta att efter distributionen du upptäckt inkompatibilitet i sava: 1.1.0 att visas i Firefox webbläsare. Du kan ange Vamp att filtrera inkommande trafik och dirigera alla Firefox användare tillbaka till kända stabil sava: 1.0.0. Det här filtret löser direkt avbrott för Firefox användare, medan alla andra fortsätter att dra nytta av fördelarna med förbättrad sava: 1.1.0.

Vamp använder **villkor** att filtrera trafik mellan vägar i en gateway. Trafiken är först filtreras och dirigeras enligt de villkor som tillämpas på varje väg. Alla återstående trafiken ska distribueras enligt inställningarna för gateway-vikt.

Du kan skapa ett villkor för att filtrera alla Firefox-användare och dirigera dem till den gamla sava: 1.0.0:

1. På sava/sava_cluster/webport **gatewayer** klickar du på ![Vamp gränssnitt – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) att lägga till en **villkor** till route sava/sava_cluster/sava:1.0.0/webport. 

2. Ange villkoret **användaragent == Firefox** och klicka på ![Vamp gränssnitt – spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp lägger till på villkoret med en standardvärdet 0%. Om du vill börja filtrera trafik, måste du justera styrkan villkor.

3. Klicka på ![Vamp gränssnitt – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) att ändra den **STYRKA** tillämpas på villkoret.
 
4. Ange den **STYRKA** till 100% och klickar på ![Vamp gränssnitt – spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) att spara.

  Vamp nu skickar all trafik som matchar villkoret (alla Firefox användare) till sava: 1.0.0.

  ![Vamp UI – Använd villkor till gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Slutligen kan du justera gateway vikten för att skicka alla återstående trafik (alla icke-Firefox användare) till den nya sava: 1.1.0. Klicka på ![Vamp gränssnitt – redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) bredvid **vikt** och ange viktfördelningen så 100% dirigeras till route sava/sava_cluster/sava:1.1.0/webport.

  All trafik som inte filtreras med villkoret omdirigeras nu till den nya sava: 1.1.0.

6. Öppna två olika webbläsare (en Firefox och en annan webbläsare) och tjänsten sava från både om du vill se filtret fungerar i praktiken. Alla Firefox-begäranden skickas till sava: 1.0.0, medan alla andra webbläsare dirigeras till sava: 1.1.0.

  ![Vamp gränssnitt – filtrera trafik](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Addera

Den här artikeln är en snabb introduktion till Vamp i ett DC/OS-kluster. Till att börja med du fick Vamp och som körs på din Azure Container Service DC/OS-kluster distribueras en tjänst med en Vamp skiss och nås vid exponerade slutpunkt (gateway).

Vi också snuddat vid vissa kraftfulla funktionerna i Vamp: sammanslagning av en ny variant service-distributionen som körs och introduktion till stegvis sedan filtrerar trafik för att lösa en kända kompatibilitetsproblem.


## <a name="next-steps"></a>Nästa steg

* Läs om hur du hanterar Vamp åtgärder via den [Vamp REST API](http://vamp.io/documentation/api/api-reference/).

* Skapa Vamp automatiseringsskript i Node.js och köra dem som [Vamp arbetsflöden](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Se ytterligare [VAMP självstudier](http://vamp.io/documentation/tutorials/).

