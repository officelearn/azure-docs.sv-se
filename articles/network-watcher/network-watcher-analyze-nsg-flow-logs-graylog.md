---
title: Analysera Azure-nätverk flödesloggar för nätverkssäkerhetsgruppen - Graylog | Microsoft Docs
description: Lär dig mer om att hantera och analysera flödesloggar för nätverkssäkerhetsgrupper i Azure med Network Watcher och Graylog.
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
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: a5fadcfce154740a79a8764f44f08b21ad18f4d8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57879947"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Hantera och analysera flödesloggar för nätverkssäkerhetsgrupper i Azure med Network Watcher och Graylog

[Network flödesloggar för nätverkssäkerhetsgruppen](network-watcher-nsg-flow-logging-overview.md) innehåller information som du kan använda för att förstå ingående och utgående IP-trafik för Azure-nätverksgränssnitt. Flödesloggar Visa utgående och inkommande flöden på en per network security group detta, nätverksgränssnittet flödet gäller att information 5-tuppel (käll-och mål-IP, källa/mål-Port, Protocol) om flödet, och om trafik tillåts eller nekas .

Du kan ha många nätverkssäkerhetsgrupper i nätverket med flow-loggning är aktiverat. Flera nätverkssäkerhetsgrupper med flow-loggning är aktiverat kan göra det besvärligt att tolka och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa nätverk flödesloggar för nätverkssäkerhetsgruppen med Graylog, en öppen källkod logghantering och analysverktyg och Logstash, en öppen källkod från serversidan databearbetningspipeline.

> [!Warning]
> Följande steg fungerar med flow loggar version 1. Mer information finns i [introduktion till flödesloggar för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md). Följande instruktioner fungerar inte med version 2 av loggfiler, utan modifiering.

## <a name="scenario"></a>Scenario

Loggar för nätverkssäkerhetsgrupper flow aktiveras med hjälp av Network Watcher. Flödet loggar flödet i till Azure blob storage. En Logstash plugin-programmet används för att ansluta och bearbeta flödesloggar från blob storage och skicka dem till Graylog. När flödesloggar lagras i Graylog, kan de analyseras och visualiseras med anpassade instrumentpaneler.

![Arbetsflöde för Graylog](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera network flödesloggar för nätverkssäkerhetsgrupper

I det här scenariot måste du ha nätverk flödesloggar för nätverkssäkerhetsgrupper aktiverade i minst en nätverkssäkerhetsgrupp i ditt konto. Instruktioner om hur du aktiverar network flödesloggar för nätverkssäkerhetsgruppen, finns i följande artikel [introduktion till flödesloggar för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurera Graylog

I det här exemplet konfigureras både Graylog och Logstash på en Ubuntu 14.04-Server distribueras i Azure.

- Referera till den [dokumentation](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) från Graylog, stegvisa anvisningar för hur installera på Ubuntu.
- Se till att även konfigurera Graylog webbgränssnittet genom att följa den [dokumentation](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Det här exemplet används inställningar som minimum Graylog (dvs.) en enda instans av en Graylog), men Graylog kan vara byggts om du vill skala över resurser beroende på dina system och produktion behov. Mer information om arkitekturen överväganden eller en djupgående arkitektoniska guide finns i Graylog's [dokumentation](https://docs.graylog.org/en/2.2/pages/architecture.html) och [arkitektoniska guiden](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog kan installeras på många sätt, beroende på plattform och inställningar. En fullständig lista över möjliga installationsmetoder finns Graylogs officiella [dokumentation](https://docs.graylog.org/en/2.2/pages/installation.html). Graylog serverprogrammet körs på Linux-distributioner och har följande krav:

-  Java SE 8 eller senare – [Azul Azure JDK-dokumentation](https://aka.ms/azure-jdks)
-  Elastisk Sök 2.x (2.1.0 eller senare)- [Elasticsearch installationsdokumentationen](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 eller senare – [dokumentationen för installation av MongoDB](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Installera Logstash

Logstash används för att platta ut JSON-formaterade flödesloggar till en flow-tuppel-nivå. Förenkla flödesloggar blir loggarna lättare att ordna och söka i Graylog.

1. För att installera Logstash, kör du följande kommandon:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurera Logstash för att parsa flödesloggar och skicka dem till Graylog. Skapa en Logstash.conf-fil:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Lägg till följande innehåll i filen. Ändra de markerade värdena för att återspegla din kontoinformation för lagring:

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
   Logstash config-fil som består av tre delar: indata, filter och utdata. Avsnittet indata anger Indatakällan för loggarna som bearbetar Logstash – i det här fallet du ska använda en Azure-bloggen inkommande plugin-program (installerad i nästa steg) som gör att vi kan komma åt network security group flödet logga JSON-filer som lagras i blob storage.

Avsnittet filter förenklar sedan loggfilerna flöde så att varje tuppel enskilda flödet och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar utdata varje Logstash-händelse till Graylog-servern. Så att den passar dina specifika behöver, ändra Logstash config-filen efter behov.

   > [!NOTE]
   > Föregående konfigurationsfilen förutsätter att Graylog servern har konfigurerats på den lokala värd loopback-IP-adressen 127.0.0.1. Annars kan du se till att ändra värdparametern i utdataavsnittet till rätt IP-adress.

Ytterligare instruktioner om hur du installerar Logstash finns i Logstash [dokumentation](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Installera plugin-programmet för Azure blob storage Logstash indata

Logstash plugin-programmet kan du direkt åtkomst till flow-loggar från deras avsedda blob storage-konto. Om du vill installera plugin-programmet, från Logstash standardkatalogen (i det här fallet /usr/share/logstash/bin), kör du följande kommando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om det här plugin-programmet finns i den [dokumentation](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Konfigurera anslutning från Logstash till Graylog

Nu när du har upprättat en anslutning till de flödesloggar med Logstash och ställa in Graylog-server, måste du konfigurera Graylog för att acceptera inkommande loggfilerna.

1. Gå till din Graylog Server webbgränssnitt med hjälp av Webbadressen som du konfigurerat för den. Du kan använda gränssnittet genom att dirigera webbläsaren till `http://<graylog-server-ip>:9000/`

2. Gå till sidan för konfiguration, välja den **System** listrutan i det övre navigeringsfältet till höger, och klicka sedan på **indata**.
   Du kan också gå till `http://<graylog-server-ip>:9000/system/inputs`

   ![Komma igång](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Om du vill starta den nya indata, Välj *GELF UDP* i den **Välj indata** listrutan och sedan fylla i formuläret. GELF står för Graylog utökade loggformat. Formatet GELF utvecklas av Graylog. Mer information om dess fördelar finns Graylog [dokumentation](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Se till att binda IP-Adressen som du har konfigurerat Graylog servern på indata. IP-adressen ska matcha den **värden** fältet på UDP-utdata från Logstash-konfigurationsfilen. Standardporten ska vara *12201*. Kontrollera att porten som matchar den **port** fält i UDP utgående anges i Logstash config-fil.

   ![Indata](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   När du startar indata, bör du se den den **lokala indata** avsnittet som visas i följande bild:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Om du vill veta mer om Graylog meddelande indata kan referera till den [dokumentation](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. När de här konfigurationerna har gjorts, du kan starta Logstash för att börja läsa i flödesloggar med följande kommando: `sudo systemctl start logstash.service`.

### <a name="search-through-graylog-messages"></a>Söka igenom Graylog meddelanden

Efter att en stund för Graylog servern för att samla in meddelanden, kan du söka igenom meddelanden. Kontrollera meddelandena som skickas till servern för Graylog från den **indata** konfigurationen klickar du på den ”**Show fått meddelanden**” knappen av GELF UDP inkommande du skapade. Du dirigeras till en skärm som liknar följande bild: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Klicka på länken blå ”% {Message}” expanderar varje meddelande som ska visas parametrarna för varje flöde tuppel enligt följande bild:

![Meddelanden](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Som standard ingår alla meddelandefält i sökningen om du inte markerar ett visst meddelande-fält att söka efter. Om du vill söka efter specifika meddelanden (dvs.) – flow tupplar från en specifik käll-IP) du kan använda Graylog search frågespråket som [dokumenterade](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analysera flödesloggar för nätverkssäkerhetsgrupper med Graylog

Nu när Graylog den konfigurera körs, kan du använda några av dess funktioner att bättre förstå din flow-loggdata. Ett sådant sätt är att använda instrumentpaneler för att skapa vissa vyer över dina data.

### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

1. I det övre navigeringsfältet väljer **instrumentpaneler** eller navigera till `http://<graylog-server-ip>:9000/dashboards/`

2. Därifrån klickar du på gröna **skapa instrumentpanelen** knappen och Fyll i formuläret kort med rubriken och beskrivningen på instrumentpanelen. Tryck på den **spara** för att skapa den nya instrumentpanelen. Du kan se en instrumentpanel som liknar följande bild:

    ![Instrumentpaneler](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Lägga till widgetar

Du kan klicka på rubriken på instrumentpanelen för att se hur det, men just nu det är tom, eftersom vi inte har lagt till alla widgetar. Ett enkelt och praktiskt-widgeten för att lägga till på instrumentpanelen är **snabb värden** diagram som visar en lista med värden för det markerade fältet och deras distribution.

1. Gå tillbaka till sökresultaten av UDP-indata som tar emot flödesloggar genom att välja **Search** från det övre navigeringsfältet.

2. Under den **sökresultat** panelen till vänster sida av skärmen, hitta den **fält** fliken som visar olika fält för varje inkommande meddelande i flow tuppel.

3. Välj alla önskade parametern där du vill visualisera (i det här exemplet är IP-källan är markerad). För att visa listan över möjliga widgetar, klickar du på den blå pilen till vänster om fältet och välj sedan **snabb värden** att generera widgeten. Du bör se något som liknar följande bild:

   ![Käll-IP-adress](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Därifrån kan du välja den **lägga till instrumentpanelen** i det övre högra hörnet av widgeten och välj att lägga till motsvarande instrumentpanelen.

5. Gå tillbaka till instrumentpanelen för att visa en widget som du just lade till.

   Du kan lägga till en mängd andra widgetar som histogram och antalet i din instrumentpanel och Håll koll på viktiga mätvärden, till exempel på instrumentpanelen för exempel som visas i följande bild:

   ![Flowlogs instrumentpanel](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Ytterligare förklaring på instrumentpaneler och andra typer av widgetar finns i Graylog's [dokumentation](https://docs.graylog.org/en/2.2/pages/dashboards.html).

Genom att integrera Network Watcher med Graylog, nu har du ett praktiskt och centraliserad sätt att hantera och visualisera flödesloggar för nätverkssäkerhetsgruppen nätverk. Graylog har ett antal andra kraftfulla funktioner, till exempel strömmar och aviseringar som kan också användas för att hantera ytterligare flödesloggar och bättre förstå din nätverkstrafik. Nu när du har Graylog Ställ in och anslutit till Azure, passa på att fortsätta att utforska andra funktioner som den erbjuder.

## <a name="next-steps"></a>Nästa steg

Lär dig att visualisera dina flödesloggar för nätverkssäkerhetsgrupper med Power BI genom att besöka [nätverkssäkerhetsgrupp kopplad till visualisera flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
