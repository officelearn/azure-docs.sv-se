---
title: Utföra identifiering av nätverksintrång med verktyg för öppen källkod
titleSuffix: Azure Network Watcher
description: I den hÃ¤r artikeln beskrivs hur du an¶kar Azure Network Watcher och verktyg med öppen källkod för att utföra identifiering av nätverksintrång
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1bd823d94552d1e920b367b6576b0e3bb74aefb2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474921"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Utför identifiering av nätverksintrång med Network Watcher- och open source-verktyg

Paketinfångningar är en viktig komponent för att implementera system för identifiering av nätverksintrång (IDS) och utföra NSM (Network Security Monitoring). Det finns flera IDS-verktyg med öppen källkod som bearbetar paketinsamlingar och letar efter signaturer för möjliga nätverksintrång och skadlig aktivitet. Med hjälp av paketinfången som tillhandahålls av Network Watcher kan du analysera nätverket för skadliga intrång eller sårbarheter.

Ett sådant verktyg med öppen källkod är Suricata, en IDS-motor som använder regeluppsättningar för att övervaka nätverkstrafik och utlöser varningar när misstänkta händelser inträffar. Suricata erbjuder en flertrådad motor, vilket innebär att den kan utföra nätverkstrafikanalyser med ökad hastighet och effektivitet. För mer information om Suricata och dess https://suricata-ids.org/kapacitet, besök deras hemsida på .

## <a name="scenario"></a>Scenario

I den här artikeln beskrivs hur du konfigurerar din miljö så att den utför identifiering av nätverksintrång med Network Watcher, Suricata och Elastic Stack. Network Watcher ger dig paketinfångningar som används för att utföra identifiering av nätverksintrång. Suricata bearbetar paketinfångningar och utlösaviseringar baserat på paket som matchar dess givna regeluppsättning av hot. Dessa aviseringar lagras i en loggfil på din lokala dator. Med hjälp av den elastiska stacken kan loggarna som genereras av Suricata indexeras och användas för att skapa en Kibana-instrumentpanel, vilket ger dig en visuell representation av loggarna och ett sätt att snabbt få insikter om potentiella nätverkssårbarheter.  

![enkelt webbprogram scenario][1]

Båda verktygen med öppen källkod kan konfigureras på en virtuell Azure-dator, så att du kan utföra den här analysen i din egen Azure-nätverksmiljö.

## <a name="steps"></a>Steg

### <a name="install-suricata"></a>Installera Suricata

För alla andra installationsmetoder, besökhttps://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. I kommandoradsterminalen för den virtuella datorn körs följande kommandon:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Om du vill verifiera `suricata -h` installationen kör du kommandot för att se hela listan med kommandon.

### <a name="download-the-emerging-threats-ruleset"></a>Ladda ner regleruppsättningen Nya hot

I detta skede har vi inga regler för Suricata att köra. Du kan skapa egna regler om det finns specifika hot mot ditt nätverk som du vill identifiera, eller så kan du också använda utvecklade regeluppsättningar från ett antal leverantörer, till exempel Nya hot eller VRT-regler från Snort. Vi använder det fritt tillgängliga Emerging Threats-regeluppsättningen här:

Hämta regeluppsättningen och kopiera dem till katalogen:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Bearbeta paketfångningar med Suricata

Om du vill bearbeta paketinsamlingar med Suricata kör du följande kommando:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Om du vill kontrollera de resulterande aviseringarna läser du filen fast.log:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Ställ in den elastiska stacken

Medan loggarna som Suricata producerar innehåller värdefull information om vad som händer i vårt nätverk, är dessa loggfiler inte de lättaste att läsa och förstå. Genom att ansluta Suricata till Elastic Stack kan vi skapa en Kibana-instrumentpanel som gör det möjligt för oss att söka, grafera, analysera och härleda insikter från våra loggar.

#### <a name="install-elasticsearch"></a>Installera Elasticsearch

1. Den elastiska stacken från version 5.0 och högre kräver Java 8. Kör kommandot `java -version` för att kontrollera din version. Om du inte har java installerat läser du dokumentation om [Azure-suppored JDK: er](https://aka.ms/azure-jdks).

1. Ladda ner rätt binära paket för ditt system:

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

För ytterligare instruktioner om hur du installerar Elastisk sökning, se sidan [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Installera Logstash

1. Så här installerar du Logstash och kör följande kommandon:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Nästa måste vi konfigurera Logstash att läsa från produktionen av eve.json fil. Skapa en logstash.conf-fil med:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Lägg till följande innehåll i filen (kontrollera att sökvägen till eve.json-filen är korrekt):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Se till att ge rätt behörighet till eve.json-filen så att Logstash kan inta filen.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Så här startar du Kommandot Logstash:

    ```
    sudo /etc/init.d/logstash start
    ```

För ytterligare instruktioner om hur du installerar Logstash, se den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Installera Kibana

1. Kör följande kommandon för att installera Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Så här kör du Kibana med kommandona:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Om du vill visa ditt Kibana-webbgränssnitt navigerar du till`http://localhost:5601`
1. I det här scenariot är indexmönstret som används för Suricata-loggarna "logstash-*"

1. Om du vill visa Kibana-instrumentpanelen på distans skapar du en inkommande NSG-regel som tillåter åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en Kibana-instrumentpanel

I den här artikeln har vi tillhandahållit en exempelinstrumentpanel där du kan visa trender och information i dina aviseringar.

1. Ladda ner instrumentpanelsfilen [här](https://aka.ms/networkwatchersuricatadashboard), visualiseringsfilen [här](https://aka.ms/networkwatchersuricatavisualization)och den sparade sökfilen [här](https://aka.ms/networkwatchersuricatasavedsearch).

1. Under fliken **Hantering** i Kibana navigerar du till **Sparade objekt** och importerar alla tre filerna. Sedan kan du öppna och läsa in exempelinstrumentpanelen från fliken **Instrumentpanel.**

Du kan också skapa egna visualiseringar och instrumentpaneler som är skräddarsydda för mätvärden av ditt eget intresse. Läs mer om hur du skapar Kibana-visualiseringar från Kibanas [officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![kibana instrumentpanel][2]

### <a name="visualize-ids-alert-logs"></a>Visualisera IDS-varningsloggar

Exempelinstrumentpanelen innehåller flera visualiseringar av Suricata-varningsloggarna:

1. Varningar från GeoIP – en karta som visar fördelningen av varningar efter ursprungsland/ursprungsregion baserat på geografisk plats (bestäms av IP)

    ![geo ip][3]

1. Topp 10-aviseringar – en sammanfattning av de 10 mest frekventa utlösta aviseringarna och deras beskrivning. Om du klickar på en enskild avisering filtreras instrumentpanelen ned till informationen om den specifika aviseringen.

    ![bild 4][4]

1. Antal aviseringar – det totala antalet aviseringar som utlöses av regeluppsättningen

    ![bild 5][5]

1. Topp 20 Source/Destination IP/Ports - cirkeldiagram som visar de 20 ip-adresser och portar som aviseringar har utlösts. Du kan filtrera nedåt på specifika IPs/portar för att se hur många och vilken typ av aviseringar som utlöses.

    ![bild 6][6]

1. Varningssammanfattning – en tabell som sammanfattar specifika detaljer för varje enskild avisering. Du kan anpassa den här tabellen för att visa andra parametrar av intresse för varje avisering.

    ![bild 7][7]

Mer information om hur du skapar anpassade visualiseringar och instrumentpaneler finns i [Kibanas officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Slutsats

Genom att kombinera paketinsamlingar som tillhandahålls av Network Watcher och IDS-verktyg med öppen källkod, till exempel Suricata, kan du utföra identifiering av nätverksintrång för en mängd olika hot. Med de här instrumentpanelerna kan du snabbt hitta trender och avvikelser i nätverket, samt gräva i data för att identifiera orsaken till aviseringar som skadliga användaragenter eller sårbara portar. Med den här extraherade data kan du fatta välgrundade beslut om hur du ska reagera på och skydda nätverket från skadliga intrångsförsök och skapa regler för att förhindra framtida intrång i nätverket.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du utlöser paketinsamlingar baserat på aviseringar genom att besöka [Använd paketfångst för att göra proaktiv nätverksövervakning med Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Lär dig hur du visualiserar dina NSG-flödesloggar med Power BI genom att besöka [Visualize NSG-flöden loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
