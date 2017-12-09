---
title: "Visualisera Azure Network Watcher NSG flödet loggar med öppen källkod verktyg | Microsoft Docs"
description: "Den här sidan beskriver hur du använder verktyg med öppen källkod visualisera NSG flödet loggar."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8b313b68be07da1a943748d21da68c169980cfc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualisera Azure Network Watcher NSG flödet loggar med öppen källkod verktyg

Nätverkssäkerhetsgruppen flöde loggarna ger information som kan användas för förstå ingående och utgående IP-trafik på Nätverkssäkerhetsgrupper. Loggarna flödet visar utgående och inkommande flöden på grundval av per regel, NIC flödet gäller, 5 tuppel information om flödet (källan/målet IP, källan/målet Port Protocol), och om trafiken tillåts eller nekas.

Loggarna flödet kan vara svårt att tolka och få insikter från manuellt. Det finns dock flera öppen källkod verktyg som hjälper dig att visualisera informationen. Den här artikeln får du en lösning för att visualisera dessa loggar med elastisk stacken, vilket innebär att du snabbt index och visualisera dina flödet loggar på en instrumentpanel med Kibana.

## <a name="scenario"></a>Scenario

I den här artikeln ska vi ställa in en lösning som gör att du kan visualisera Nätverkssäkerhetsgruppen flöde loggar med elastisk stacken.  Ett Logstash inkommande plugin-program hämtar flödet loggarna direkt från lagringsblob som konfigurerats för som innehåller loggarna flödet. Sedan kommer använder elastisk stacken flödet loggarna indexeras och används för att skapa en Kibana instrumentpanel om du vill visualisera informationen.

![scenario][scenario]

## <a name="steps"></a>Steg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera Nätverkssäkerhetsgruppen flöde loggning
I det här scenariot måste du ha nätverket grupp flöda säkerhetsloggning aktiverat på minst en säkerhetsgrupp för nätverk i ditt konto. Instruktioner om hur du aktiverar Network säkerhetsloggar flödet finns i följande artikel [introduktion till flödet loggning för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).


### <a name="set-up-the-elastic-stack"></a>Konfigurera elastisk Stack
Genom att ansluta NSG flödet loggar med elastisk Stack kan skapa vi en instrumentpanel för Kibana vad gör att vi kan söka, diagram, analysera och härleda insikter från våra loggar.

#### <a name="install-elasticsearch"></a>Installera Elasticsearch

1. Elastisk stacken version 5.0 och senare kräver Java 8. Kör kommandot `java -version` att kontrollera din version. Om du inte har java installera, finns i dokumentationen till på [Oracles webbplats](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Hämta rätt binära paket för ditt system:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Andra installationsmetoder finns på [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Kontrollera att Elasticsearch körs med kommandot:

    ```
    curl http://127.0.0.1:9200
    ```

    Du bör se ett svar som liknar detta:

    ```
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

Ytterligare information om installation elastisk Sök hänvisar till sidan [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Installera Logstash

1. Installera Logstash kör följande kommandon:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Nästa måste vi du konfigurera Logstash för åtkomst och parsa flödet loggarna. Skapa filen logstash.conf med:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Lägg till följande innehåll i filen:

  ```
input {
   azureblob
     {
         storage_account_name => "mystorageaccount"
         storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
         container => "insights-logs-networksecuritygroupflowevent"
         codec => "json"
         # Refer https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-read-nsg-flow-logs
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

Ytterligare instruktioner om hur du installerar Logstash finns i den [officiella dokumentation](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera Logstash inkommande plugin-programmet för Azure-blobblagring

Den här Logstash plugin-program kan du få direkt åtkomst till flödet loggar från deras avsedda storage-konto. Om du vill installera den här plugin-programmet kör från Standardinstallationskatalogen Logstash (i det här fallet /usr/share/logstash/bin) du kommandot:

```
logstash-plugin install logstash-input-azureblob
```

Om du vill starta Logstash köra kommandot:

```
sudo /etc/init.d/logstash start
```

Mer information om den här plugin-program finns i dokumentationen [här](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="install-kibana"></a>Installera Kibana

1. Kör följande kommandon för att installera Kibana:

  ```
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. Om du vill köra Använd Kibana kommandon:

  ```
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. Om du vill visa dina Kibana webbgränssnitt, gå till`http://localhost:5601`
1. I det här scenariot är index mönstret som används för flödet loggar ”nsg-flöde-logs”. Du kan ändra indexet mönster i avsnittet ”utdata” i filen logstash.conf.

1. Om du vill visa infopanelen Kibana via fjärranslutning, skapar du en inkommande NSG regel för att tillåta åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en Kibana instrumentpanel

Vi har angett ett exempel på en instrumentpanel där du kan visa trender och information i aviseringar för den här artikeln.

![bild 1][1]

1. Hämta filen instrumentpanelen [här](https://aka.ms/networkwatchernsgflowlogdashboard), visualisering filen [här](https://aka.ms/networkwatchernsgflowlogvisualizations), och filen sparad sökning [här](https://aka.ms/networkwatchernsgflowlogsearch).

1. Under den **Management** fliken av Kibana, gå till **sparade objekt** och importera alla tre filerna. Sedan från den **instrumentpanelen** fliken kan du öppna och läsa in de exempel på en instrumentpanelen.

Du kan också skapa egna visualiseringar och instrumentpaneler som skräddarsys mot mätvärden för ditt eget intresse. Läs mer om hur du skapar Kibana visualiseringar från Kibana's [officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>Visualisera flödet NSG-loggar

I exemplet på instrumentpanel innehåller flera visualiseringar flödet loggar:

1. Flöden av beslut/riktning över tid - tid serien diagram som visar antal flöden för tidsperioden. Du kan redigera tidsenheten och tidsrymden för båda dessa visualiseringar. Flöden genom beslut visar andelen tillåta eller neka som måste fattas medan flöden av riktning visas andelen inkommande och utgående trafik. Med dessa visuell information du undersöka trender trafik över tid och leta efter alla toppar eller ovanliga mönster.

  ![bild 2][2]

1. Flöden av målservern/källport – cirkeldiagram visar fördelningen av flödar till respektive port. Med den här vyn kan du se dina mest använda portar. Om du klickar på en specifik port i cirkeldiagrammet filtrerar resten av instrumentpanelen till flödet av denna port.

  ![figure3][3]

1. Antal flöden och tidigaste Loggtid – mått som visar du antal flöden registreras och datumet för den tidigaste loggen avbildas.

  ![bild 4][4]

1. Flöden av NSG och regel – ett stapeldiagram som visar fördelningen av flöden inom varje NSG, samt distribution av regler inom varje NSG. Du kan se vilka NSG och regler genereras mest trafik från den här.

  ![figure5][5]

1. Främsta 10 källan/målet IP-adresser – stapeldiagram som visar upp 10 käll- och IP-adresser. Du kan justera dessa diagram om du vill visa mer eller mindre översta IP-adresser. Härifrån kan du se de vanligaste uppstår IP-adresser samt trafik beslut (Tillåt eller neka) som görs mot varje IP.

  ![figure6][6]

1. Flödet Tupplar – den här tabellen visar du informationen i varje flöde tuppel samt dess motsvarande NGS och regeln.

  ![figure7][7]

Med hjälp av fältet fråga överst på instrumentpanelen kan du filtrera ned instrumentpanelen med någon parameter av flöden, till exempel prenumerations-ID, resursgrupper, regel eller alla andra variabler av intresse. Mer information om Kibanas frågor och filter som avser den [officiella dokumentation](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Slutsats

Genom att kombinera Nätverkssäkerhetsgruppen flöde loggar med elastisk Stack, har vi kom fram till kraftfulla och anpassningsbara sätt att visualisera våra nätverkstrafik. Instrumentpanelerna kan du snabbt få och dela information om nätverkstrafik samt filter ned och undersöka på alla potentiella avvikelser. Med Kibana kan du anpassa instrumentpanelerna och skapa specifika grafik som uppfyller behoven för alla säkerhets-, gransknings- och kompatibilitet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualisera dina NSG flödet loggar med Power BI genom att besöka [visualisera NSG flödar loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
