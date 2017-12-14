---
title: "Analysera Azure-nätverk grupp flödet säkerhetsloggar - Graylog | Microsoft Docs"
description: "Lär dig att hantera och analysera nätverket grupp flödet säkerhetsloggar i Azure med hjälp av Nätverksbevakaren och Graylog."
services: network-watcher
documentationcenter: na
author: mareat
manager: vitinnan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: 8d82ffa84c3d75ec3acd102a2de2bdce3718a995
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Hantera och analysera nätverket grupp flödet säkerhetsloggar i Azure med hjälp av Nätverksbevakaren och Graylog

[Nätverk säkerhetsloggar grupp flödet](network-watcher-nsg-flow-logging-overview.md) innehåller information som du kan använda för att förstå ingående och utgående IP-trafik för Azure nätverksgränssnitt. Flödet loggarna visar utgående och inkommande flöden på en per network security regeln grundval nätverksgränssnittet flödet gäller, till 5-tuppel information (källan/målet IP-källan/målet Port Protocol) om flödet, och om trafiken tillåts eller nekas .

Du kan ha många nätverkssäkerhetsgrupper i nätverket med flödet loggning aktiverad. Flera nätverkssäkerhetsgrupper med flödet loggning är aktiverat kan det vara krånglig att tolka och få insikter från dina loggar. Den här artikeln innehåller en lösning för att centralt hantera dessa nätverk grupp flödet säkerhetsloggar med Graylog, en öppen källkod hantering och analysverktyg och Logstash, en öppen källkod serversidan databearbetning pipeline.

## <a name="scenario"></a>Scenario

Nätverket säkerhetsloggar grupp flödet aktiveras med hjälp av Nätverksbevakaren. Flödet loggar flöde i till Azure blob storage. Ett Logstash plugin-program används för att ansluta och bearbeta flödet loggar från blob storage och skicka dem till Graylog. När loggarna flödet lagras i Graylog, de analyseras och visualiseras i anpassade instrumentpaneler.

! [Graylog arbetsflöde]] (. / media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installationssteg

### <a name="enable-network-security-group-flow-logging"></a>Aktivera nätverk säkerhetsloggning grupp flöde

I det här scenariot måste du ha nätverket grupp flödet säkerhetsloggning aktiverat på minst en säkerhetsgrupp i ditt konto. För instruktioner om hur du aktiverar nätverks säkerhetsloggar grupp flödet, finns i följande artikel [introduktion till flödet loggning för nätverkssäkerhetsgrupper](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Konfigurera Graylog

I det här exemplet konfigureras både Graylog och Logstash på en Ubuntu 14.04-Server distribueras i Azure.

- Referera till den [dokumentationen](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) Graylog, steg-för-steg-instruktioner för hur för att installera på Ubuntu.
- Se till att även konfigurera webbgränssnitt Graylog genom att följa den [dokumentationen](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Det här exemplet används inställningen för minsta Graylog (dvs en instans av en Graylog), men kan vara konstruerad Graylog om du vill skala över resurser beroende på systemet och produktion behov. Mer information om arkitekturen överväganden eller en djup arkitektur guide finns Graylog's [dokumentationen](http://docs.graylog.org/en/2.2/pages/architecture.html) och [arkitektur guiden](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog kan installeras på många olika sätt beroende på din plattform och inställningar. En fullständig lista över möjliga installationsmetoder avser Graylogs officiella [dokumentationen](http://docs.graylog.org/en/2.2/pages/installation.html). Graylog serverprogrammet körs på Linux-distributioner och har följande krav:

-   Oracle Java SE 8 eller senare – [Oracles dokumentationen](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Elastisk söka 2.x (2.1.0 eller senare)- [Elasticsearch dokumentationen](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   MongoDB 2.4 eller senare – [MongoDB-dokumentation för installation](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Installera Logstash

Logstash används för att förenkla JSON-formaterade flödet loggar till en tuppel flödet-nivå. Förenkla flödet loggarna underlättar loggarna att ordna och söka i Graylog.

1.  Om du vill installera Logstash, kör du följande kommandon:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  Konfigurera Logstash för att parsa flödet loggarna och skicka dem till Graylog. Skapa en Logstash.conf-fil:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  Lägg till följande innehåll i filen. Ändra de markerade värdena att visa information om ditt lagringskonto:

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
Konfigurationsfilen Logstash som består av tre delar: indata, filter och utdata. Avsnittet inkommande betecknar Indatakällan i loggar som bearbetar Logstash – i det här fallet vi ska använda en Azure blogg inkommande plugin-program (installerad i nästa steg) som gör att vi kan komma åt nätverket säkerhet grupp flödet logga JSON-filer som lagras i blob storage.

Avsnittet filter förenklar sedan varje flöde loggfil så att varje enskild flödet tuppel och dess associerade egenskaper blir en separat Logstash-händelse.

Slutligen vidarebefordrar utdata varje Logstash-händelsen till Graylog-servern. Så att de passar dina specifika behöver, ändra Logstash config-filen som krävs.

   > [!NOTE]
   > Tidigare konfigurationsfilen förutsätter att Graylog-servern har konfigurerats på den lokala loopback-IP-adressen 127.0.0.1. Om inte, se till att ändra värdparametern i utdataavsnittet till rätt IP-adress.

Ytterligare instruktioner om hur du installerar Logstash finns i Logstash [dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Installera plugin-program för Azure-blobblagring Logstash indata

Logstash plugin-programmet kan du få direkt åtkomst till flödet loggar från deras avsedda blob storage-konto. Installera plugin-programmet, från Standardinstallationskatalogen Logstash (i det här fallet /usr/share/logstash/bin) genom att köra följande kommando:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Mer information om den här plugin i finns i [dokumentationen](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Konfigurera anslutning från Logstash till Graylog

Nu när vi har upprättat en anslutning till flödet loggar med Logstash och konfigurera servern Graylog behöver konfigurera Graylog för att acceptera inkommande loggfilerna.

1.  Gå till din Graylog Server webbgränssnitt med hjälp av Webbadressen som du har konfigurerat för den. Du kan använda gränssnittet genom att styra webbläsaren`http://<graylog-server-ip>:9000/`

2.  Om du vill gå till sidan för konfiguration, markera den **System** listrutan i det övre navigeringsfältet till höger, och klicka sedan på **indata**.
    Du kan också gå till`http://<graylog-server-ip>:9000/system/inputs`

    ![Komma igång](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  Om du vill starta ny indata, Välj *GELF UDP* i den **Välj indata** listrutan, och sedan fylla i formuläret. GELF står för Graylog utökat loggfilsformat. Formatet GELF utvecklas av Graylog. Mer information om dess fördelar finns på Graylog [dokumentationen](http://docs.graylog.org/en/2.2/pages/gelf.html).

    Se till att binda indata till IP-Adressen som du har konfigurerat Graylog servern på. IP-adressen ska matcha den **värden** i konfigurationsfilen Logstash UDP-utdata. Standardporten ska vara *12201*. Kontrollera att porten som matchar den **port** fält i UDP utgående anges i konfigurationsfilen Logstash.

    ![Indata](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    När du startar indata bör du se den visas under den **lokala indata** avsnittet som visas i följande bild:

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    Om du vill veta mer om Graylog meddelande indata kan referera till den [dokumentationen](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4.  När de här konfigurationerna har gjorts kan du starta Logstash om du vill börja läsa i flödet loggar med följande kommando:

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Söka igenom Graylog meddelanden

Efter att en stund innan Graylog servern att samla in meddelanden kan du söka igenom meddelanden. Att kontrollera de meddelanden som skickas till servern Graylog från den **indata** konfigurationen klickar på ”**visa mottagna meddelanden**”-knappen i GELF UDP inkommande du skapade. Du dirigeras till en skärm som liknar bilden nedan: 

![Histogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Klicka på länken blå ”% {Message}” expanderar varje meddelande att visa parametrar för varje flöde tuppel som visas i följande bild:

![Meddelanden](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Som standard ingår alla meddelandefält i sökningen om du inte markerar ett visst meddelande-fält för att söka efter. Om du vill söka efter specifika meddelanden (dvs – flödet tupplar från en specifik käll-IP) du kan använda Graylog Sök frågespråket som [dokumenterade](http://docs.graylog.org/en/2.2/pages/queries.html)


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analysera nätverket grupp flödet säkerhetsloggar med Graylog

Nu när Graylog det ställts in körs, kan vi använda några av dess funktioner att bättre förstå våra flödet loggdata. Ett sådant sätt är att använda instrumentpaneler för att skapa specifika vyer av våra data.

### <a name="create-a-dashboard"></a>Skapa en instrumentpanel

1.  I det övre navigeringsfältet väljer **instrumentpaneler** eller navigera till`http://<graylog-server-ip>:9000/dashboards/`

2.  Därifrån klickar du på gröna **skapa instrumentpanelen** knappen och fylla i formuläret kort med rubriken och beskrivningen av din instrumentpanel. Tryck på **spara** för att skapa den nya instrumentpanelen. Du kan se en instrumentpanel som liknar bilden nedan:

    ![Instrumentpaneler](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Lägga till widgetar

Du kan klicka på rubriken på instrumentpanelen för att se den, men just nu är tom, eftersom vi inte har lagt till några widgetar. Ett enkelt och användbara widget att lägga till på instrumentpanelen är **snabb värden** diagram som visar en lista över värden för det markerade fältet och distribution.

1.  Gå tillbaka till sökresultat på UDP-indata som tar emot flödet loggar genom att välja **Sök** från det övre navigeringsfältet.

2.  Under den **sökresultatet** panelen på vänster sida av skärmen, Sök efter den **fält** fliken som listar olika fält för varje inkommande flödet tuppel meddelande.

3.  Välj önskade parametrar att visualisera (i det här exemplet vi har valt käll-IP). Om du vill visa listan över möjliga widgetar, klicka på den blå pilen till vänster om fältet och välj sedan **snabb värden** att generera widgeten. Du bör se något som liknar bilden nedan:

    ![Käll-IP-adress](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  Därifrån kan du välja den **lägga till instrumentpanelen** i det övre högra hörnet i widgeten och välj att lägga till motsvarande instrumentpanelen.

5.  Gå tillbaka till instrumentpanelen för att se widgeten som du just lagt till.

    Du kan lägga till en mängd andra widgetar som histogram och antal instrumentpanelen för att hålla reda på viktiga mätvärden, till exempel exempelinstrumentpanelen visas i följande bild:

    ![Flowlogs instrumentpanelen](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Ytterligare förklaring på instrumentpaneler och andra typer av widgetar finns i Graylog's [dokumentationen](http://docs.graylog.org/en/2.2/pages/dashboards.html).

Genom att integrera Nätverksbevakaren med Graylog har vi nu ett bekvämt och centraliserat sätt att hantera och visualisera nätverket säkerhetsloggar grupp flödet. Graylog har ett antal andra kraftfulla funktioner, till exempel dataströmmar och aviseringar som kan också användas för att hantera ytterligare flödet loggar och bättre förstå trafik på nätverket. Nu när du har Graylog ställs in och ansluten till Azure kan passa på att fortsätta att utforska de funktioner som den erbjuder.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du visualisera dina nätverk grupp flödet säkerhetsloggar med Power BI genom att besöka [visualisera nätverkssäkerhetsgruppen flödar loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
