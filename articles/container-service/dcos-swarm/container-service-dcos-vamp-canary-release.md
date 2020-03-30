---
title: (FÖRÅLDRAD) Kanariefågelversion med Vamp i Azure DC/OS-kluster
description: Så här använder du Vamp för canary release-tjänster och tillämpar smart trafikfiltrering i ett AZURE Container Service DC/OS-kluster
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77189117"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(FÖRÅLDRAD) Mikrotjänster för canary-frisläppning med Vamp i ett DC/OS-kluster för Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

I den här genomgången konfigurerar vi Vamp på Azure Container Service med ett DC/OS-kluster. Vi kanariefågel släppa Vamp demo tjänst "sava", och sedan lösa en inkompatibilitet av tjänsten med Firefox genom att tillämpa smart trafik filtrering. 

> [!TIP] 
> I den här genomgången körs Vamp på ett DC/OS-kluster, men du kan också använda Vamp med Kubernetes som orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Om kanariefågel utgåvor och Vamp


[Canary release](https://martinfowler.com/bliki/CanaryRelease.html) är en smart distributionsstrategi som antagits av innovativa organisationer som Netflix, Facebook och Spotify. Det är en strategi som är vettigt, eftersom det minskar problem, introducerar skyddsnät och ökar innovationen. Så varför är inte alla företag som använder det? Att utöka en CI/CD-pipeline till att omfatta kanariefågelstrategier ger komplexitet och kräver omfattande kunskap och erfarenhet. Det räcker för att blockera mindre företag och företag innan de ens kommer igång. 

[Vamp](https://vamp.io/) är ett system med öppen källkod som utformats för att underlätta denna övergång och få kanariefågel släppa funktioner till din föredragna behållare schemaläggare. Vamp's canary funktionalitet går utöver procent-baserade utrullningar. Trafiken kan filtreras och delas på en mängd olika förhållanden, till exempel för att rikta specifika användare, IP-intervall eller enheter. Vamp spårar och analyserar prestandamått, vilket möjliggör automatisering baserat på verkliga data. Du kan ställa in automatisk återställning på fel eller skala enskilda tjänstvarianter baserat på belastning eller svarstid.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurera Azure Container Service med DC/OS



1. [Distribuera ett DC/OS-kluster](container-service-deployment.md) med en huvudhanterare och två agenter av standardstorlek. 

2. [Skapa en SSH-tunnel](../container-service-connect.md) för att ansluta till DC/OS-klustret. Den här artikeln förutsätter att du tunnel till klustret på lokal port 80.


## <a name="set-up-vamp"></a>Ställ in Vamp

Nu när du har ett DC/OS-kluster som körs kan du installera\/Vamp från DC/OS-användargränssnittet (http: /localhost:80). 

![DC/OS-gränssnitt:](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Installationen sker i två steg:

1. **Distribuera Elasticsearch**.

2. Distribuera sedan **Vamp** genom att installera Vamp DC/OS-universumpaketet.

### <a name="deploy-elasticsearch"></a>Distribuera elasticsearch

Vamp kräver Elasticsearch för statistikinsamling och aggregering. Du kan använda [magneticio Docker-avbildningarna](https://hub.docker.com/r/magneticio/elastic/) för att distribuera en kompatibel Vamp Elasticsearch-stack.

1. Gå till **Tjänster** i DC/OS-användargränssnittet och klicka på **Distribuera tjänst**.

2. Välj **JSON-läge** i **popup-programmet Distribuera ny tjänst.**

   ![Välj JSON-läge](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Klistra in följande JSON. Den här konfigurationen kör behållaren med 1 GB RAM och en grundläggande hälsokontroll av Elasticsearch-porten.
  
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

   DC/OS distribuerar Elasticsearch-behållaren. Du kan följa förloppet på sidan **Tjänster.**  

   ![distribuera e? Elasticsearch (Elasticsearch)](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Distribuera Vamp

När Elasticsearch rapporterar som **Löpning**kan du lägga till Paketet Vamp DC/OS Universe. 

1. Gå till **universum** och söka efter **vampyr.** 
   ![Vamp på DC / OS universum](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klicka på **Installera** bredvid vampyrpaketet och välj **Avancerad installation**.

3. Bläddra nedåt och ange följande elasticsearch-url: `http://elasticsearch.marathon.mesos:9200`. 

   ![Ange URL för elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klicka på **Granska och installera**och sedan på **Installera** för att starta distributionen.  

   DC/OS distribuerar alla nödvändiga Vamp-komponenter. Du kan följa förloppet på sidan **Tjänster.**
  
   ![Distribuera Vamp som universumpaket](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. När distributionen är klar kan du komma åt Vamp-användargränssnittet:

   ![Vamp-tjänst på DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Distribuera din första tjänst

Nu när Vamp är igång, distribuera en tjänst från en ritning. 

I det enklaste formuläret beskriver en [Vamp-skiss](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) slutpunkter (gateways), kluster och tjänster som ska distribueras. Vamp använder kluster för att gruppera olika varianter av samma tjänst i logiska grupper för kanariefågelfrisläppning eller A/B-testning.  

Det här scenariot använder ett exempel monolitisk program som kallas [**sava**](https://github.com/magneticio/sava), som finns på version 1.0. Monoliten är förpackad i en Docker-behållare, som ligger i Docker Hub under magneticio/sava:1.0.0. Appen körs normalt på port 8080, men du vill exponera den under port 9050 i det här fallet. Distribuera appen via Vamp med hjälp av en enkel skiss.

1. Gå till **Distributioner**.

2. Klicka på **Lägg till**.

3. Klistra in följande skiss YAML. Den här skissen innehåller ett kluster med endast en tjänstvariant, som vi ändrar i ett senare steg:

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

4. Klicka på **Spara**. Vamp initierar utplaceringen.

Distributionen visas på sidan **Distributioner.** Klicka på distributionen för att övervaka dess status.

![Vamp UI - distribuera sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava service i Vamp UI](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Två gateways skapas som visas på sidan **Gateways:**

* en stabil slutpunkt för att komma åt den tjänst som körs (port 9050) 
* en Vamp-hanterad intern gateway (mer om den här gatewayen senare). 

![Vamp UI - sava gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Sava-tjänsten har nu distribuerats, men du kan inte komma åt den externt eftersom Azure Load Balancer inte vet att vidarebefordra trafik till den ännu. Uppdatera Azure-nätverkskonfigurationen för att komma åt tjänsten.


## <a name="update-the-azure-network-configuration"></a>Uppdatera Azure-nätverkskonfigurationen

Vamp distribuerade sava-tjänsten på DC/OS-agentnoderna och avslöjade en stabil slutpunkt vid port 9050. Om du vill komma åt tjänsten utanför DC/OS-klustret gör du följande ändringar i Azure-nätverkskonfigurationen i klusterdistributionen: 

1. **Konfigurera Azure Load Balancer** för agenter (resursen **dcos-agent-lb-xxxx)** med en hälsoavsökning och en regel för att vidarebefordra trafik på port 9050 till sava-instanserna. 

2. **Uppdatera nätverkssäkerhetsgruppen** för offentliga agenter (resursen **XXXX-agent-public-nsg-XXXX)** för att tillåta trafik på port 9050.

Detaljerade steg för att slutföra dessa uppgifter med Azure-portalen finns i [Aktivera offentlig åtkomst till ett Azure Container Service-program](container-service-enable-public-access.md). Ange port 9050 för alla portinställningar.


När allt har skapats går du till **överblickbladet** för belastningsutjämnaren DC/OS-agenten (resursen **dcos-agent-lb-xxxx**). Hitta den **offentliga IP-adressen**och använd adressen för att komma åt sava i port 9050.

![Azure portal - få offentlig IP-adress](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![Sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Kör en kanariefågel release

Anta att du har en ny version av det här programmet som du vill kanariefågel release i produktion. Du har det containerized som magneticio/sava:1.1.0 och är redo att gå. Med Vamp kan du enkelt lägga till nya tjänster i den löpande distributionen. Dessa "sammanslagna" tjänster distribueras tillsammans med befintliga tjänster i klustret och tilldelas en vikt på 0 %. Ingen trafik dirigeras till en nyligen sammanslagen tjänst förrän du justerar trafikdistributionen. Viktreglaget i Vamp UI ger dig fullständig kontroll över fördelningen, vilket möjliggör inkrementella justeringar (kanariefågel release) eller en omedelbar återställning.

### <a name="merge-a-new-service-variant"></a>Slå samman en ny tjänstvariant

Så här sammanfogar du den nya sava 1.1-tjänsten med den driftsbaserad distributionen:

1. Klicka på **Ritningar**i användargränssnittet i Vamp.

2. Klicka på **Lägg till** och klistra in i följande skiss YAML: Den här skissen beskriver en ny tjänstvariant (sava:1.1.0) som ska distribueras i det befintliga klustret (sava_cluster).

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
  
3. Klicka på **Spara**. Skissen lagras och visas på sidan **Skisser.**

4. Öppna åtgärdsmenyn på skissen sava:1.1 och klicka på **Sammanfoga till**.

   ![Vamp UI - ritningar](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Markera **sava-distributionen** och klicka på **Koppla**.

   ![Vamp UI - koppla skiss till distribution](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp distribuerar den nya sava:1.1.0-tjänstvarianten som beskrivs i skissen tillsammans med sava:1.0.0 i **sava_cluster** för den löpande distributionen. 

![Vamp UI - uppdaterad sava-distribution](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**Sava/sava_cluster/webport-gatewayen** (klusterslutpunkten) uppdateras också och en väg läggs till i den nyligen distribuerade sava:1.1.0. Vid denna punkt, ingen trafik dirigeras här **(vikt** är inställd på 0%).

![Vamp UI - klustergateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanariefågel release

Med båda versionerna av sava som distribueras i samma kluster justerar du fördelningen av trafik mellan dem genom att flytta reglaget **VIKT.**

1. Klicka ![på Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - redigera **bredvid VIKT**.

2. Ställ in viktfördelningen till 50 %/50 % och klicka på **Spara**.

   ![Vamp UI - gateway vikt skjutreglage](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Gå tillbaka till din webbläsare och uppdatera sava sidan några gånger. Sava-programmet växlar nu mellan en sava:1.0 sida och en sava:1.1 sida.

   ![alternerande sava1.0- och sava1.1-tjänster](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Denna växling av sidan fungerar bäst med "Incognito" eller "Anonym" läge i din webbläsare på grund av cachelagring av statiska tillgångar.
  >

### <a name="filter-traffic"></a>Filtrera trafik

Antag att efter distributionen att du upptäckte en inkompatibilitet i sava:1.1.0 som orsakar visningsproblem i Firefox webbläsare. Du kan ställa in Vamp för att filtrera inkommande trafik och styra alla Firefox-användare tillbaka till den kända stabila sava:1.0.0. Detta filter löser omedelbart störningar för Firefox-användare, medan alla andra fortsätter att dra nytta av den förbättrade sava:1.1.0.

Vamp använder **villkor** för att filtrera trafik mellan vägar i en gateway. Trafiken filtreras först och dirigeras enligt de villkor som gäller för varje rutt. All återstående trafik fördelas enligt inställningen för gatewayens vikt.

Du kan skapa ett villkor för att filtrera alla Firefox-användare och hänvisa dem till den gamla sava:1.0.0:

1. På sidan sava/sava_cluster/webport **Gateways** klickar ![du på Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - redigera för att lägga till ett **VILLKOR** i rutten sava/sava_cluster/sava:1.0.0/webport. 

2. Ange villkoret **användaragent ==** ![Firefox och klicka](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)på Vamp UI - spara .

   Vamp lägger till villkoret med en standardstyrka på 0%. Om du vill börja filtrera trafik måste du justera tillståndsstyrkan.

3. Klicka ![på Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - redigera om du vill ändra **styrkan** som används på villkoret.
 
4. Ställ in **STYRKAN** på 100% och klicka på ![Vamp UI - spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) för att spara.

   Vamp skickar nu all trafik som matchar villkoret (alla Firefox-användare) till sava:1.0.0.

   ![Vamp UI - tillämpa villkor för gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Slutligen justera gateway vikt för att skicka all återstående trafik (alla icke-Firefox-användare) till den nya sava:1.1.0. Klicka ![på Vamp](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) UI - redigera **bredvid VIKT** och ange viktfördelningen så att 100% riktas till rutten sava/sava_cluster/sava:1.1.0/webport.

   All trafik som inte filtreras efter villkoret dirigeras nu till den nya sava:1.1.0.

6. För att se filtret i aktion, öppna två olika webbläsare (en Firefox och en annan webbläsare) och få tillgång till sava-tjänsten från båda. Alla Firefox-förfrågningar skickas till sava:1.0.0, medan alla andra webbläsare dirigeras till sava:1.1.0.

   ![Vamp UI - filtrera trafik](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Sammanfattningsvis

Den här artikeln var en snabb introduktion till Vamp på en DC / OS kluster. Till att börja med har du Vamp igång på ditt AZURE Container Service DC/OS-kluster, distribuerade en tjänst med en Vamp-skiss och kom åt den vid den exponerade slutpunkten (gatewayen).

Vi berörde också några kraftfulla funktioner i Vamp: sammanslagning av en ny tjänstvariant till den löpande distributionen och införande av den stegvis, sedan filtrering av trafik för att lösa en känd inkompatibilitet.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att hantera Vamp åtgärder genom [Vamp REST API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Bygg Vamp automation skript i Node.js och köra dem som [Vamp arbetsflöden](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Se ytterligare [VAMP tutorials](https://docs.vamp.io/tutorials/).

