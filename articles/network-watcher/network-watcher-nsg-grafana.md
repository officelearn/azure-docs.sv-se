---
title: Hantera Flow loggar för Nätverkssäkerhetsgrupper med Network Watcher och Grafana | Microsoft Docs
description: Hantera och analysera Flow loggar för Nätverkssäkerhetsgrupper i Azure med Network Watcher och Grafana.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: e375476536e7fe150e3aabcae7cee942deac02d5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42060338"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Hantera och analysera flödesloggar för Nätverkssäkerhetsgruppen med Network Watcher och Grafana

[Network Security Group (NSG) flödesloggar](network-watcher-nsg-flow-logging-overview.md) innehåller information som kan användas för att förstå ingående och utgående IP-trafik på nätverksgränssnitt. Dessa flödesloggar Visa utgående och inkommande flöden på en per NSG-regel-basis nätverkskortet flödet gäller, 5-tuppel information om flödet (källa/mål-IP, källa/mål-Port, Protocol), och om trafik tillåts eller nekas.

Du kan ha många NSG: er i nätverket med flow-loggning är aktiverat. Den här mängden loggningsdata är det besvärligt att tolka och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa NSG-flödesloggar med Grafana, ett rita in verktyget, ElasticSearch, en distribuerad sökning och Analysmotorn och Logstash, vilket är en öppen källkod från serversidan databearbetningspipeline med öppen källkod.  

## <a name="scenario"></a>Scenario

NSG-flödesloggar aktiveras med hjälp av Network Watcher och lagras i Azure blob storage. En Logstash plugin-programmet används för att ansluta och bearbeta flödesloggar från blob storage och skicka dem till ElasticSearch.  När flödesloggar lagras i ElasticSearch, kan de analyseras och visualiseras med anpassade instrumentpaneler i Grafana.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera Nätverkssäkerhetsgrupp flödesloggar

Det här scenariot måste du ha nätverk grupp Flow säkerhetsloggning aktiverat på minst en Nätverkssäkerhetsgrupp i ditt konto. Instruktioner om hur du aktiverar Network Security Flödesloggar finns i följande artikel [introduktion till flödesloggar för Nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Tänka på vid konfiguration

I det här exemplet konfigureras Grafana, ElasticSearch och Logstash på en Ubuntu 16.04 LTS-Server som distribueras i Azure. Den här minimal installation används för att köra alla tre komponenter – de körs på samma virtuella dator. Den här konfigurationen rekommenderas endast för testning och icke-kritiska arbetsbelastningar. Logstash, Elasticsearch och Grafana kan alla vara byggts för att skala oberoende av varandra över många instanser. Mer information finns i dokumentationen för var och en av dessa komponenter.

### <a name="install-logstash"></a>Installera Logstash

Du kan använda Logstash för att platta ut JSON-formaterade flödesloggar till en flow-tuppel-nivå.

1. För att installera Logstash, kör du följande kommandon:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurera Logstash för att parsa flödesloggar och skicka dem till ElasticSearch. Skapa en Logstash.conf filen med:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Lägg till följande innehåll i filen. Ändra namn och lagringskontonyckeln för att återspegla din kontoinformation för lagring:

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

Logstash config-fil som består av tre delar: indata, filter och utdata.
Avsnittet inkommande betyder Indatakällan för loggarna som bearbetar Logstash – i det här fallet vi ska använda en ”azureblob” indata-plugin-programmet (installerad i nästa steg) som gör det möjligt för oss att få åtkomst till NSG flow JSON loggfilerna lagras i blob storage. 

Avsnittet filter förenklar sedan loggfilerna flöde så att varje tuppel enskilda flödet och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar utdata varje Logstash-händelse till ElasticSearch-servern. Passa på att ändra Logstash config-fil så att den passar dina behov.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera Logstash inkommande plugin-programmet för Azure Blob storage

Den här Logstash plugin-programmet kan du direkt åtkomst till flow-loggar från deras avsedda blob storage-konto. Kör kommandot för att installera det här plugin-programmet, från Logstash standardinstallationen katalogen (i det här fallet /usr/share/logstash/bin):

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om det här plugin-programmet finns i [Logstash inkommande plugin-program för Azure Storage-Blobbar](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Installera ElasticSearch

Du kan använda följande skript för att installera ElasticSearch. Information om hur du installerar ElasticSearch finns i [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

Du måste sedan lägga till ElasticSearch indexet som innehåller flödesloggar som en datakälla. Du kan lägga till en datakälla genom att välja **Lägg till datakälla** och fyller i formuläret med relevant information. Ett exempel på den här konfigurationen finns i följande skärmbild:

![Lägg till datakälla](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Nu när du har konfigurerat Grafana att läsa från ElasticSearch indexet som innehåller NSG-flödesloggar kan du skapa och anpassa instrumentpaneler. Om du vill skapa en ny instrumentpanel, Välj **skapa din första instrumentpanel**. Följande exempelkonfiguration för diagrammet visar flöden uppdelat efter NSG-regel:

![Instrumentpanelen graph](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Följande skärmbild visar ett diagram och diagram som visar de översta flödena och ofta. Flöden visas även av NSG-regel och flöden per beslut. Grafana är mycket anpassningsbara så du rekommenderas att du skapar instrumentpaneler så att den passar dina specifika övervakningsbehov. I följande exempel visas en typisk instrumentpanel:

![Instrumentpanelen graph](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Sammanfattning

Genom att integrera Network Watcher med ElasticSearch och Grafana, nu har du ett praktiskt och centraliserad sätt att hantera och visualisera NSG-flödesloggar samt andra data. Grafana har ett antal andra kraftfulla diagramfunktionerna som också kan användas för att hantera ytterligare flödesloggar och bättre förstå din nätverkstrafik. Passa på att fortsätta att utforska andra funktioner som den erbjuder nu när du har en Grafana-instans ställa in och anslutit till Azure.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du använder [Network Watcher](network-watcher-monitoring-overview.md).

