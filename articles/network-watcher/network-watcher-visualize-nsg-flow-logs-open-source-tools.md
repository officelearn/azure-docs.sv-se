---
title: Hantera och analysera flödesloggar för Nätverkssäkerhetsgruppen med Network Watcher och Elastic Stack | Microsoft Docs
description: Hantera och analysera Flow loggar för Nätverkssäkerhetsgrupper i Azure med Network Watcher och Elastic Stack.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 7361eff0f76271564fd5a0e9b8a18221ec4138e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860148"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualisera Azure Network Watcher NSG-flödesloggar med verktyg för öppen källkod

Flödesloggar för Nätverkssäkerhetsgruppen innehåller information som du kan använda förstå ingående och utgående IP-trafik på Nätverkssäkerhetsgrupper. Dessa flödesloggar visar utgående och inkommande flöden på basis av per regel, flödet som gäller för nätverkskortet, 5 tuppel information om flödet (käll-och mål IP, källa/mål-Port, protokoll), och om trafik tillåts eller nekas.

Dessa flödesloggar kan vara svårt att tolka och få insikter från manuellt. Det finns dock flera verktyg för öppen källkod som kan hjälpa dig att visualisera data. Den här artikeln ger en lösning för att visualisera dessa loggar med hjälp av Elastic Stack, som gör att du kan snabbt indexera och visualisera ditt flöde loggar in på en Kibana-instrumentpanelen.

> [!Warning]  
> Följande steg fungerar med flow loggar version 1. Mer information finns i [introduktion till flödesloggar för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfiler, utan modifiering.

## <a name="scenario"></a>Scenario

I den här artikeln ska vi konfigurera en lösning som gör att du kan visualisera flödesloggar för Nätverkssäkerhetsgruppen med Elastic Stack.  En inkommande Logstash-plugin-programmet hämtar flödesloggar direkt från storage-blob som konfigurerats för som innehåller flödesloggar. Sedan kommer använder Elastic Stack, flödesloggar vara indexerade och används för att skapa en Kibana-instrumentpanelen för att visualisera informationen.

![scenario][scenario]

## <a name="steps"></a>Steg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera Nätverkssäkerhetsgrupp flödesloggar
Det här scenariot måste du ha nätverk grupp Flow säkerhetsloggning aktiverat på minst en Nätverkssäkerhetsgrupp i ditt konto. Instruktioner om hur du aktiverar Network Security Flödesloggar finns i följande artikel [introduktion till flödesloggar för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Konfigurera Elastic Stack
Genom att ansluta NSG-flödesloggar med Elastic Stack, kan vi skapa en instrumentpanel med Kibana vad kan vi söka, diagram, analysera och slutsatser från våra loggar.

#### <a name="install-elasticsearch"></a>Installera Elasticsearch

1. Elastic Stack från version 5.0 och senare kräver Java 8. Kör kommandot `java -version` kan kontrollera din version. Om du inte har installerat java läser dokumentationen på den [Azure kodblock JDKs](https://aka.ms/azure-jdks).
2. Ladda ned rätt binära paket för ditt system:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Andra installationsmetoder finns på [Elasticsearch-Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

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

Mer information om installation elastiska search finns [Installationsinstruktioner](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Installera Logstash

1. Installera Logstash kör följande kommandon:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Därefter måste vi konfigurera Logstash för att komma åt och parsa flödesloggar. Skapa en logstash.conf filen med:

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

För ytterligare instruktioner om hur du installerar Logstash avser den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera Logstash inkommande plugin-programmet för Azure blob storage

Den här Logstash plugin-programmet kan du få direkt åtkomst till flow-loggar från deras avsedda storage-konto. Om du vill installera det här plugin-programmet kör från Logstash standardkatalogen (i det här fallet /usr/share/logstash/bin) du kommandot:

```bash
logstash-plugin install logstash-input-azureblob
```

Starta Logstash kör kommandot:

```bash
sudo /etc/init.d/logstash start
```

Mer information om det här plugin-programmet i den [dokumentation](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Install Kibana

1. Kör följande kommandon för att installera Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Om du vill köra Använd Kibana kommandon:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Om du vill visa Kibana-webbgränssnittet, gå till `http://localhost:5601`
4. Det här scenariot är mönstret index används för flödesloggar ”nsg-flödesloggar”. Du kan ändra index mönstret i avsnittet ”utdata” i filen logstash.conf.
5. Om du vill visa Kibana-instrumentpanelen via en fjärranslutning, skapar du en inkommande NSG-regel som tillåter åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en instrumentpanel i Kibana

I följande bild visas ett exempel på en instrumentpanel att visa trender och information i dina aviseringar:

![bild 1][1]

Ladda ned den [instrumentpanelsfil](https://aka.ms/networkwatchernsgflowlogdashboard), [visualisering filen](https://aka.ms/networkwatchernsgflowlogvisualizations), och [sparade sökningen filen](https://aka.ms/networkwatchernsgflowlogsearch).

Under den **Management** fliken av Kibana, gå till **sparade objekt** och importera alla tre filerna. Sedan från den **instrumentpanelen** fliken kan du öppna och läsa in exempelinstrumentpanel.

Du kan också skapa egna visualiseringar och instrumentpaneler som är skräddarsydda för din egen mätvärden. Läs mer om att skapa Kibana visualiseringar från Kibana's [officiella dokumentationen](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Visualisera flödesloggar för NSG

Exempelinstrumentpanelen innehåller flera visualiseringar av flow-loggar:

1. Flöden per beslut/riktning över tid – time series diagram som visar antalet flöden under tidsperioden. Du kan redigera tidsenheten och span båda dessa visuella objekt. Flöden per beslut visar andelen tillåter eller nekar beslut som fattas, medan flöden per riktning visas andelen av inkommande och utgående trafik. Med dessa visuella objekt kan du granska trafik trender över tid och leta efter eventuella toppar och onormala mönster.

   ![bild 2][2]

2. Flöden per mål/källport – diagram som visar fördelningen av flöden till respektive port. Med den här vyn kan du se dina vanligaste portar. Om du klickar på en specifik port i cirkeldiagrammet filtrerar resten av instrumentpanelen ned till flöden av den porten.

   ![figure3][3]

3. Antal flöden och tidigaste Loggtid – mått som visar du antalet flöden som har registrerats och datumet då den tidigaste loggen avbildas.

   ![bild 4][4]

4. Flöden per NSG och regel – ett stapeldiagram som visar fördelningen av flöden i varje NSG, samt distribution av regler i varje NSG. Härifrån kan du se vilka regler och NSG genereras mest trafik.

   ![figure5][5]

5. Främsta 10 källans/målets IP-adresser – stapeldiagram som visar översta 10 källans och målets IP-adresser. Du kan justera dessa diagram om du vill visa mer eller mindre främsta IP-adresser. Härifrån kan du se de vanligaste inträffar IP-adresser samt beslutet trafik (Tillåt eller neka) som görs för varje IP.

   ![figure6][6]

6. Flow Tupplar – den här tabellen visar du informationen i varje flöde tuppel samt dess motsvarande redigera och regeln.

   ![figure7][7]

Med fältet fråga överst på instrumentpanelen kan filtrera du ned instrumentpanelen med någon parameter av de, till exempel prenumerations-ID, resursgrupper, regel eller alla andra variabler av intresse. Mer information om Kibanas frågor och filter som avser den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Sammanfattning

Genom att kombinera flödesloggar för Nätverkssäkerhetsgruppen med Elastic Stack, har vi kom fram till kraftfull och anpassningsbar sätt att visualisera vår nätverkstrafik. Dessa instrumentpaneler kan du snabbt få och dela information om nätverkstrafik, samt filtrera ned och undersöka om alla potentiella avvikelser. Med Kibana kan du skräddarsy dessa instrumentpaneler och skapa specifika visualiseringar för att uppfylla alla behov för säkerhet, granskning och efterlevnad.

## <a name="next-steps"></a>Nästa steg

Lär dig att visualisera dina NSG-flödesloggar med Power BI genom att besöka [visualisera NSG-flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
