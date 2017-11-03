---
title: "Hantera nätverket grupp flöda säkerhetsloggar med Nätverksbevakaren och Grafana | Microsoft Docs"
description: "Hantera och analysera nätverket grupp flöda säkerhetsloggar i Azure med hjälp av Nätverksbevakaren och Grafana."
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Hantera och analysera Nätverkssäkerhetsgruppen flöde loggar med Nätverksbevakaren och Grafana

[Network Security Group (NSG) flöde loggar](network-watcher-nsg-flow-logging-overview.md) innehåller information som kan användas för att förstå ingående och utgående IP-trafik på nätverksgränssnitt. Loggarna Flöde Visa utgående och inkommande flöden på en per NSG regeln bas NIC flödet gäller, till 5-tuppel information om flödet (källan/målet IP-källan/målet Port Protocol), och om trafiken tillåts eller nekas.

Du kan ha många NSG: er i nätverket med flödet loggning aktiverad. Den här mängden loggningsdata är det krånglig att tolka och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa loggar för flödet av NSG med Grafana, en öppen källkod rita in verktyget, ElasticSearch, en distribuerad sökning och analytics-motorn och Logstash, vilket är en öppen källkod serversidan databearbetning pipeline.  

## <a name="scenario"></a>Scenario

NSG-flöde loggar aktiveras med hjälp av Nätverksbevakaren och lagras i Azure blob storage. Ett Logstash plugin-program används för att ansluta och bearbeta flödet loggar från blob storage och skicka dem till ElasticSearch.  När loggarna flödet lagras i ElasticSearch, de analyseras och visualiseras i anpassade instrumentpaneler i Grafana.

![NSG nätverket Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera Nätverkssäkerhetsgruppen flöde loggning

I det här scenariot måste du ha nätverket grupp flöda säkerhetsloggning aktiverat på minst en säkerhetsgrupp för nätverk i ditt konto. Instruktioner om hur du aktiverar Network säkerhetsloggar flödet finns i följande artikel [introduktion till flödet loggning för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Konfigurera överväganden

I det här exemplet konfigureras Grafana och ElasticSearch Logstash på en Ubuntu 16.04 LTS-Server som distribueras i Azure. Den här minimal installation används för att köra alla tre komponenter – de körs på samma virtuella dator. Den här installationen ska bara användas för testning och icke-kritiska arbetsbelastningar. Logstash och Elasticsearch Grafana kan alla vara konstruerad för att skala oberoende av varandra mellan många instanser. Mer information finns i dokumentationen för var och en av dessa komponenter.

### <a name="install-logstash"></a>Installera Logstash

Du kan använda Logstash för att förenkla JSON-formaterade flödet loggar till en tuppel flödet-nivå.

1. Om du vill installera Logstash, kör du följande kommandon:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurera Logstash för att parsa flödet loggarna och skicka dem till ElasticSearch. Skapa filen Logstash.conf med:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Lägg till följande innehåll i filen. Ändra lagringskontots namn och åtkomstnyckel för att återspegla information om ditt lagringskonto:

    ```bash
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

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
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
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
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

Konfigurationsfilen Logstash som består av tre delar: indata, filter och utdata. Avsnittet inkommande anger Indatakällan för de loggar som bearbetar Logstash – i det här fallet vi ska använda en ”azureblob” inkommande plugin-program (installerad i nästa steg) som gör att vi kan komma åt NSG flödet JSON loggfilerna lagras i blob storage. 

Avsnittet filter förenklar sedan varje flöde loggfil så att varje enskild flödet tuppel och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar utdata varje Logstash-händelsen till ElasticSearch-servern. Du kan ändra Logstash config-filen så att de passar dina behov.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera Logstash inkommande plugin-programmet för Azure Blob storage

Den här Logstash plugin-program kan du få direkt åtkomst till flödet loggar från deras avsedda blob storage-konto. Installera den här plugin, från Logstash Standardinstallationskatalogen (i det här fallet /usr/share/logstash/bin) kör kommandot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Läs mer om den här plugin i [Logstash inkommande plugin för Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Installera ElasticSearch

Du kan använda följande skript för att installera ElasticSearch. Information om hur du installerar ElasticSearch finns [elastisk Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Installera Grafana

Om du vill installera och köra Grafana, kör du följande kommandon:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Av ytterligare installationsinformation finns i [installerar på Debian / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Lägg till ElasticSearch-servern som en datakälla

Du måste sedan lägga till ElasticSearch indexet som innehåller flödet loggarna som en datakälla. Du kan lägga till en datakälla genom att välja **Lägg till datakälla** och fyller i formuläret med relevant information. Ett exempel på den här konfigurationen finns i följande skärmbild:

![Lägg till datakälla](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Nu när du har konfigurerat Grafana att läsa från ElasticSearch indexet som innehåller NSG flödet loggar, kan du skapa och anpassa instrumentpaneler. Om du vill skapa en ny instrumentpanel **skapar instrumentpanelen första**. Följande exempel diagrammet konfiguration visar flöden segmenterade av NSG regel:

![Instrumentpanel för diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Följande skärmbild visar ett diagram och diagram som visar de översta flödena och hur ofta. Flöden visas även av NSG regeln och flöden av beslut. Grafana är mycket anpassningsbara så är det lämpligt att du skapar instrumentpaneler så att de passar dina specifika behov för övervakning. I följande exempel visas en typisk instrumentpanel:

![Instrumentpanel för diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Slutsats

Genom att integrera Nätverksbevakaren med ElasticSearch och Grafana nu har du ett bekvämt och centraliserat sätt att hantera och visualisera NSG flödet loggar samt andra data. Grafana har ett antal andra kraftfulla diagramfunktionerna kan också användas för att hantera ytterligare flödet loggar och bättre förstå trafik på nätverket. Nu när du har en Grafana instans ställa in och ansluten till Azure kan passa på att fortsätta att utforska de funktioner som den erbjuder.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om hur du använder [Nätverksbevakaren](network-watcher-monitoring-overview.md).

