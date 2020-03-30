---
title: Analysera flödesloggar för Azure-nätverkssäkerhetsgrupper – Graylog | Microsoft-dokument
description: Lär dig hur du hanterar och analyserar nätverkssäkerhetsgruppflödesloggar i Azure med Network Watcher och Graylog.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 1e597a81967a8fb6be2959d53e65ad01135e5e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842911"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Hantera och analysera nätverkssäkerhetsgruppflödesloggar i Azure med Network Watcher och Graylog

[Nätverkssäkerhetsgruppflödesloggar](network-watcher-nsg-flow-logging-overview.md) innehåller information som du kan använda för att förstå inkommande och utgående IP-trafik för Azure-nätverksgränssnitt. Flödesloggar visar utgående och inkommande flöden per nätverkssäkerhetsgruppregelbas, nätverksgränssnittet som flödet gäller för, 5-tuppelinformation (Källa/mål-IP, Källa/Målport, Protokoll) om flödet och om trafiken tilläts eller nekades.

Du kan ha många nätverkssäkerhetsgrupper i nätverket med flödesloggning aktiverat. Flera nätverkssäkerhetsgrupper med flödesloggning aktiverat kan göra det besvärligt att tolka och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa nätverkssäkerhetsgruppflödesloggar med Graylog, ett logghanterings- och analysverktyg med öppen källkod och Logstash, en databearbetningspipeline på öppen källkod.

> [!Warning]
> Följande steg fungerar med flödesloggar version 1. Mer information finns i [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfilerna, utan ändringar.

## <a name="scenario"></a>Scenario

Nätverkssäkerhetsgruppsflödesloggar är aktiverade med Network Watcher. Flödesloggar flödar in till Azure blob storage. En Logstash plugin används för att ansluta och bearbeta flödesloggar från blob lagring och skicka dem till Graylog. När flödesloggarna har lagrats i Graylog kan de analyseras och visualiseras till anpassade instrumentpaneler.

![Gråloggarbetsflöde](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera nätverkssäkerhetsgruppsflödesloggning

I det här fallet måste du ha nätverkssäkerhetsgruppflödesloggning aktiverat på minst en nätverkssäkerhetsgrupp i ditt konto. Instruktioner om hur du aktiverar flödesloggar för nätverkssäkerhetsgrupper finns i följande artikel [Introduktion till flödesloggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Ställa in grålogg

I det här exemplet är både Graylog och Logstash konfigurerade på en Ubuntu 14.04 Server som distribueras i Azure.

- Se [dokumentationen](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) från Graylog, för steg för steg instruktioner om hur installera på Ubuntu.
- Se till att även konfigurera Graylog-webbgränssnittet genom att följa [dokumentationen](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

I det här exemplet används den lägsta Graylog-inställningen (dvs. en enda instans av en Graylog), men Graylog kan utformas för att skala över resurser beroende på ditt system och produktionsbehov. Mer information om arkitektoniska överväganden eller en djup arkitektonisk guide finns i Graylogs [dokumentation](https://docs.graylog.org/en/2.2/pages/architecture.html) och [arkitekturguide](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog kan installeras på många sätt, beroende på din plattform och dina preferenser. En fullständig lista över möjliga installationsmetoder finns i Graylogs officiella [dokumentation](https://docs.graylog.org/en/2.2/pages/installation.html). Graylog-serverprogrammet körs på Linux-distributioner och har följande förutsättningar:

-  Java SE 8 eller senare – [Azul Azure JDK-dokumentation](https://aka.ms/azure-jdks)
-  Elastisk sökning 2.x (2.1.0 eller senare) - [Dokumentation för Elasticsearch-installation](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2,4 eller senare – [MongoDB installationsdokumentation](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Installera Logstash

Logstash används för att förenkla JSON-formaterade flödesloggar till en flödestuppdeppelnivå. Om du förenklar flödesloggarna blir det lättare att ordna och söka i Graylog.

1. Om du vill installera Logstash kör du följande kommandon:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurera Logstash för att tolka flödesloggarna och skicka dem till Graylog. Skapa en Logstash.conf-fil:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Lägg till följande innehåll i filen. Ändra de markerade värdena så att de återspeglar dina lagringskontouppgifter:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Den Logstash config-fil som tillhandahålls består av tre delar: indata, filter och utdata. Indataavsnittet anger indatakällan för loggarna som Logstash kommer att bearbeta – i det här fallet kommer du att använda en Azure-blogginmatningsinputsprogram (installerad i nästa steg) som gör att vi kan komma åt nätverkssäkerhetsgruppens flödeslogg JSON-filer som lagras i blob-lagring.

Filteravsnittet plattar sedan till varje flödesloggfil så att varje enskild flödesuppppel och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar utdataavsnittet varje Logstash-händelse till Graylog-servern. Ändra filen Logstash config efter behov för att passa dina specifika behov.

   > [!NOTE]
   > Den tidigare config-filen förutsätter att Graylog-servern har konfigurerats på den lokala värdloopback IP-adressen 127.0.0.1. Om inte, se till att ändra värdparametern i utdataavsnittet till rätt IP-adress.

Mer information om hur du installerar Logstash finns i [dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)till Logstash .

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Installera plugin-programmet Logstash-indata för Azure-bloblagring

Den Logstash plugin kan du direkt komma åt flödet loggar från deras utsedda blob lagringskonto. Om du vill installera plugin-programmet kör du följande kommando från standardinstallationskatalogen för Logstash (i det här fallet /usr/share/logstash/bin):

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om den här kontakten finns i [dokumentationen](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Konfigurera anslutning från Logstash till Graylog

Nu när du har upprättat en anslutning till flödesloggarna med Logstash och konfigurerar Graylog-servern måste du konfigurera Graylog så att de inkommande loggfilerna accepteras.

1. Navigera till webbgränssnittet i Graylog Server med den URL som du har konfigurerat för det. Du kan komma åt gränssnittet genom att styra din webbläsare till`http://<graylog-server-ip>:9000/`

2. Om du vill navigera till konfigurationssidan väljer du den **nedrullningsbara** menyn System i det övre navigeringsfältet till höger och klickar sedan på **Ingångar**.
   Alternativt kan du navigera till`http://<graylog-server-ip>:9000/system/inputs`

   ![Komma igång](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Om du vill starta den nya indata väljer du *GELF UDP* i listrutan **Välj indata** och fyller sedan i formuläret. GELF står för Graylog Extended Log Format. GELF-formatet är utvecklat av Graylog. Mer information om dess fördelar finns i [Graylog-dokumentationen](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Se till att binda indata till IP du konfigurerade din Graylog-server på. IP-adressen ska matcha **värdfältet** för UDP-utdata för logstash-konfigurationsfilen. Standardporten ska vara *12201*. Kontrollera att porten matchar **portfältet** i UDP-utdata som anges i Logstash-config-filen.

   ![Indata](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   När du har startat indata bör du se den visas under avsnittet **Lokala indata,** som visas i följande bild:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Mer information om Graylog-meddelandeindata finns i [dokumentationen](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. När dessa konfigurationer har gjorts kan du starta Logstash för att börja `sudo systemctl start logstash.service`läsa i flödesloggar med följande kommando: .

### <a name="search-through-graylog-messages"></a>Sök igenom Graylog-meddelanden

När du har tillåtit lite tid för din Graylog-server att samla in meddelanden kan du söka igenom meddelandena. Om du vill kontrollera de meddelanden som skickas till Graylog-servern klickar du på knappen "**Visa mottagna meddelanden**" på gelf UDP-indata som du skapade från **konfigurationssidan för indata.** Du dirigeras till en skärm som liknar följande bild: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Om du klickar på den blå länken %{Message}utökas varje meddelande för att visa parametrarna för varje flödesuppppel, som visas i följande bild:

![Meddelanden](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Som standard inkluderas alla meddelandefält i sökningen om du inte väljer ett specifikt meddelandefält att söka efter. Om du vill söka efter specifika meddelanden (dvs. – flödesupptuppningar från en viss käll-IP) kan du använda graylog-sökfrågespråket som [dokumenterat](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analysera flödesloggar för nätverkssäkerhetsgrupper med Graylog

Nu när Graylog den konfigureras körs kan du använda vissa av dess funktioner för att bättre förstå dina flödesloggdata. Ett sådant sätt är att använda instrumentpaneler för att skapa specifika vyer av dina data.

### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

1. I det övre navigeringsfältet väljer du **Instrumentpaneler** eller navigerar till`http://<graylog-server-ip>:9000/dashboards/`

2. Därifrån klickar du på den gröna knappen **Skapa instrumentpanel** och fyller i det korta formuläret med rubrik och beskrivning av instrumentpanelen. Tryck på **knappen Spara** för att skapa den nya instrumentpanelen. Du ser en instrumentpanel som liknar följande bild:

    ![Instrumentpaneler](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Lägga till widgetar

Du kan klicka på titeln på instrumentpanelen för att se den, men just nu är den tom, eftersom vi inte har lagt till några widgets. En enkel och användbar textwidget som ska läggas till instrumentpanelen är **snabbvärdens** diagram, som visar en lista med värden för det markerade fältet och deras fördelning.

1. Navigera tillbaka till sökresultaten för UDP-indata som tar emot flödesloggar genom att välja **Sök** i det övre navigeringsfältet.

2. Leta reda på fliken **Fält** på panelen **Sökresultat** till vänster på skärmen, där de olika fälten i varje meddelande om inkommande flödesuttåde.

3. Välj en önskad parameter där du vill visualisera (i det här exemplet är IP-källan markerad). Om du vill visa listan över möjliga widgetar klickar du på den blå nedrullningsbara pilen till vänster om fältet och väljer sedan **Snabbvärden** för att generera widgeten. Du bör se något som liknar följande bild:

   ![Käll-IP-adress](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Därifrån kan du välja knappen **Lägg till instrumentpanelen** längst upp till höger i widgeten och välja motsvarande instrumentpanel att lägga till.

5. Navigera tillbaka till instrumentpanelen för att se den widget du just lagt till.

   Du kan lägga till en mängd andra widgetar, till exempel histogram och räknar på instrumentpanelen för att hålla reda på viktiga mått, till exempel exempel exempel instrumentpanelen som visas i följande bild:

   ![Instrumentpanelen Flödesloggar](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Mer förklaring om instrumentpaneler och andra typer av widgetar finns i Graylogs [dokumentation](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Genom att integrera Network Watcher med Graylog har du nu ett bekvämt och centraliserat sätt att hantera och visualisera flödesloggar för nätverkssäkerhetsgrupper. Graylog har ett antal andra kraftfulla funktioner, till exempel strömmar och aviseringar som också kan användas för att ytterligare hantera flödesloggar och bättre förstå nätverkstrafiken. Nu när du har Graylog konfigurerat och anslutet till Azure kan du fortsätta att utforska de andra funktioner som erbjuds.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualiserar flödesloggarna för nätverkssäkerhetsgruppen med Power BI genom att besöka [Visualize network security group flows loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
