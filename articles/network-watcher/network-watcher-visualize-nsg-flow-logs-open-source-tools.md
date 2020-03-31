---
title: Visualisera NSG-flödesloggar - Elastisk stack
titleSuffix: Azure Network Watcher
description: Hantera och analysera flödesloggar för nätverkssäkerhetsgrupper i Azure med nätverksbevakare och elastisk stack.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e567994038fb4f71ef86dc577760ecf4699a0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840646"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualisera NSG-flödesloggar från Azure Network Watcher med hjälp av verktyg med öppen källkod

Nätverkssäkerhetsgruppsflödesloggar innehåller information som kan användas för att förstå inkommande och utgående IP-trafik i nätverkssäkerhetsgrupper. Dessa flödesloggar visar utgående och inkommande flöden per regel, nätverkskortet som flödet gäller för, 5 tuppelinformation om flödet (Source/Destination IP, Source/Destination Port, Protocol) och om trafiken tilläts eller nekades.

Dessa flödesloggar kan vara svåra att manuellt tolka och få insikter från. Det finns dock flera verktyg med öppen källkod som kan hjälpa till att visualisera dessa data. Den här artikeln kommer att ge en lösning för att visualisera dessa loggar med hjälp av elastisk stack, vilket gör att du snabbt indexera och visualisera dina flödesloggar på en Kibana instrumentpanel.

> [!Warning]  
> Följande steg fungerar med flödesloggar version 1. Mer information finns i [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfilerna, utan ändringar.

## <a name="scenario"></a>Scenario

I den här artikeln kommer vi att ställa in en lösning som gör att du kan visualisera Network Security Group flödesloggar med hjälp av elastisk stack.  En Logstash indata plugin kommer att få flödesloggar direkt från lagring blob konfigureras för att innehålla flödesloggar. Med hjälp av den elastiska stacken indexeras flödesloggarna sedan och används för att skapa en Kibana-instrumentpanel för att visualisera informationen.

![scenario][scenario]

## <a name="steps"></a>Steg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera flödesloggning för nätverkssäkerhetsgruppen
I det här fallet måste du ha nätverkssäkerhetsgruppflödesloggning aktiverat på minst en nätverkssäkerhetsgrupp i ditt konto. Instruktioner om hur du aktiverar nätverkssäkerhetsflödesloggar finns i följande artikel [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Ställ in den elastiska stacken
Genom att ansluta NSG-flödesloggar med Elastic Stack kan vi skapa en Kibana-instrumentpanel som gör det möjligt för oss att söka, grafera, analysera och härleda insikter från våra loggar.

#### <a name="install-elasticsearch"></a>Installera Elasticsearch

1. Den elastiska stacken från version 5.0 och högre kräver Java 8. Kör kommandot `java -version` för att kontrollera din version. Om du inte har java installerat läser du dokumentation om [Azure-suppored JDK: er](https://aka.ms/azure-jdks).
2. Ladda ner rätt binära paket för ditt system:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Andra installationsmetoder finns på [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Kontrollera att Elasticsearch körs med kommandot:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Du bör se ett svar som liknar detta:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Mer information om hur du installerar elastisk sökning finns i [installationsinstruktioner](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Installera Logstash

1. Så här installerar du Logstash och kör följande kommandon:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Nästa vi behöver konfigurera Logstash att komma åt och tolka flödesloggar. Skapa en logstash.conf-fil med:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Lägg till följande innehåll i filen:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

För ytterligare instruktioner om hur du installerar Logstash, se den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera indataindataindataindataindatafilen logstash för Azure-bloblagring

Denna Logstash plugin gör att du kan direkt komma åt flödesloggar från deras utsedda lagringskonto. För att installera detta plugin, från standard Logstash installationskatalog (i detta fall / usr / share/logstash / bin) kör kommandot:

```bash
logstash-plugin install logstash-input-azureblob
```

Så här startar du Kommandot Logstash:

```bash
sudo /etc/init.d/logstash start
```

Mer information om detta program finns i [dokumentationen](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Installera Kibana

1. Kör följande kommandon för att installera Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Så här kör du Kibana med kommandona:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Om du vill visa ditt Kibana-webbgränssnitt navigerar du till`http://localhost:5601`
4. I det här fallet är indexmönstret som används för flödesloggarna "nsg-flow-logs". Du kan ändra indexmönstret i avsnittet "utdata" i filen logstash.conf.
5. Om du vill visa Kibana-instrumentpanelen på distans skapar du en inkommande NSG-regel som tillåter åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en Kibana-instrumentpanel

En exempelinstrumentpanel för att visa trender och information i dina aviseringar visas i följande bild:

![bild 1][1]

Hämta [instrumentpanelsfilen,](https://aka.ms/networkwatchernsgflowlogdashboard) [visualiseringsfilen](https://aka.ms/networkwatchernsgflowlogvisualizations)och den [sparade sökfilen](https://aka.ms/networkwatchernsgflowlogsearch).

Under fliken **Hantering** i Kibana navigerar du till **Sparade objekt** och importerar alla tre filerna. Sedan kan du öppna och läsa in exempelinstrumentpanelen från fliken **Instrumentpanel.**

Du kan också skapa egna visualiseringar och instrumentpaneler som är skräddarsydda för mätvärden av ditt eget intresse. Läs mer om hur du skapar Kibana-visualiseringar från Kibanas [officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Visualisera NSG-flödesloggar

Exempelinstrumentpanelen innehåller flera visualiseringar av flödesloggarna:

1. Flöden efter beslut/riktning över tid - tidsseriediagram som visar antalet flöden under tidsperioden. Du kan redigera enheten för tid och spännvidd för båda dessa visualiseringar. Flöden per beslut visar andelen tillåta eller neka beslut som fattats, medan Flöden efter riktning visar andelen inkommande och utgående trafik. Med dessa visuella objekt kan du undersöka trafiktrender över tid och leta efter eventuella spikar eller ovanliga mönster.

   ![figur2][2]

2. Flöden efter mål-/källport – cirkeldiagram som visar fördelningen av flöden till sina respektive portar. Med den här vyn kan du se dina vanligaste portar. Om du klickar på en viss port i cirkeldiagrammet filtreras resten av instrumentpanelen ned till flöden av porten.

   ![figur3][3]

3. Antal flöden och tidigaste loggtid – mått som visar antalet registrerade flöden och datumet för den tidigaste loggen som fångas in.

   ![figur4][4]

4. Flöden efter NSG och Regel – ett stapeldiagram som visar fördelningen av flöden inom varje NSG, samt fördelningen av regler inom varje NSG. Härifrån kan du se vilka NSG och regler som genererade mest trafik.

   ![figur5][5]

5. Topp 10 Source/Destination IP-adresser – stapeldiagram som visar de 10 främsta käll- och mål-IP-adresserna. Du kan justera dessa diagram så att de visar mer eller mindre topp-IPs. Härifrån kan du se de vanligaste förekommande IP-adresser samt trafikbeslut (tillåta eller neka) görs mot varje IP.

   ![figur6][6]

6. Flödesupplar – den här tabellen visar informationen i varje flödesupptuppning, samt motsvarande NGS och regel.

   ![figur7][7]

Med frågefältet högst upp på instrumentpanelen kan du filtrera ned instrumentpanelen baserat på valfri parameter i flödena, till exempel prenumerations-ID, resursgrupper, regel eller någon annan intressevariabel. Mer information om Kibanas frågor och filter finns i den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Slutsats

Genom att kombinera nätverkssäkerhetsgruppens flödesloggar med elastic stacken har vi kommit på ett kraftfullt och anpassningsbart sätt att visualisera vår nätverkstrafik. Med de här instrumentpanelerna kan du snabbt få och dela insikter om nätverkstrafiken samt filtrera nedåt och undersöka eventuella avvikelser. Med Kibana kan du skräddarsy dessa instrumentpaneler och skapa specifika visualiseringar för att uppfylla alla säkerhets-, gransknings- och efterlevnadsbehov.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualiserar dina NSG-flödesloggar med Power BI genom att besöka [Visualize NSG-flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
