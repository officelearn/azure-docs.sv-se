---
title: Hantera NSG-flödesloggar med Grafana
titleSuffix: Azure Network Watcher
description: Hantera och analysera flödesloggar för nätverkssäkerhetsgrupper i Azure med Network Watcher och Grafana.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: c48d5a02cdb8ef63904642c6c2c76cb5d61e1f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840918"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Hantera och analysera flödesloggar för Nätverkssäkerhetsgruppen med Network Watcher och Grafana

[NSG-flödesloggar (Network Security Group)](network-watcher-nsg-flow-logging-overview.md) innehåller information som kan användas för att förstå inkommande och utgående IP-trafik i nätverksgränssnitt. Dessa flödesloggar visar utgående och inkommande flöden enligt NSG-regelbasen, nätverkskortet som flödet gäller för, 5-tuppelinformation om flödet (Käll-/mål-IP, Källa/målport, protokoll) och om trafiken tilläts eller nekades.

> [!Warning]  
> Följande steg fungerar med flödesloggar version 1. Mer information finns i [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfilerna, utan ändringar.

Du kan ha många NSG:er i nätverket med flödesloggning aktiverat. Den här mängden loggningsdata gör det besvärligt att tolka och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa NSG-flödesloggar med Grafana, ett diagramverktyg med öppen källkod, ElasticSearch, en distribuerad sök- och analysmotor och Logstash, som är en databehandlingspipeline på öppen källkod.  

## <a name="scenario"></a>Scenario

NSG-flödesloggar är aktiverade med Network Watcher och lagras i Azure blob storage. En Logstash plugin används för att ansluta och bearbeta flödesloggar från blob lagring och skicka dem till ElasticSearch.  När flödesloggarna har lagrats i ElasticSearch kan de analyseras och visualiseras till anpassade instrumentpaneler i Grafana.

![NSG Nätverk Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera flödesloggning för nätverkssäkerhetsgruppen

I det här fallet måste du ha nätverkssäkerhetsgruppflödesloggning aktiverat på minst en nätverkssäkerhetsgrupp i ditt konto. Instruktioner om hur du aktiverar nätverkssäkerhetsflödesloggar finns i följande artikel [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Att tänka på vid konfiguration

I det här exemplet konfigureras Grafana, ElasticSearch och Logstash på en Ubuntu 16.04 LTS-server som distribueras i Azure. Den här minimala installationen används för att köra alla tre komponenterna – de körs alla på samma virtuella dator. Den här inställningen bör endast användas för testning och icke-kritiska arbetsbelastningar. Logstash, Elasticsearch och Grafana kan alla vara arkitekterade för att skala självständigt över många instanser. Mer information finns i dokumentationen för var och en av dessa komponenter.

### <a name="install-logstash"></a>Installera Logstash

Du använder Logstash för att förenkla JSON-formaterade flödesloggar till en flödesuppppelnivå.

1. Om du vill installera Logstash kör du följande kommandon:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurera Logstash för att tolka flödesloggarna och skicka dem till ElasticSearch. Skapa en Logstash.conf-fil med:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Lägg till följande innehåll i filen. Ändra namn och åtkomstnyckel för lagringskonto för att återspegla dina lagringskontouppgifter:

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

Den Logstash config-fil som tillhandahålls består av tre delar: indata, filter och utdata.
Indataavsnittet anger indatakällan för loggarna som Logstash kommer att behandla - i det här fallet kommer vi att använda en "azureblob" indata plugin (installeras i nästa steg) som gör det möjligt för oss att komma åt NSG flödeslogg JSON filer som lagras i blob lagring. 

Filteravsnittet plattar sedan till varje flödesloggfil så att varje enskild flödesuppppel och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar utdataavsnittet varje Logstash-händelse till ElasticSearch-servern. Känn dig fri att ändra Logstash config filen för att passa dina specifika behov.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installera indataindataindataindataindatafilen logstash för Azure Blob-lagring

Med den här Logstash-insticksprogrammet kan du direkt komma åt flödesloggarna från deras angivna bloblagringskonto. Om du vill installera den här kontakten kör kommandot från standardinstallationskatalogen för Logstash (i det här fallet /usr/share/logstash/bin):

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om den här plugin-kontakten finns i [Logstash indata plugin för Azure Storage Blobbar](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Installera ElasticSearch

Du kan använda följande skript för att installera ElasticSearch. Information om hur du installerar ElasticSearch finns i [Elastisk stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

Om du vill installera och köra Grafana kör du följande kommandon:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Mer information om installation finns i [Installera på Debian / Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Lägga till ElasticSearch-servern som en datakälla

Därefter måste du lägga till ElasticSearch-indexet som innehåller flödesloggar som en datakälla. Du kan lägga till en datakälla genom att välja **Lägg till datakälla** och fylla i formuläret med relevant information. Ett exempel på den här konfigurationen finns i följande skärmbild:

![Lägg till datakälla](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

Nu när du har konfigurerat Grafana för att läsa från ElasticSearch-indexet som innehåller NSG-flödesloggar kan du skapa och anpassa instrumentpaneler. Om du vill skapa en ny instrumentpanel väljer du **Skapa den första instrumentpanelen**. Följande exempeldiagramkonfiguration visar flöden som segmenterats efter NSG-regel:

![Diagram över instrumentpanelen](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Följande skärmbild visar ett diagram och diagram som visar de översta flödena och deras frekvens. Flöden visas också av NSG-regeln och flöden efter beslut. Grafana är mycket anpassningsbar så det är lämpligt att du skapar instrumentpaneler som passar dina specifika övervakningsbehov. I följande exempel visas en typisk instrumentpanel:

![Diagram över instrumentpanelen](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Slutsats

Genom att integrera Network Watcher med ElasticSearch och Grafana har du nu ett bekvämt och centraliserat sätt att hantera och visualisera NSG-flödesloggar samt andra data. Grafana har ett antal andra kraftfulla graffunktioner som också kan användas för att ytterligare hantera flödesloggar och bättre förstå din nätverkstrafik. Nu när du har konfigurerat och anslutit till Azure i Grafana kan du gärna fortsätta att utforska de andra funktioner som den erbjuder.

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du använder [Network Watcher](network-watcher-monitoring-overview.md).

