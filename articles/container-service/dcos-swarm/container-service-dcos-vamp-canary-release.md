---
title: "Kanarieöarna version med Vamp på Azure DC/OS-kluster | Microsoft Docs"
description: "Hur du använder Vamp till Kanarieöarna versionen tjänster och tillämpa smart trafik filtrering på ett Azure Container Service DC/OS-kluster"
services: container-service
author: gggina
manager: rasquill
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 4a20091b59f2643ea71cce99c159a5075706e35d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>Kanarieöarna versionen mikrotjänster med Vamp på ett Azure Container Service DC/OS-kluster

I den här genomgången ska konfigurera vi Vamp på Azure Container Service med ett DC/OS-kluster. Vi Kanarieöarna släpp Vamp demo tjänsten ”sava” och sedan matcha inkompatibilitet med Firefox genom att använda smart trafikfiltrering. 

> [!TIP] 
> I den här genomgången Vamp körs på en DC/OS-klustret, men du kan också använda Vamp med Kubernetes som orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Om Kanarieöarna versioner och Vamp


[Kanarieöarna släppa](https://martinfowler.com/bliki/CanaryRelease.html) är en smart distributionsstrategi antas av innovativa organisationer som Netflix, Facebook och Spotify. Det är en lösning som passar eftersom det minskar problem, introducerar säkerhet nät och ökar innovation. Så varför inte alla företag som använder den? Utöka en CI/CD-pipeline för att inkludera Kanarieöarna strategier lägger till komplexitet och kräver omfattande devops kunskap och erfarenhet. Det är tillräckligt för att blockera mindre företag och företag som är både innan de även komma igång. 

[Vamp](http://vamp.io/) lanserar ett system för öppen källkod som utformats för att underlätta övergången och sätta Kanarieöarna funktioner till din önskade behållaren Schemaläggaren. Vamps Kanarieöarna funktioner går utöver procent-baserade installationer. Trafik kan filtreras och dela på en mängd olika villkor, till exempel till målet specifika användare, IP-adressintervall eller enheter. Vamp spårar och analyserar prestandamått, så att för automatisering som bygger på verkliga data. Du kan ställa in automatisk återställning vid fel eller skala enskild tjänst varianter utifrån belastningen eller svarstid.

## <a name="set-up-azure-container-service-with-dcos"></a>Konfigurera Azure Container Service med DC/OS



1. [Distribuera ett DC/OS-kluster](container-service-deployment.md) med en överordnad och två agenterna av standardstorleken. 

2. [Skapa en SSH-tunnel](../container-service-connect.md) att ansluta till DC/OS-klustret. Den här artikeln förutsätter att du tunnel till klustret på lokal port 80.


## <a name="set-up-vamp"></a>Ställ in Vamp

Nu när du har ett DC/OS-kluster som körs kan du installera Vamp från DC/OS-Gränssnittet (http://localhost:80). 

![DC/OS-gränssnitt:](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Installationen sker i två steg:

1. **Distribuera Elasticsearch**.

2. Sedan **distribuera Vamp** genom att installera paketet Vamp DC/OS universe.

### <a name="deploy-elasticsearch"></a>Distribuera Elasticsearch

Vamp kräver Elasticsearch för insamling av mätvärden och aggregering. Du kan använda den [magneticio Docker bilder](https://hub.docker.com/r/magneticio/elastic/) att distribuera en kompatibel Vamp Elasticsearch stack.

1. I DC/OS-Gränssnittet går du till **Services** och på **distribuera tjänst**.

2. Välj **JSON-läget** från den **distribuera nya tjänster** popup.

  ![Välj JSON-läge](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Klistra in följande JSON. Den här konfigurationen kör behållaren med 1 GB RAM-minne och grundläggande hälsokontrollen på Elasticsearch port.
  
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

  DC/OS distribuerar Elasticsearch behållaren. Du kan följa förloppet på den **Services** sidan.  

  ![distribuera e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Distribuera Vamp

När Elasticsearch rapporterar som **kör**, du kan lägga till Universe Vamp DC/OS-paketet. 

1. Gå till **Universe** och Sök efter **vamp**. 
  ![Vamp på DC/OS universe](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Klicka på **installera** bredvid vamp paketet och välj **Installation i Avancerat**.

3. Bläddra nedåt och ange följande elasticsearch-url: `http://elasticsearch.marathon.mesos:9200`. 

  ![Ange Elasticsearch URL](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Klicka på **granska och installera**, klicka på **installera** att starta distributionen.  

  DC/OS distribuerar alla nödvändiga komponenter för Vamp. Du kan följa förloppet på den **Services** sidan.
  
  ![Distribuera Vamp som universe paket](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. När distributionen är klar kan du komma åt Vamp-Användargränssnittet:

  ![Vamp-tjänsten på DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Distribuera din första tjänst

Nu att Vamp är igång kan du distribuera en tjänst från ett utkast. 

I sin enklaste form är en [Vamp modell](http://vamp.io/documentation/using-vamp/blueprints/) beskriver slutpunkter (gateway), kluster och tjänster för att distribuera. Vamp använder kluster för att gruppera olika varianter av samma tjänst i logiska grupper Kanarieöarna släppa eller A / B-testning.  

Det här scenariot använder ett monolitisk exempelprogram som kallas [ **sava**](https://github.com/magneticio/sava), vilket är version 1.0. Monolitvolym paketeras i en dockerbehållare med som finns i Docker under magneticio/sava:1.0.0. Appen körs normalt på port 8080, men du vill exponera under port 9050 i det här fallet. Distribuera appen via Vamp med en enkel modell.

1. Gå till **distributioner**.

2. Klicka på **Lägg till**.

3. Klistra in i den följande modell YAML. Det här utkastet innehåller ett kluster med en enda service variant som vi ändras i ett senare steg:

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

Distributionen finns på den **distributioner** sidan. Klicka om du vill övervaka statusen för distributionen.

![Vamp gränssnitt – distribuera sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![sava service i Användargränssnittet för Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Två gateways skapas, som visas på den **Gateways** sidan:

* en stabil slutpunkter för åtkomst till tjänsten körs (9050-port) 
* en Vamp hanteras internt gateway (mer om den här gatewayen senare). 

![Vamp UI - sava gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Tjänsten sava nu har distribuerats, men du kan inte komma åt den externt eftersom Azure belastningsutjämnare inte vet för att vidarebefordra trafik till det ännu. Uppdatera Azure nätverkskonfigurationen för att komma åt tjänsten.


## <a name="update-the-azure-network-configuration"></a>Uppdatera konfigurationen av Azure-nätverk

Vamp distribuerade tjänsten sava på DC/OS-agenten noder, exponera en stabil slutpunkt på port 9050. Gör följande ändringar i konfigurationen för Azure-nätverk i klustret distributionen för att komma åt tjänsten från utanför DC/OS-klustret: 

1. **Konfigurera Azure belastningsutjämnare** för agenter (resursen med namnet **DC/OS-agent-lb-xxxx**) med en hälsoavsökningen och en regel för att vidarebefordra trafik på port 9050 till sava-instanser. 

2. **Uppdatera nätverkssäkerhetsgruppen** för offentliga agenter (resursen med namnet **XXXX-agent-offentliga-nsg-XXXX**) som tillåter trafik på port 9050.

Detaljerade steg för att utföra dessa uppgifter med hjälp av Azure portal, finns [aktivera offentlig åtkomst till ett Azure Container Service-program](container-service-enable-public-access.md). Ange port 9050 för alla portinställningar.


När allt har skapats, gå till den **översikt** bladet för DC/OS-agentens belastningsutjämnare (resursen med namnet **DC/OS-agent-lb-xxxx**). Hitta de **offentliga IP-adressen**, och Använd adress för att komma åt sava på port 9050.

![Azure portal – hämta offentlig IP-adress](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Kör en version av Kanarieöarna

Anta att du har en ny version av det här programmet som du vill att Kanarieöarna ut i produktion. Du har den behållare som magneticio/sava:1.1.0 och är redo att sätta igång. Vamp kan du enkelt lägga till nya tjänster körs distributionen. Tjänsterna ”kopplade” distribuerats tillsammans med befintliga tjänster i klustret, och tilldelas en vikt på 0%. Ingen trafik dirigeras till en nyligen kopplade tjänst förrän du justera trafik distribution. Skjutreglaget vikt i Användargränssnittet för Vamp ger fullständig kontroll över distributionen för inkrementell justeringar (Kanarieöarna utgåva) eller en omedelbar återställning.

### <a name="merge-a-new-service-variant"></a>Koppla en ny service-variant

Att sammanfoga den nya sava 1.1-tjänsten med körs distributionen:

1. I Användargränssnittet för Vamp klickar du på **ritningarna**.

2. Klicka på **Lägg till** och klistra in i den följande modell YAML: den här utkast som beskriver en ny service-variant (sava: 1.1.0) ska distribueras i det befintliga klustret (sava_cluster).

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
  
3. Klicka på **Spara**. Modell lagras och visas på den **ritningarna** sidan.

4. Öppna åtgärdsmenyn på den sava: 1.1-modell och klicka på **koppla till**.

  ![Vamp UI - ritningarna](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Välj den **sava** distribution och klickar på **sammanfoga**.

  ![Vamp UI - merge modell som distributionen](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp distribuerar den nya sava: 1.1.0 service varianten som beskrivs i modell tillsammans med sava: 1.0.0 i den **sava_cluster** för distributionen körs. 

![Vamp UI - uppdaterade sava distribution](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

Den **webport-sava/sava_cluster** gateway (klusterslutpunkten) uppdateras även att lägga till en väg till nyligen distribuerade sava: 1.1.0. Nu ingen trafik dirigeras här (den **vikt** anges till 0%).

![Vamp UI - klustret gateway](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Kanarieöarna versionen

Med båda versionerna av sava som distribueras i samma kluster, justera fördelningen av trafiken mellan dem genom att flytta den **vikt** skjutreglaget.

1. Klicka på ![Vamp UI - redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) bredvid **vikt**.

2. Ange viktfördelningen till 50/50% och klicka på **spara**.

  ![Vamp UI - gateway vikt skjutreglaget](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Gå tillbaka till din webbläsare och uppdatera sidan sava några gånger. Sava programmet nu växlar mellan en sava: 1.0 och en sava: 1.1-sidan.

  ![Alternerande sava1.0 och sava1.1 tjänster](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Den här växlingar på sidan fungerar bäst med ”Incognito” eller ”Anonymous” läge i webbläsaren på grund av cachelagring av statiska tillgångar.
  >

### <a name="filter-traffic"></a>Filtrera trafik

Anta att efter distributionen du identifierade inkompatibilitet i sava: 1.1.0 att visas i Firefox webbläsare. Du kan ange Vamp att filtrera inkommande trafik och dirigera alla Firefox användare tillbaka till kända stabil sava: 1.0.0. Det här filtret löser omedelbart avbrott för Firefox användare, medan alla andra fortsätter att dra nytta av fördelarna med förbättrad sava: 1.1.0.

Vamp använder **villkor** att filtrera trafik mellan vägar i en gateway. Trafiken först filtreras och dirigeras enligt villkor som används för varje flöde. Alla återstående trafiken fördelas enligt inställningen gateway vikt.

Du kan skapa ett villkor för att filtrera alla Firefox användare och dirigera dem till den gamla sava: 1.0.0:

1. På sava/sava_cluster/webport **Gateways** klickar du på ![Vamp UI - redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) att lägga till en **VILLKORET** till väg sava/sava_cluster/sava:1.0.0/webport. 

2. Ange villkoret **användaragent == Firefox** och på ![Vamp UI - spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp lägger till villkor med en standardvärdet 0%. Om du vill starta filtrerar trafik, måste du justera styrkan villkor.

3. Klicka på ![Vamp UI - redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) att ändra den **styrkan** tillämpas på villkoret.
 
4. Ange den **styrkan** till 100% och klicka på ![Vamp UI - spara](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) att spara.

  Vamp nu skickar all trafik som matchar villkoret (alla Firefox användare) till sava: 1.0.0.

  ![Vamp UI - gäller villkor för gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Slutligen justera gateway vikt för att skicka alla återstående trafik (alla användare med icke-Firefox) till den nya sava: 1.1.0. Klicka på ![Vamp UI - redigera](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) bredvid **vikt** och ange viktfördelningen så dirigeras till väg sava/sava_cluster/sava:1.1.0/webport 100%.

  All trafik som inte filtreras efter villkoret omdirigeras nu till den nya sava: 1.1.0.

6. Öppna två olika webbläsare (en Firefox och en annan webbläsare) och tjänsten sava från både om du vill visa filter i åtgärden. Alla Firefox begäranden skickas till sava: 1.0.0, medan alla andra webbläsare dirigeras till sava: 1.1.0.

  ![Vamp UI - filtrera trafik](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Sammanfattningsvis

Den här artikeln är en snabb introduktion till Vamp på ett DC/OS-kluster. Börja du har fått Vamp och körs på din Azure Container Service DC/OS-kluster distribuerat en tjänst med en Vamp modell och används på exponerade slutpunkten (gateway).

Vi också har använts på vissa kraftfulla funktioner i Vamp: Koppla en ny service variant-distributionen körs och introduktion till inkrementellt sedan filtrerar trafik för att lösa kända kompatibilitetsproblem.


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du hanterar Vamp åtgärder via den [Vamp REST API](http://vamp.io/documentation/api/api-reference/).

* Skapa Vamp automatiseringsskript i Node.js och köra dem som [Vamp arbetsflöden](http://vamp.io/documentation/tutorials/create-a-workflow/).

* Se ytterligare [VAMP självstudier](http://vamp.io/documentation/tutorials/overview/).

